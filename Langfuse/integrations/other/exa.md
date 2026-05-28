---
source: ⚠️ Jupyter Notebook
title: Monitor Exa AI Search with Langfuse
sidebarTitle: Exa
logo: /images/integrations/exa_icon.png
description: Learn how to trace Exa AI search operations using Langfuse to capture detailed observability data for your search queries and API calls.
category: Integrations
---

# Exa Integration

In this guide, we'll show you how to integrate [Langfuse](https://langfuse.com) with [Exa](https://exa.ai/) to trace your AI search operations. By leveraging Langfuse's tracing capabilities, you can automatically capture details such as inputs, outputs, and execution times of your Exa search functions.

> **What is Exa?** [Exa](https://exa.ai/) is an AI-powered search API built for LLMs and AI applications. Unlike traditional search engines, Exa is designed to understand semantic meaning and retrieve high-quality, relevant results that are perfect for AI use cases like RAG (Retrieval-Augmented Generation), research, and content discovery.

> **What is Langfuse?** [Langfuse](https://langfuse.com) is an open source LLM engineering platform that helps teams trace API calls, monitor performance, and debug issues in their AI applications.

<Steps>
## Install Dependencies

First, install the necessary Python packages:

```python
%pip install langfuse exa-py
```

### Set Up Environment Variables

Get your Langfuse API keys by signing up for [Langfuse Cloud](https://cloud.langfuse.com) or [self-hosting Langfuse](https://langfuse.com/self-hosting). You'll also need your Exa and OpenAI API key.

```python
import os

# Get keys for your project from the project settings page: https://cloud.langfuse.com
os.environ["LANGFUSE_PUBLIC_KEY"] = "pk-lf-..."
os.environ["LANGFUSE_SECRET_KEY"] = "sk-lf-..."
os.environ["LANGFUSE_HOST"] = "https://cloud.langfuse.com" # 🇪🇺 EU region
# Other Langfuse data regions include 🇺🇸 US: https://us.cloud.langfuse.com, 🇯🇵 Japan: https://jp.cloud.langfuse.com and ⚕️ HIPAA: https://hipaa.cloud.langfuse.com

# Your Exa API key
os.environ["EXA_API_KEY"] = "..."

# Your openai key
os.environ["OPENAI_API_KEY"] = "sk-..."
```

With the environment variables set, we can now initialize the Exa and the Langfuse client.

```python
from exa_py import Exa
from langfuse import get_client

exa = Exa(api_key= os.environ["EXA_API_KEY"])
langfuse = get_client()
```

## Example 1: Trace Exa `search_and_contents`

To monitor your Exa search operations, we use the [Langfuse `@observe()` decorator](https://langfuse.com/docs/sdk/python/decorators). In this example, the `@observe()` decorator captures the inputs, outputs, and execution time of the `search_with_exa()` function. For more control over the data you are sending to Langfuse, you can use the [Context Manager or create manual observations](https://langfuse.com/docs/observability/sdk/python/instrumentation#custom-instrumentation) using the Python SDK.

```python
from langfuse import observe

@observe(as_type="retriever")
def search_with_exa(query: str, num_results: int = 5):
    """Search the web using Exa AI and return results."""
    results = exa.search_and_contents(
        query,
        num_results=num_results,
        text=True
    )
    return results

# Example: Search for information about Langfuse
search_results = search_with_exa("What is Langfuse and how does it help with LLM observability?")

# Display the results
for result in search_results.results:
    print(f"Title: {result.title}")
    print(f"URL: {result.url}")
    print(f"Text: {result.text[:200]}...\n")
```

## Example 2: Exa Search together with OpenAI

You can also trace more complex workflows that involve summarizing the search results with OpenAI. Here we use the [Langfuse `@observe()` decorator](https://langfuse.com/docs/sdk/python/decorators) to group both the Exa search and the OpenAI generation into one trace.

```python
import os
from langfuse.openai import OpenAI

@observe()
def search_and_summarize(query: str):

    # 1. Exa search
    @observe(as_type="retriever")
    def search_with_exa(query: str, num_results: int = 5):
        """Search the web using Exa AI and return results."""
        results = exa.search_and_contents(
            query,
            num_results=num_results,
            text=True
        )
        return results

    results = search_with_exa(query)

    # 2. Build a short context
    context = "\n".join([f"{r.title} ({r.url}): {r.text}" for r in results.results])

    # 3. Summarize with OpenAI
    client = OpenAI()
    resp = client.chat.completions.create(
        model="gpt-5-mini",
        messages=[
            {"role": "system", "content": "Summarize the following search results clearly and concisely."},
            {"role": "user", "content": context}
        ]
    )

    print("Summary:\n", resp.choices[0].message.content)

search_and_summarize("What is Langfuse and how does it help with LLM observability?")
```

## See Traces in Langfuse

After executing the traced functions, log in to your [Langfuse Dashboard](https://cloud.langfuse.com) to view detailed trace logs. You'll be able to see:

- Search queries and their parameters
- Response times for each API call
- Nested traces showing the relationship between search and similarity operations
- Full input and output data for debugging

![Example trace in the Langfuse UI](https://langfuse.com/images/cookbook/integration_exa/exa-search-example-trace.png)

[Example trace in Langfuse](https://cloud.langfuse.com/project/cloramnkj0002jz088vzn1ja4/traces/27c03a05a881ae454f6612a99cc54a92?observation=5af197a22620dc4a&timestamp=2025-10-28T10:20:13.672Z)

</Steps>

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

