---
source: ⚠️ Jupyter Notebook
title: Monitor Firecrawl Scraper with Langfuse
sidebarTitle: Firecrawl
logo: /images/integrations/firecrawl_icon.png
description: Learn how to trace Firecrawl operations using Langfuse to capture detailed observability data for your web scraping tasks.
category: Integrations
---

# Firecrawl Integration

In this guide, we'll show you how to integrate [Langfuse](https://langfuse.com) with [Firecrawl](https://www.firecrawl.dev/) to trace your web scraping operations. By leveraging Langfuse’s tracing capabilities, you can automatically capture details such as inputs, outputs, and execution times of your Firecrawl functions.

> **What is Firecrawl?** [Firecrawl](https://www.firecrawl.dev/) is an API service that crawls websites—automatically processing the target URL and its accessible subpages—and converts the retrieved content into clean, LLM-ready markdown. It offers robust scraping, crawling, and extraction capabilities that transform web content (including HTML, metadata, and more) into structured data for various applications.

> **What is Langfuse?** [Langfuse](https://langfuse.com) is an open source LLM engineering platform that helps teams trace API calls, monitor performance, and debug issues in their AI applications.

## Get Started

First, install the necessary Python packages:

```python
%pip install langfuse firecrawl-py
```

Next, configure your environment with your Langfuse API keys. You can obtain your keys from your Langfuse dashboard.

```python
import os
# Get keys for your project from the project settings page https://cloud.langfuse.com

os.environ["LANGFUSE_PUBLIC_KEY"] = "pk-lf-..."
os.environ["LANGFUSE_SECRET_KEY"] = "sk-lf-..."
os.environ["LANGFUSE_BASE_URL"] = "https://cloud.langfuse.com" # 🇪🇺 EU region
# Other Langfuse data regions include 🇺🇸 US: https://us.cloud.langfuse.com, 🇯🇵 Japan: https://jp.cloud.langfuse.com and ⚕️ HIPAA: https://hipaa.cloud.langfuse.com
```

Initialize the Firecrawl application with your Firecrawl API key.

```python
from firecrawl import FirecrawlApp

app = FirecrawlApp(api_key="fc-...")
```

To monitor your Firecrawl scraper, we use the L[angfuse `@observe()` decorator](https://langfuse.com/docs/sdk/python/decorators). In this example, the `@observe()` decorator captures the inputs, outputs, and execution time of the `scrape_website()` function. All trace data is automatically sent to Langfuse, allowing you to monitor your Firecrawl operations in real time.

```python
from langfuse import observe

@observe()
def scrape_website(url):
  scrape_status = app.scrape_url(
    url,
    params={'formats': ['markdown', 'html']}
  )
  return scrape_status

scrape_website("https://langfuse.com")
```

## See Traces in Langfuse

After executing the traced function, log in to your [Langfuse Dashboard](https://cloud.langfuse.com) to view detailed trace logs.

![Example trace in Langfuse](https://langfuse.com/images/cookbook/integration-firecrawl/firecrawl-example-trace.png)

_[Public link to example trace in Langfuse](https://cloud.langfuse.com/project/cloramnkj0002jz088vzn1ja4/traces/49206b8e-5366-4458-9357-bd38b740ec4e?timestamp=2025-02-18T16%3A42%3A51.446Z)_

## Interoperability with the Python SDK

You can use this integration together with the Langfuse [SDKs](/docs/observability/sdk/overview) to add additional attributes to the observation.

<Tabs items={["Decorator", "Context Manager"]}>
<Tab>

The [`@observe()` decorator](/docs/observability/sdk/instrumentation#custom-instrumentation) provides a convenient way to automatically wrap your instrumented code and add additional attributes to the observation.

```python
from langfuse import observe, propagate_attributes, get_client

langfuse = get_client()

@observe()
def my_llm_pipeline(input):
    # Add additional attributes (user_id, session_id, metadata, version, tags) to all spans created within this execution scope
    with propagate_attributes(
        user_id="user_123",
        session_id="session_abc",
        tags=["agent", "my-observation"],
        metadata={"email": "user@langfuse.com"},
        version="1.0.0"
    ):

        # YOUR APPLICATION CODE HERE
        result = call_llm(input)

        return result

# Run the function
my_llm_pipeline("Hi")
```

Learn more about using the Decorator in the [Langfuse SDK instrumentation docs](/docs/observability/sdk/instrumentation#custom-instrumentation).

</Tab>
<Tab>

The [Context Manager](/docs/observability/sdk/instrumentation#custom-instrumentation) allows you to wrap your instrumented code using context managers (with `with` statements), which allows you to add additional attributes to the observation.

```python
from langfuse import get_client, propagate_attributes

langfuse = get_client()

with langfuse.start_as_current_observation(
    as_type="span",
    name="my-observation",
    trace_context={"trace_id": "abcdef1234567890abcdef1234567890"},  # Must be 32 hex chars
) as observation:

    # Add additional attributes (user_id, session_id, metadata, version, tags)
    # to all observations created within this execution scope
    with propagate_attributes(
        user_id="user_123",
        session_id="session_abc",
        metadata={"experiment": "variant_a", "env": "prod"},
        version="1.0",
    ):
        # YOUR APPLICATION CODE HERE
        result = call_llm("some input")

# Flush events in short-lived applications
langfuse.flush()
```

Learn more about using the Context Manager in the [Langfuse SDK instrumentation docs](/docs/observability/sdk/instrumentation#custom-instrumentation).

</Tab>
</Tabs>

## Troubleshooting

<details>
<summary>No observations appearing</summary>

First, enable [debug mode](/docs/observability/sdk/advanced-features#logging--debugging) in the Python SDK:

```bash
export LANGFUSE_DEBUG="True"
```

Then run your application and check the debug logs:

- **OTel observations appear in the logs:** Your application is instrumented correctly but observations are not reaching Langfuse. To resolve this:
  1. Call [`langfuse.flush()`](/docs/observability/sdk/instrumentation#client-lifecycle--flushing) at the end of your application to ensure all observations are exported.
  2. Verify that you are using the correct API keys and base URL.
- **No OTel spans in the logs:** Your application is not instrumented correctly. Make sure the instrumentation runs before your application code.

</details>

<details>
<summary>Unwanted observations in Langfuse</summary>

The Langfuse SDK is based on OpenTelemetry. Other libraries in your application may emit OTel spans that are not relevant to you. These still count toward your [billable units](/docs/administration/billable-units), so you should filter them out. See [Unwanted spans in Langfuse](/faq/all/unwanted-http-database-spans) for details.

</details>

<details>
<summary>Missing attributes</summary>

Some attributes may be stored in the metadata object of the observation rather than being mapped to the Langfuse data model. If a mapping or integration does not work as expected, please [raise an issue on GitHub](/issues).

</details>

## Next Steps

Once you have instrumented your code, you can manage, evaluate and debug your application:

<Cards num={2}> 
  <Cards.Card
    title="Manage prompts in Langfuse"
    href="/docs/prompts/get-started"
    icon={}
  />
  <Cards.Card
    title="Add evaluation scores"
    href="/docs/evaluation/features/evaluation-methods/custom-scores"
    icon={}
  />
  <Cards.Card
    title="Run LLM-as-a-judge Evaluators"
    href="/docs/scores/model-based-evals"
    icon={}
  />
  <Cards.Card
    title="Create datasets"
    href="/docs/datasets/overview"
    icon={}
  />
  <Cards.Card
    title="Create custom dashboards"
    href="/docs/analytics/custom-dashboards"
    icon={}
  />
  <Cards.Card
    title="Test queries in the Playground"
    href="/docs/playground"
    icon={}
  />
</Cards>

