---
source: ⚠️ Jupyter Notebook
title: Integrate X.ai (Grok) with Langfuse
sidebarTitle: xAI Grok
logo: /images/integrations/xai_icon.svg
description: Guide on integrating xAI's Grok models with Langfuse for observability.
category: Integrations
---

# Observability for xAI / Grok with Langfuse

This guide shows you how to integrate Grok with Langfuse using the OpenAI SDK.

> **What is Grok?** Grok is X.ai’s advanced AI platform that streamlines natural language processing for intelligent application integration. Learn more at [Grok Documentation](https://docs.x.ai/docs).

> **What is Langfuse?** [Langfuse](https://langfuse.com) is an open source LLM engineering platform that helps teams trace API calls, monitor performance, and debug issues in their AI applications.

<Steps>
## Step 1: Install Dependencies

Make sure you have installed the necessary Python packages:

```python
%pip install openai langfuse
```

## Step 2: Set Up Environment Variables

```python
import os

# Get keys for your project from the project settings page
# https://cloud.langfuse.com

os.environ["LANGFUSE_PUBLIC_KEY"] = "pk-lf-..."
os.environ["LANGFUSE_SECRET_KEY"] = "sk-lf-..."
os.environ["LANGFUSE_BASE_URL"] = "https://cloud.langfuse.com" # 🇪🇺 EU region
# Other Langfuse data regions include 🇺🇸 US: https://us.cloud.langfuse.com, 🇯🇵 Japan: https://jp.cloud.langfuse.com and ⚕️ HIPAA: https://hipaa.cloud.langfuse.com


# Get your Grok API key from your Grok account settings
os.environ["GROK_API_KEY"] = "xai-..."
```

## Step 3: Use Grok with the OpenAI SDK

To utilize Grok through the OpenAI SDK, we use the Langfuse drop-in replacement for OpenAI. Replace the base URL with Grok’s endpoint.

```python
# Instead of importing openai directly:
from langfuse.openai import openai

client = openai.OpenAI(
  api_key=os.environ.get("GROK_API_KEY"),
  base_url="https://api.x.ai/v1"  # Grok's endpoint
)
```

## Step 4: Run an Example

The following example demonstrates how to make a simple request using Grok's API. All API calls will be automatically traced by Langfuse.

```python
response = client.chat.completions.create(
  model="grok-2-latest",
  messages=[
    {"role": "system", "content": "You are an assistant."},
    {"role": "user", "content": "What is Langfuse?"}
  ],
  name = "Grok-2-Trace"
)

print(response.choices[0].message.content)
```

## Step 5: Enhance Tracing (Optional)

You can enhance your Grok traces:

- Add [metadata](https://langfuse.com/docs/tracing-features/metadata), [tags](https://langfuse.com/docs/tracing-features/tags), [log levels](https://langfuse.com/docs/tracing-features/log-levels) and [user IDs](https://langfuse.com/docs/tracing-features/users) to traces
- Group traces by [sessions](https://langfuse.com/docs/tracing-features/sessions)
- [`@observe()` decorator](https://langfuse.com/docs/sdk/python/decorators) to trace additional application logic
- Use [Langfuse Prompt Management](https://langfuse.com/docs/prompts/get-started) and link prompts to traces
- Add [score](https://langfuse.com/docs/scores/custom) to traces

Visit the [OpenAI SDK cookbook](https://langfuse.com/guides/cookbook/integration_openai_sdk) to see more examples on passing additional parameters.
Find out more about Langfuse Evaluations and Prompt Management in the [Langfuse documentation](https://langfuse.com/docs).

## Step 6: See Traces in Langfuse

After running the example, log in to Langfuse to view the detailed traces, including:

- Request parameters
- Response content
- Token usage and latency metrics

![Langfuse Trace Example](https://langfuse.com/images/cookbook/integration-grok/grok-example-trace.png)

_[Public example trace link in Langfuse](https://cloud.langfuse.com/project/cloramnkj0002jz088vzn1ja4/traces/9178205e-2cb4-4952-8ec0-5244a1105263?timestamp=2025-03-05T11%3A07%3A46.600Z)_

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

