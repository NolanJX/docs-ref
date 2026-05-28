---
title: Networking (self-hosted)
description: Learn how to configure networking for your self-hosted Langfuse deployment. Langfuse can be run without internet access.
label: "Version: v3"
sidebarTitle: "Networking"
---

# Networking

Langfuse can be deployed in a VPC or on-premises in high-security environments. This guide covers the networking requirements and considerations.

Architecture diagram (from [architecture overview](/self-hosting#architecture)):

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

## Network Exposure & Service Configuration

Only the `langfuse/langfuse` (web) container needs to be accessible by users, via API, and SDKs.
Optionally, this can be behind a firewall, proxy, or VPN.

By default `PORT=3000` is used for the Langfuse Web container. This can be configured using the `PORT` environment variable ([docs](/self-hosting/configuration)). Usually a network load balancer is used to expose the service and handle ssl termination ([docs](/self-hosting/configuration/encryption)).

Langfuse is designed to be exposed publicly as a web service.
This is penetration tested and secure by design as the Langfuse Team runs the same container for the managed Langfuse Cloud Offering.
See [security documentation](/security) of Langfuse Cloud for more details.

## Internet Access

Langfuse does not require internet access.

Some optional components, like the LLM Playground and LLM-evals require access to an [LLM API/Gateway](/self-hosting/deployment/infrastructure/llm-api).
This can be deployed in the same VPC or peered with the VPC.

Langfuse pings a cached version of the GitHub API to check for updates to the Langfuse Server. If internet access is not available, this check will fail gracefully.
