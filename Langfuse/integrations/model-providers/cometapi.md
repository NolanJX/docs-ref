---
source: ⚠️ Jupyter Notebook
title: Monitor CometAPI with Langfuse
sidebarTitle: CometAPI
logo: /images/integrations/cometapi_icon.svg
description: Learn how to integrate CometAPI with Langfuse using the OpenAI drop-in replacement.
category: Integrations
---

# Observability for CometAPI with Langfuse

This guide shows you how to integrate CometAPI with Langfuse. CometAPI's API endpoints for chat, language and code are fully compatible with OpenAI's API. This allows us to use the Langfuse OpenAI drop-in replacement to trace all parts of your application.

> **What is CometAPI?** [CometAPI](https://www.cometapi.com/?utm_source=langfuse&utm_campaign=integration&utm_medium=integration&utm_content=integration) is a unified AI model API platform providing access to 500+ AI models through a single OpenAI-compatible interface. Whether you need chat models, embeddings, or specialized AI capabilities, CometAPI offers affordable and reliable access with simple integration.

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

os.environ["LANGFUSE_PUBLIC_KEY"] = "pk-..."
os.environ["LANGFUSE_SECRET_KEY"] = "sk-..."
os.environ["LANGFUSE_BASE_URL"] = "https://cloud.langfuse.com" # 🇪🇺 EU region
# Other Langfuse data regions include 🇺🇸 US: https://us.cloud.langfuse.com, 🇯🇵 Japan: https://jp.cloud.langfuse.com and ⚕️ HIPAA: https://hipaa.cloud.langfuse.com


# Get your CometAPI API key from https://api.cometapi.com/console/token
os.environ["COMETAPI_KEY"] = "..."
```

## Step 3: Langfuse OpenAI drop-in Replacement

In this step we use the native [OpenAI drop-in replacement](https://langfuse.com/integrations/model-providers/openai-py) by importing `from langfuse.openai import openai`.

To start using CometAPI with OpenAI's client libraries, pass in your CometAPI API key to the `api_key` option, and change the `base_url` to `https://api.cometapi.com/v1/`:

```python
# instead of import openai:
from langfuse.openai import openai

client = openai.OpenAI(
  api_key=os.environ.get("COMETAPI_KEY"),
  base_url="https://api.cometapi.com/v1/",
)
```

**Note:** The OpenAI drop-in replacement is fully compatible with the [Low-Level Langfuse Python SDKs](https://langfuse.com/docs/sdk/python/low-level-sdk) and [`@observe()` decorator](https://langfuse.com/docs/sdk/python/decorators) to trace all parts of your application.

## Step 4: Run An Example

The following cell demonstrates how to call CometAPI's chat model using the traced OpenAI client. All API calls will be automatically traced by Langfuse.

```python
client = openai.OpenAI(
  api_key=os.environ.get("COMETAPI_KEY"),
  base_url="https://api.cometapi.com/v1/",
)

response = client.chat.completions.create(
  model="gpt-4o-mini",
  messages=[
    {"role": "system", "content": "Act like you are a helpful assistant."},
    {"role": "user", "content": "What are the famous attractions in San Francisco?"},
  ]
)

print(response.choices[0].message.content)
```

## Step 5: See Traces in Langfuse

After running the example model call, you can see the traces in Langfuse. You will see detailed information about your CometAPI API calls, including:

- Request parameters (model, messages, temperature, etc.)
- Response content
- Token usage statistics
- Latency metrics

_Example trace link in Langfuse_

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

## Resources

- Check the [CometAPI Documentation](https://api.cometapi.com/doc) for further details on available models and API options.
- Visit [CometAPI](https://www.cometapi.com/?utm_source=langfuse&utm_campaign=integration&utm_medium=integration&utm_content=integration) to get your API key and explore 500+ AI models.
- Visit [Langfuse](https://langfuse.com) to learn more about monitoring and tracing capabilities for your LLM applications.
