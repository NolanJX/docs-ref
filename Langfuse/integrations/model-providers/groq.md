---
source: ⚠️ Jupyter Notebook
title: Observability for Groq models with Langfuse Integration
description: Traceability and observability for Groq language models with Langfuse. This cookbook provides examples on how to use the OpenAI SDK and the Groq SDK to interact with Groq models and trace them with Langfuse.
category: Integrations
sidebarTitle: Groq
logo: /images/integrations/groq_icon.png
---

# Cookbook: Observability for Groq Models (Python)

This cookbook shows two ways to interact with Groq models and trace them with Langfuse:

1. Using the OpenAI SDK to interact with the Groq model
2. Using the OpenInference instrumentation library to interact with Groq models

By following these examples, you'll learn how to log and trace interactions with Groq language models, enabling you to debug and evaluate the performance of your AI-driven applications.

**Note:** *Langfuse is also natively integrated with [LangChain](https://langfuse.com/integrations/frameworks/langchain), [LlamaIndex](https://langfuse.com/integrations/frameworks/llamaindex), [LiteLLM](https://langfuse.com/integrations/gateways/litellm), and [other frameworks](https://langfuse.com/integrations). If you use one of them, any use of Groq models is instrumented right away.*

To get started, set up your environment variables for Langfuse and Groq:

```python
import os

# Get keys for your project from the project settings page: https://cloud.langfuse.com
os.environ["LANGFUSE_PUBLIC_KEY"] = "pk-lf-..."
os.environ["LANGFUSE_SECRET_KEY"] = "sk-lf-..."
os.environ["LANGFUSE_BASE_URL"] = "https://cloud.langfuse.com" # 🇪🇺 EU region
# Other Langfuse data regions include 🇺🇸 US: https://us.cloud.langfuse.com, 🇯🇵 Japan: https://jp.cloud.langfuse.com and ⚕️ HIPAA: https://hipaa.cloud.langfuse.com

# Your Groq API key
os.environ["GROQ_API_KEY"] = "gsk_..."
```

## Option 1: Using the OpenAI SDK to interact with the Groq model

**Note**: _This example shows how to use the OpenAI Python SDK. If you use JS/TS, have a look at our [OpenAI JS/TS SDK](https://langfuse.com/integrations/model-providers/openai-js)._

### Install Required Packages

```python
%pip install langfuse openai --upgrade
```

### Import Necessary Modules

Instead of importing `openai` directly, import it from `langfuse.openai`. Also, import any other necessary modules.

```python
# Instead of: import openai
from langfuse.openai import OpenAI
```

### Initialize the OpenAI Client for the Groq Model

Initialize the OpenAI client but point it to the Groq model endpoint. Replace the access token with your own.

```python
client = OpenAI(
    base_url="https://api.groq.com/openai/v1",
    api_key=os.environ.get("GROQ_API_KEY")
)
```

### Chat Completion Request

Use the `client` to make a chat completion request to the Groq model.

```python
completion = client.chat.completions.create(
    model="llama3-8b-8192",
    messages=[
        {"role": "system", "content": "You are a helpful assistant."},
        {
            "role": "user",
            "content": "Write a poem about language models"
        }
    ]
)
print(completion.choices[0].message.content)
```

_[Example trace in Langfuse](https://cloud.langfuse.com/project/cm0nywmaa005c3ol2msoisiho/traces/8c0fe015-2d87-46a8-87e6-e6bd439b35b5?timestamp=2025-01-10T12%3A55%3A11.990Z)_

## Option 2: Using the OpenInference instrumentation

This option will use the [OpenInference instrumentation library](https://github.com/Arize-ai/openinference/tree/main/python/instrumentation/openinference-instrumentation-groq) to send traces to Langfuse.

For more detailed guidance on the Groq SDK, please refer to the [Groq Documentation](https://console.groq.com/docs) and the [Langfuse Documentation](https://langfuse.com/docs/sdk/python/decorators#log-any-llm-call).

### Install Required Packages

```python
%pip install groq langfuse openinference-instrumentation-groq
```

```python
import os

# Get keys for your project from the project settings page: https://cloud.langfuse.com
os.environ["LANGFUSE_PUBLIC_KEY"] = "pk-lf-..."
os.environ["LANGFUSE_SECRET_KEY"] = "sk-lf-..."
os.environ["LANGFUSE_BASE_URL"] = "https://cloud.langfuse.com" # 🇪🇺 EU region
# Other Langfuse data regions include 🇺🇸 US: https://us.cloud.langfuse.com, 🇯🇵 Japan: https://jp.cloud.langfuse.com and ⚕️ HIPAA: https://hipaa.cloud.langfuse.com

# Your Groq API key
os.environ["GROQ_API_KEY"] = "gsk_..."
```

With the environment variables set, we can now initialize the Langfuse client. `get_client()` initializes the Langfuse client using the credentials provided in the environment variables.

```python
from langfuse import get_client

# Initialise Langfuse client and verify connectivity
langfuse = get_client()
assert langfuse.auth_check(), "Langfuse auth failed - check your keys ✋"
```

### OpenTelemetry Instrumentation

Use the [OpenInference instrumentation library](https://github.com/Arize-ai/openinference/tree/main/python/instrumentation/openinference-instrumentation-groq) to wrap the Groq SDK calls and send OpenTelemetry spans to Langfuse.

```python
from openinference.instrumentation.groq import GroqInstrumentor

GroqInstrumentor().instrument()
```

### Example LLM Call

```python
from groq import Groq

# Initialize Groq client
groq_client = Groq(api_key=os.environ["GROQ_API_KEY"])
```

```python
chat_completion = groq_client.chat.completions.create(
    messages=[
        {
            "role": "user",
            "content": "Explain the importance of fast language models",
        }
    ],
    model="llama-3.3-70b-versatile",
)

print(chat_completion.choices[0].message.content)
```

### See Traces in Langfuse

After running the example model call, you can see the traces in Langfuse. You will see detailed information about your Groq API calls, including:

- Request parameters (model, messages, temperature, etc.)
- Response content
- Token usage statistics
- Latency metrics

![Example trace in Langfuse](https://langfuse.com/images/cookbook/integration-groq/single-trace-example.png)

_[Example trace in Langfuse](https://cloud.langfuse.com/project/cloramnkj0002jz088vzn1ja4/traces/986dec33c084f29a2cf57fc35eec8668?timestamp=2025-08-06T07%3A41%3A00.503Z&display=details)_

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

