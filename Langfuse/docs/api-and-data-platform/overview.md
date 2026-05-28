---
title: Overview
seoTitle: "Open Source LLM API & Data Platform"
description: Langfuse is designed to be extensible and flexible. People using Langfuse are building all kinds of workflows and customizations on top of it. This is powered by our open data platform.
---

# API & Data Platform

**Langfuse is designed to be open, extensible and flexible** (see [_why langfuse?_](/why)). People using Langfuse are building all kinds of workflows and customizations on top of it. This is powered by our open data platform.

Example use cases:

- Billing based on LLM costs tracked in Langfuse
- Reporting of online evaluations in external dashboards
- Fine-tuning based on raw exports of traces
- Correlation of LLM Evals with observed user behavior in Data Warehouse

## Start here

```mermaid
graph LR
    LF["Langfuse<br/><br/>• Traces<br/>• Prompts<br/>• Scores"]

    LF --> API["Public API"]
    LF --> Dash["In-app Dashboards"]
    LF --> Metrics["Metrics API v2"]
    LF --> Observations["Observations API v2"]
    LF --> Export["Exports to S3 / DWH"]
```

Choose the data access path based on what you want to build:

| Goal                                                           | Recommended path                                                                   |
| -------------------------------------------------------------- | ---------------------------------------------------------------------------------- |
| Query aggregate cost, usage, latency, volume, or score metrics | [Metrics API v2](/docs/metrics/features/metrics-api#v2)                            |
| Retrieve row-level spans, generations, or events               | [Observations API v2](/docs/api-and-data-platform/features/observations-api#v2)    |
| Use the API from Python or JS/TS                               | [Query via SDKs](/docs/api-and-data-platform/features/query-via-sdk)               |
| Export large volumes on a schedule                             | [Blob Storage Export](/docs/api-and-data-platform/features/export-to-blob-storage) |
| Download a filtered one-off export                             | [Export from UI](/docs/api-and-data-platform/features/export-from-ui)              |
| Manage prompts, datasets, projects, and other resources        | [Public API](/docs/api-and-data-platform/features/public-api)                      |

## Features

<Cards num={3}>
  }
    arrow
  />
  }
    arrow
  />
  }
    arrow
  />
  }
    arrow
  />
  }
    arrow
  />
  }
    arrow
  />
  }
    arrow
  />
  <Card
    title="Export to PostHog"
    href="/integrations/analytics/posthog"
    icon={
      <div className="w-6 h-6 dark:bg-white rounded-sm p-1 flex items-center justify-center">
        <img
          src="/images/integrations/posthog_icon.svg"
          alt="PostHog icon"
          className="w-full h-full object-contain"
        />
      </div>
    }
    arrow
  />
  <Card
    title="Export to Mixpanel"
    href="/integrations/analytics/mixpanel"
    icon={
      <div className="w-6 h-6 dark:bg-white rounded-sm p-1 flex items-center justify-center">
        <img
          src="/images/integrations/mixpanel_icon.svg"
          alt="Mixpanel icon"
          className="w-full h-full object-contain"
        />
      </div>
    }
    arrow
  />
</Cards>
