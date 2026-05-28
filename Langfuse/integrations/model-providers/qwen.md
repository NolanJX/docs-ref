---
source: ⚠️ Jupyter Notebook
title: Observability for Qwen with Langfuse
sidebarTitle: Qwen
logo: /images/integrations/qwen_icon.png
description: Learn how to use Langfuse to monitor and trace your Qwen LLM applications using the OpenAI SDK integration.
category: Integrations
---

# Observability for Qwen with Langfuse

This notebook shows how to trace **Qwen** API calls with **Langfuse** using the OpenAI SDK drop-in replacement.

> **What is Qwen?** [Qwen](https://qwen.ai) is a family of large language models developed by Alibaba Cloud, offering models like Qwen-Plus, Qwen-Max, and Qwen-Turbo through an OpenAI-compatible API.

> **What is Langfuse?** [Langfuse](https://langfuse.com) is an open-source LLM engineering platform that helps teams trace, debug, and evaluate their LLM applications.

<Steps>
## Step 1: Install Dependencies

```python
%pip install openai langfuse
```

## Step 2: Set Up Environment Variables

Get your Langfuse keys from the project settings in [Langfuse Cloud](https://langfuse.com/cloud) or set up [self-hosting](https://langfuse.com/self-hosting).

```python
import os

# Get keys for your project from the project settings page: https://cloud.langfuse.com
os.environ["LANGFUSE_PUBLIC_KEY"] = "pk-lf-..."
os.environ["LANGFUSE_SECRET_KEY"] = "sk-lf-..."
os.environ["LANGFUSE_BASE_URL"] = "https://cloud.langfuse.com" # 🇪🇺 EU region
# os.environ["LANGFUSE_BASE_URL"] = "https://us.cloud.langfuse.com" # 🇺🇸 US region

os.environ["DASHSCOPE_API_KEY"] = "sk-..."  # Get your API key from https://qwen.ai/apiplatform
```

## Step 3: Use Langfuse OpenAI Drop-in Replacement

Instead of importing `openai` directly, import it from `langfuse.openai`. All calls through this client are automatically traced in Langfuse.

The Qwen API is OpenAI-compatible, so we just point the client at the [DashScope endpoint](https://help.aliyun.com/zh/model-studio/developer-reference/compatibility-of-openai-with-dashscope).

```python
import os
from langfuse.openai import openai

client = openai.OpenAI(
    api_key=os.environ.get("DASHSCOPE_API_KEY"),
    base_url="https://dashscope-intl.aliyuncs.com/compatible-mode/v1",
)
```

## Step 4: Run an Example

Send a chat completion request to a Qwen model. The trace will appear in your Langfuse dashboard automatically.

```python
response = client.chat.completions.create(
    model="qwen3.6-plus",
    messages=[
        {"role": "system", "content": "You are a helpful assistant."},
        {"role": "user", "content": "What makes open-source AI models important?"},
    ],
    name="Qwen-Trace",
)
print(response.choices[0].message.content)
```

## Step 5: View Traces in Langfuse

After running the example, open your [Langfuse dashboard](https://cloud.langfuse.com) to see the full trace including prompts, completions, tool calls, token usage, and latency.

![Example Qwen trace in Langfuse](https://langfuse.com/images/cookbook/integration-qwen/qwen-example-trace.png)

[Example trace in Langfuse](https://cloud.langfuse.com/project/cloramnkj0002jz088vzn1ja4/traces/d76897711559e73336d5257178f837b6?observation=b0dc6d1e7957f9fa&timestamp=2026-04-17T07:39:44.054Z&traceId=d76897711559e73336d5257178f837b6)

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

