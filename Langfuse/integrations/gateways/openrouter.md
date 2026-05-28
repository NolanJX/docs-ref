---
title: "Trace OpenRouter with Langfuse"
sidebarTitle: OpenRouter
logo: /images/integrations/openrouter_icon.svg
description: "Learn how to integrate Langfuse with OpenRouter using Broadcast (no-code) or the OpenAI SDK."
---

# OpenRouter Integration

In this guide, we'll show you how to integrate [Langfuse](/) with [OpenRouter](https://openrouter.ai/).

> **What is OpenRouter?** [OpenRouter](https://openrouter.ai/) provides an OpenAI-compatible completion API to +280 language models and providers that you can call directly or using the OpenAI SDK. This allows developers to access a variety of LLMs through a single, unified interface.

> **What is Langfuse?** [Langfuse](/) is an open source LLM engineering platform that helps teams trace LLM calls, monitor performance, and debug issues in their AI applications.

Two ways to integrate Langfuse with OpenRouter:

1. [**You can use the OpenRouter Broadcast feature**](#broadcast) to automatically send traces to Langfuse without any code changes.
2. [**You can use the Langfuse OpenAI SDK wrapper**](#sdk) to manually send traces to Langfuse. Since OpenRouter uses the OpenAI API schema, we can utilize Langfuse's native integration with the OpenAI SDK, available in both [Python](/integrations/model-providers/openai-py) and [TypeScript](/integrations/model-providers/openai-js).

There is more information on e.g. cost tracking or interoperability with Langfuse's Python SDK as well as next steps in the guides below.

## 1. OpenRouter Broadcast Tracing with Langfuse (No-Code) [#broadcast]

OpenRouter's [Broadcast feature](https://openrouter.ai/docs/guides/features/broadcast/overview) can automatically send traces to Langfuse without any code changes. To enable this integration, go to your [OpenRouter settings](https://openrouter.ai/settings) and connect your Langfuse API keys. Once set up, all requests processed through OpenRouter will be traced and made available in your Langfuse project.

<Frame className="my-10" fullWidth>
  ![OpenRouter settings showing the Langfuse Broadcast
  configuration](/images/integrations/openrouter-broadcast-langfuse-config.png)
</Frame>

> **Note:** This method is recommended if you want to capture all OpenRouter requests in Langfuse with minimal configuration, and do not require advanced features like custom trace metadata or nested tracing.

For more information about setting up your Langfuse API keys or available options, see the [Langfuse getting started guide](/docs/observability/get-started).

## 2. SDK Integration [#sdk]

Use the Langfuse OpenAI SDK wrapper for advanced features like nested tracing, custom metadata, and full control over trace data. The rest of this guide covers the SDK integration method.

### Get started with SDK Integration

```bash
pip install langfuse openai
```

```python
import os

# Set your Langfuse API keys
LANGFUSE_SECRET_KEY="sk-lf-..."
LANGFUSE_PUBLIC_KEY="pk-lf-..."
# 🇪🇺 EU region
LANGFUSE_BASE_URL="https://cloud.langfuse.com"
# Other Langfuse data regions include 🇺🇸 US: https://us.cloud.langfuse.com, 🇯🇵 Japan: https://jp.cloud.langfuse.com and ⚕️ HIPAA: https://hipaa.cloud.langfuse.com
# Set your OpenRouter API key (OpenRouter uses the 'OPENAI_API_KEY' environment variable)
os.environ["OPENAI_API_KEY"] = "<YOUR_OPENROUTER_API_KEY>"
```

### Example 1: Simple LLM Call

Since OpenRouter provides an OpenAI-compatible API, we can use the [Langfuse OpenAI SDK wrapper](/integrations/model-providers/openai-py) to automatically log OpenRouter calls as generations in Langfuse.

- The `base_url` is set to OpenRouter's API endpoint.
- You can replace `"qwen/qwen-plus"` with any model available on OpenRouter.
- The `default_headers` can include optional headers as per OpenRouter's documentation.
- The `extra_body={"usage": {"include": True}}` includes the costs that OpenRouter returns.

```python
# Import the Langfuse OpenAI SDK wrapper
from langfuse.openai import openai

# Create an OpenAI client with OpenRouter's base URL
client = openai.OpenAI(
    base_url="https://openrouter.ai/api/v1",
    default_headers={
        "HTTP-Referer": "<YOUR_SITE_URL>",  # Optional: Your site URL
        "X-Title": "<YOUR_SITE_NAME>",      # Optional: Your site name
    }
)

# Make a chat completion request
response = client.chat.completions.create(
    model="qwen/qwen-plus",
    messages=[
        {"role": "system", "content": "You are a helpful assistant."},
        {"role": "user", "content": "Tell me a fun fact about space."}
    ],
    extra_body={"usage": {"include": True}},
    name="fun-fact-request"  # Optional: Name of the generation in Langfuse
)

# Print the assistant's reply
print(response.choices[0].message.content)
```

### Example 2: Nested LLM Calls

By using the `@observe()` decorator, we can capture execution details of any Python function, including nested LLM calls, inputs, outputs, and execution times. This provides in-depth observability with minimal code changes.

- The `@observe()` decorator captures inputs, outputs, and execution details of the functions.
- Nested functions `summarize_text` and `analyze_sentiment` are also decorated, creating a hierarchy of traces.
- Each LLM call within the functions is logged, providing a detailed trace of the execution flow.

```python
from langfuse import observe
from langfuse.openai import openai

# Create an OpenAI client with OpenRouter's base URL
client = openai.OpenAI(
    base_url="https://openrouter.ai/api/v1",
)

@observe()  # This decorator enables tracing of the function
def analyze_text(text: str):
    # First LLM call: Summarize the text
    summary_response = summarize_text(text)
    summary = summary_response.choices[0].message.content

    # Second LLM call: Analyze the sentiment of the summary
    sentiment_response = analyze_sentiment(summary)
    sentiment = sentiment_response.choices[0].message.content

    return {
        "summary": summary,
        "sentiment": sentiment
    }

@observe()  # Nested function to be traced
def summarize_text(text: str):
    return client.chat.completions.create(
        model="openai/gpt-3.5-turbo",
        messages=[
            {"role": "system", "content": "You summarize texts in a concise manner."},
            {"role": "user", "content": f"Summarize the following text:\n{text}"}
        ],
        extra_body={"usage": {"include": True}},
        name="summarize-text"
    )

@observe()  # Nested function to be traced
def analyze_sentiment(summary: str):
    return client.chat.completions.create(
        model="openai/gpt-3.5-turbo",
        messages=[
            {"role": "system", "content": "You analyze the sentiment of texts."},
            {"role": "user", "content": f"Analyze the sentiment of the following summary:\n{summary}"}
        ],
        extra_body={"usage": {"include": True}},
        name="analyze-sentiment"
    )

# Example usage
text_to_analyze = "OpenAI's GPT-4 model has significantly advanced the field of AI, setting new standards for language generation."
analyze_text(text_to_analyze)
```

![Example trace in Langfuse](/images/docs/openrouter-example-trace/openrouter-example-trace.png)

_[Public link to example trace in Langfuse](https://cloud.langfuse.com/project/cloramnkj0002jz088vzn1ja4/traces/f0f78d8887011612a3c25bdcb8fe75fd?timestamp=2025-09-04T09:39:47.697Z&display=details)_

### Cost Tracking

Langfuse [token and cost tracking](/docs/observability/features/token-and-cost-tracking) can directly capture the OpenRouter cost instead of calculating it. This will increase the accuracy of the cost tracking, especially for less popular models.

To enable this, you need to:

1. Use the Langfuse OpenAI integration as shown above
2. Enable [Usage Accounting](https://openrouter.ai/docs/use-cases/usage-accounting) in OpenRouter as this results in costs being returned by OpenRouter for each call

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

