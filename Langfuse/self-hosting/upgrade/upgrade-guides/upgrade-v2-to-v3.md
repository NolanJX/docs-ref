---
title: Migrate Langfuse v2 to v3 (self-hosted)
description: A guide to upgrade a Langfuse v2 setup to v3.
---

# Migrate Langfuse v2 to v3

This is a big upgrade and we tried to make it as seamless as possible. Please create a [GitHub Issue](/issues) or contact [support](/support) in case you have any questions while upgrading to v3.

Langfuse v3 (released on Dec. 6th, 2024) introduces a new backend architecture that unlocks many new features and performance improvements.

Follow this guide to:

1. Understand the architectural changes and reasoning behind them.
2. Learn about the other breaking changes.
3. Follow the upgrade steps to successfully migrate to Langfuse v3.

## Architecture Changes

This section dives into the reasoning behind the architectural changes we made for Langfuse v3.
To learn more about the architecture of Langfuse v3, jump to the [architecture overview](/self-hosting#architecture-overview).

Langfuse has gained significant traction over the last months, both in our Cloud environment and in self-hosted setups.
With Langfuse v3 we introduce changes that allow our backend to handle hundreds of events per second with higher reliability.
To achieve this scale, we introduce a second Langfuse container and additional storage services like S3/Blob store, Clickhouse, and Redis which are better suited for the required workloads than our previous Postgres-based setup.

In short, Langfuse v3 adds:

- A new worker container that processes events asynchronously.
- A new S3/Blob store for storing large objects.
- A new Clickhouse instance for storing traces, observations, and scores.
- Redis/Valkey for queuing events and caching data.

### Comparison of the architectures

<Tabs items={["Langfuse v3", "Langfuse v2"]}>
<Tab>

Architecture Diagram

```mermaid
flowchart TB
    User["UI, API, SDKs"]
    subgraph vpc["VPC"]
        Web["Web Server<br/>(langfuse/langfuse)"]
        Worker["Async Worker<br/>(langfuse/worker)"]
        Postgres@{ img: "/images/logos/postgres_icon.svg", label: "Postgres - OLTP\n(Transactional Data)", pos: "b", w: 60, h: 60, constraint: "on" }
        Cache@{ img: "/images/logos/redis_icon.png", label: "Redis\n(Cache, Queue)", pos: "b", w: 60, h: 60, constraint: "on" }
        Clickhouse@{ img: "/images/logos/clickhouse_icon.svg", label: "Clickhouse - OLAP\n(Observability Data)", pos: "b", w: 60, h: 60, constraint: "on" }
        S3@{ img: "/images/logos/s3_icon.svg", label: "S3 / Blob Storage\n(Raw events, multi-modal attachments)", pos: "b", w: 60, h: 60, constraint: "on" }
    end
    LLM["LLM API/Gateway<br/>(optional; BYO; can be same VPC or VPC-peered)"]

    User --> Web
    Web --> S3
    Web --> Postgres
    Web --> Cache
    Web --> Clickhouse
    Web -..->|"optional for playground"| LLM

    Cache --> Worker
    Worker --> Clickhouse
    Worker --> Postgres
    Worker --> S3
    Worker -..->|"optional for evals"| LLM
```

Langfuse consists of two application containers, storage components, and an optional LLM API/Gateway.

- [**Application Containers**](/self-hosting/deployment/infrastructure/containers)
  - Langfuse Web: The main web application serving the Langfuse UI and APIs.
  - Langfuse Worker: A worker that asynchronously processes events.
- **Storage Components**:
  - [Postgres](/self-hosting/deployment/infrastructure/postgres): The main database for transactional workloads.
  - [Clickhouse](/self-hosting/deployment/infrastructure/clickhouse): High-performance OLAP database which stores traces, observations, and scores.
  - [Redis/Valkey cache](/self-hosting/deployment/infrastructure/cache): A fast in-memory data structure store. Used for queue and cache operations.
  - [S3/Blob Store](/self-hosting/deployment/infrastructure/blobstorage): Object storage to persist all incoming events, multi-modal inputs, and large exports.
- [**LLM API / Gateway**](/self-hosting/deployment/infrastructure/llm-api): Some features depend on an external LLM API or gateway.

Langfuse can be deployed within a VPC or on-premises in high-security environments.
Internet access is optional.
See [networking](/self-hosting/security/networking) documentation for more details.

</Tab>

<Tab>

Architecture Diagram

```mermaid
flowchart TB
    User["UI, API, SDKs"]
    subgraph vpc["VPC"]
        Web["Web Server (langfuse/langfuse)"]
        Postgres@{ img: "/images/logos/postgres_icon.svg", label: "Postgres Database", pos: "b", w: 60, h: 60, constraint: "on" }
    end
    LLM["LLM API/Gateway<br/>(optional; BYO; can be same VPC or VPC-peered)"]

    User --> Web
    Web --> Postgres
    Web -.->|"optional for playground"| LLM
```

</Tab>
</Tabs>

### Reasoning for the architectural changes [#reasoning]

Learn more about the v2 to v3 evolution and architectural decisions in our [technical blog post](/blog/2024-12-langfuse-v3-infrastructure-evolution).

<details>
<summary>1. Why Clickhouse</summary>

We made the strategic decision to migrate our traces, observations, and scores table from Postgres to Clickhouse.
Both us and our self-hosters observed bottlenecks in Postgres when dealing with millions of rows of tracing data,
both on ingestion and retrieval of information.
Our core requirement was a database that could handle massive volumes of trace and event data with exceptional query speed and efficiency
while also being available for free to self-hosters.

**Limitations of Postgres**

Initially, Postgres was an excellent choice due to its robustness, flexibility, and the extensive tooling available.
As our platform grew, we encountered performance bottlenecks with complex aggregations and time-series data.
The row-based storage model of PostgreSQL becomes increasingly inefficient when dealing with billions of rows of tracing data,
leading to slow query times and high resource consumption.

**Our requirements**

- Analytical queries: all queries for our dashboards (e.g. sum of LLM tokens consumed over time)
- Table queries: Finding tracing data based on filtering and ordering selected via tables in our UI.
- Select by ID: Quickly locating a specific trace by its ID.
- High write throughput while allowing for updates. Our tracing data can be updated from the SKDs. Hence, we need an option to update rows in the database.
- Self-hosting: We needed a database that is free to use for self-hosters, avoiding dependencies on specific cloud providers.
- Low operational effort: As a small team, we focus on building features for our users. We try to keep operational efforts as low as possible.

**Why Clickhouse is great**

- Optimized for Analytical Queries: ClickHouse is a modern OLAP database capable of ingesting data at high rates and querying it with low latency. It handles billions of rows efficiently.
- Rich feature-set: Clickhouse offers different Table Engines, Materialized views, different types of Indices, and many integrations which helps us to build fast and achieve low latency read queries.
- Our self-hosters can use the official Clickhouse Helm Charts and Docker Images for deploying in the cloud infrastructure of their choice.
- Clickhouse Cloud: Clickhouse Cloud is a database as a SaaS service which allows us to reduce operational efforts on our side.

When talking to other companies and looking at their code bases, we learned that Clickhouse is a popular choice these days for analytical workloads.
Many modern observability tools, such as [Signoz](https://signoz.io/) or [Posthog](https://posthog.com/), as well as established companies like [Cloudflare](https://blog.cloudflare.com/http-analytics-for-6m-requests-per-second-using-clickhouse/), use Clickhouse for their analytical workloads.

**Clickhouse vs. others**

We think there are many great OLAP databases out there and are sure that we could have chosen an alternative and would also succeed with it. However, here are some thoughts on alternatives:

- Druid: Unlike Druid's [modular architecture](https://posthog.com/blog/clickhouse-vs-druid), ClickHouse provides a more straightforward, unified instance approach. Hence, it is easier for teams to manage Clickhouse in production as there are fewer moving parts. This reduces the operational burden especially for our self-hosters.
- StarRocks: We think StarRocks is great but early. The vast amount of features in Clickhouse help us to remain flexible with our requirements while benefiting from the performance of an OLAP database.

**Building an adapter and support multiple databases**

We explored building a multi-database adapter to support Postgres for smaller self-hosted deployments.
After talking to engineers and reviewing some of PostHog's [Clickhouse implementation](https://github.com/PostHog/posthog),
we decided against this path due to its complexity and maintenance overhead.
This allows us to focus our resources on building user features instead.

</details>

<details>
<summary>2. Why Redis</summary>

We added a Redis instance to serve cache and queue use-cases within our stack.
With its open source license, broad native support my major cloud vendors, and ubiquity in the industry, Redis was a natural choice for us.

</details>

<details>
<summary>3. Why S3/Blob Store</summary>

Observability data for LLM application tends to contain large, semi-structured bodies of data to represent inputs and outputs.
We chose S3/Blob Store as a scalable, secure, and cost-effective solution to store these large objects.
It allows us to store all incoming events for further processing and acts as a native backup solution, as the full state
can be restored based on the events stored there.

</details>

<details>
<summary>4. Why Worker Container</summary>

When processing observability data for LLM applications, there are many CPU-heavy operations which block the main loop in our Node.js backend,
e.g. tokenization and other parsing of event bodies.
To achieve high availability and low latencies across client applications, we decided to move the heavy processing into an asynchronous worker container.
It accepts events from a Redis queue and ensures that they are eventually being upserted into Clickhouse.

</details>

## Other Breaking Changes

If you use Langfuse SDKs above version 2.0.0 (released Dec 2023), these changes will not affect you. The Langfuse Team has already upgraded Langfuse Cloud to v3 without any issues after helping a handful of teams (less than 1% of users) to upgrade the Langfuse SDKs.

### SDK Requirements

**SDK v1.x.x is no longer supported**. While we aim to keep our SDKs and APIs fully backwards compatible, we have to introduce backwards incompatible changes with our update to Langfuse Server v3. Certain APIs in SDK versions below version 2.0.0 are not compatible with our new backend architecture.

#### Release dates of SDK v2

- Langfuse Python SDK v2 was [released](https://github.com/langfuse/langfuse-python/releases/tag/v2.0.1) on Dec 17, 2023,
- Langfuse JavaScript SDK v2 was [released](https://github.com/langfuse/langfuse-js/releases/tag/v2.0.0) on Dec 18, 2023.

#### Upgrade options if you are on SDK version 1.x.x

- Default SDK upgrade: Follow the 1.x.x to 2.x.x upgrade path ([Python](/docs/sdk/python/low-level-sdk#upgrading-from-v1xx-to-v2xx), [JavaScript](/docs/sdk/typescript/guide#upgrade1to2)). For the JavaScript SDK, consider an upgrade [from 2.x.x to 3.x.x](/docs/sdk/typescript/guide#upgrade2to3) as well. The upgrade is straightforward and should not take much time.
- Optionally switch to our [new integrations](/docs/get-started): Since the first major version, we built many new ways to integrate your code with Langfuse such as [Decorators](/docs/sdk/python/decorators) for Python. We would recommend to check out our [quickstart](/docs/get-started) to see whether there is a more convenient integration available for you.

#### Background of this change

Langfuse v3 relies on an event driven backend architecture.
This means, that we acknowledge HTTP requests from the SDKs, queue the HTTP bodies in the backend, and process them asynchronously.
This allows us to scale the backend more easily and handle more requests without overloading the database.
The SDKs below 2.0.0 send the events to our server and expect a synchronous response containing the database representation of the event.
If you rely on this data and access it in the code, your SDK will break as of Nov. 11th, 2024 for the cloud version and post-upgrade to Langfuse v3 when self-hosting.

### API Changes

#### POST /api/public/ingestion

The `/api/public/ingestion` endpoint is now asynchronous.
It will accept all events as they come in and queue them for processing before returning a 207 status code.
This means that events will _not_ be available immediately after acceptance by the backend and instead will appear
eventually in subsequent read requests.

As we switched our data store from Postgres to Clickhouse, we also had to remove the updating behavior for some fields. All of the listed fields below are not updatable anymore and new records are only written to the database, if the field was set in any event for the same `id`.

- `observation.startTime`
- `observation.type`
- `observation.traceId`
- `trace.timestamp`
- `score.timestamp`
- `score.traceId`

The individual events accepted a `metadata` property within their body of type `string | string[] | Record<string, unknown>`.
Only the `Record` type is supported within our UI and endpoints to perform queries and filter events.
Therefore, we enforce an object type for `metadata` going forward.
All incoming events with `{ metadata: string | string[] }` will have their metadata mapped to an object with key `metadata`,
i.e. `{ event: { body: { metadata: "test" } } }` will be transformed to `{ event: { body: { metadata: { metadata: "test" } } } }`.

#### POST /api/public/scores

The `/api/public/scores` endpoint is now asynchronous.
It behaves exactly as the `/api/public/ingestion` endpoint, but will return a 200 status code with a body of `{ id: string }` type.
Before, the endpoint returned the created score object directly.
This change is inline with our [API reference](https://api.reference.langfuse.com/#tag/score/post/api/public/scores) and therefore not considered breaking.

#### Deprecated endpoints

The following endpoints are deprecated since our v2 release and thereby have not been used by the Langfuse SDKs since Feb 2024.
Langfuse v3 continues to accept requests to these endpoints.
Their API behavior changes to be asynchronous and the endpoints will only return the id of the created object instead of the full updated record.
Please note that these endpoints will be removed in a future release.

- POST /api/public/events
- POST /api/public/generations
- PATCH /api/public/generations
- POST /api/public/spans
- PATCH /api/public/spans
- POST /api/public/traces

### UI Behavioral Changes

#### Trace Deletion

Deleting traces within the Langfuse UI was a synchronous operation and traces got removed immediately.
Going forward, all traces will be scheduled for deletion, but may still be visible in the UI for a short period of time.

#### Project Deletion

Deleting projects within Langfuse was a synchronous operation and projects got removed immediately.
Projects will be marked as deleted within Langfuse v3 and will not be accessible using the standard UI navigation options.
We immediately revoke access keys for projects, but all remaining data will be removed in the background.

Information will not be deleted from the [S3/Blob Store](/self-hosting/deployment/infrastructure/blobstorage).
This action needs to be performed manually by an administrator.
This process will be automated in a future release.

## Migration Steps

We tried to make the version upgrade as seamless as possible.
If you encounter any issues please reach out to [support](/support) or open an [issue on GitHub](/issues).

By following this guide, you can upgrade your Langfuse v2 deployment to v3 without prolonged downtime.

### Video Walkthrough

<iframe
  width="100%"
  className="aspect-[15.94/9] rounded-lg border mt-6 w-full"
  src="https://www.youtube-nocookie.com/embed/Bb24vYp5zkA?si=oCRGQUxemOhoUCwi"
  title="YouTube video player"
  frameborder="0"
  allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share"
  referrerpolicy="strict-origin-when-cross-origin"
  allowFullScreen
></iframe>

### Before you start the upgrade

Before starting your upgrade, make sure you are familiar with the contents of the respective [v3 deployment guide](/self-hosting).
In addition, we recommend that you perform a backup of your Postgres database before you start the upgrade.
Also, ensure that you run a recent version of Langfuse, ideally a version later than v2.92.0.

For a zero-downtime upgrade, we recommend that you provision new instances of the Langfuse web and worker containers
and move your traffic after validating that the new instances are working as expected.

  
If you go for the zero-downtime upgrade, we recommend to disable background
migrations until you shift traffic to the new instances. Otherwise, the
migration may miss events that are ingested after the new instances were
started. Set `LANGFUSE_ENABLE_BACKGROUND_MIGRATIONS=false` in the environment
variables of the new Langfuse web and worker containers until traffic is shifted. Afterwards, remove the overwrite or set to `true`.

In addition, the upgrade is known to work well between v2.92.0 and v3.29.0.
Newer versions of v3 remove database entities that v2 still depends on.
Therefore, we recommend to use v3.29.0 for parallel operations and upgrade to the latest v3 once the migration is complete.

### Upgrade Steps

<Steps>

#### 1. Provision new infrastructure

Ensure that you deploy all required storage components ([Clickhouse](/self-hosting/deployment/infrastructure/clickhouse), [Redis](/self-hosting/deployment/infrastructure/cache), [S3/Blob Store](/self-hosting/deployment/infrastructure/blobstorage)) and have the connection information handy.
You can reuse your existing Postgres instance for the new deployment.
Ensure that you also have your Postgres connection details ready.

The following new environment variables are required for the new Langfuse web and worker containers.
If you do not provide them, the deployment will fail.

- `CLICKHOUSE_URL`
- `CLICKHOUSE_USER`
- `CLICKHOUSE_PASSWORD`
- `CLICKHOUSE_MIGRATION_URL`
- `REDIS_CONNECTION_STRING` (or its alternatives)
- `LANGFUSE_S3_EVENT_UPLOAD_BUCKET`

#### 2. Start new Langfuse containers.

Deploy the Langfuse web and worker containers with the settings from our self-hosting guide.
Ensure that you set the environment variables for the new storage components and the Postgres connection details.

At this point, you can start to test the new Langfuse instance.
The UI should load as expected, but there should not be any traces, observations, or scores.
This is expected, as data is being read from Clickhouse while those elements still reside in Postgres.

#### 3. Shift traffic from v2 to v3

Point the traffic to the new Langfuse instance by updating your DNS records or your Loadbalancer configuration.
All new events will be stored in Clickhouse and should appear within the UI within a few seconds of being ingested.

#### 4. Wait for historic data migration to complete

We have introduced [background migrations](/self-hosting/upgrade/background-migrations) as part of the migration to v3.
Those allow Langfuse to schedule longer-running migrations without impacting the availability of the service.
As part of the v3 release, we have introduced four migrations that will run once you deploy the new stack.

1. **Cost backfill**: We calculate costs for all events and store them in the Postgres database. Before, those were calculated on read which had a negative impact on read performance.
2. **Traces migration**: We migrate all traces in batches from Postgres to Clickhouse. We start with most recent traces, i.e. those should show within your dashboard soon after starting the migration.
3. **Observations migration**: We migrate all observations in batches from Postgres to Clickhouse. We start with most recent observations, i.e. those should show within your dashboard soon after starting the migration.
4. **Scores migration**: We migrate all scores in batches from Postgres to Clickhouse. We start with most recent scores, i.e. those should show within your dashboard soon after starting the migration.

Each migration has to finish, before the next one starts.
Depending on the size of your event tables, this process may take multiple hours.

In case of any issues, please review the troubleshooting section in the [background migrations guide](/self-hosting/upgrade/background-migrations#troubleshooting).

#### 5. Stop the old Langfuse containers

After you have verified that new events are being stored in Clickhouse and are shown in the UI, you can stop the old Langfuse containers.

</Steps>

## Deployment Specific Guides

In this section, we collect deployment-specific guides to help you with the upgrade process.
Feel free to contribute guides or create GitHub issues if you encounter any issues or are missing guidelines.

### Docker Compose

For the docker compose upgrade we assume that a short downtime is acceptable while services are restarting.
If you want to perform a zero-downtime upgrade, you can follow the steps outlined in the general upgrade guide.
In case you use an external Postgres instance, you should follow the general guide as well and just start a new v3 deployment which points to your Postgres instance.

<Steps>

#### 1. Note the volume name for the Postgres instance

We assume that you have deployed Langfuse v2 using the docker-compose.yml from the [Langfuse repository](https://github.com/langfuse/langfuse/blob/v2/docker-compose.yml).
Take a note of the volume configuration for your database, e.g. `database_data` in the example below.

```yaml
volumes:
  database_data:
    driver: local
```

#### 2. Create a copy of the docker-compose.yml from v3

Create a copy of the Langfuse v3 [docker-compose file](https://github.com/langfuse/langfuse/blob/main/docker-compose.yml) on your local machine.
Replace the `langfuse_postgres_data` volume name with the one you noted in step 1.
Make sure to update the `volumes` and the `postgres` section.

#### 3. Stop the Langfuse v2 deployment (Beginning of downtime)

Run `docker compose down` to stop your Langfuse v2 deployment.
Make sure to _not_ add `-v` as we want to retain all volumes.

#### 4. Start the Langfuse v3 deployment (End of downtime)

Run `docker compose -f docker-compose.v3.yml up -d` to start the Langfuse v3 deployment.
Make sure to replace `docker-compose.v3.yml` with the name of the file you created in step 2.
You can start to ingest traces and use other features as soon as the new containers are up and running.
The deployment will migrate data from your v2 deployment in the background, so you may see some data missing in the UI for a while.

</Steps>

## Support

If you experience any issues, please create an [issue on GitHub](/issues) or contact the maintainers ([support](/support)).

For support with production deployments, the Langfuse team provides dedicated enterprise support. To learn more, reach out to enterprise@langfuse.com or [talk to us](/talk-to-us).

Alternatively, you may consider using Langfuse Cloud, which is a fully managed version of Langfuse. You can find information about its security and privacy [here](/security).
