---
source: ⚠️ Jupyter Notebook
title: Example cookbook on how to monitor DeepSeek models with Langfuse using the OpenAI SDK
sidebarTitle: DeepSeek
logo: /images/integrations/deepseek_icon.svg
description: Discover how to integrate Langfuse with DeepSeek for enhanced LLM application monitoring, debugging, and tracing. Improve your AI development workflow today.
category: Integrations
---

# Cookbook: Monitor DeepSeek Models with Langfuse Using the OpenAI SDK

The DeepSeek API uses an API format compatible with OpenAI. By modifying the configuration, you can use the OpenAI SDK or software compatible with the OpenAI API to access the DeepSeek API.

This cookbook demonstrates how to monitor [DeepSeek](https://github.com/deepseek-ai/DeepSeek-V3) models using the OpenAI SDK integration with [Langfuse](https://langfuse.com). By leveraging Langfuse's observability tools and the OpenAI SDK, you can effectively debug, monitor, and evaluate your applications that utilize DeepSeek models.

This guide will walk you through setting up the integration, making requests to DeepSeek models, and observing the interactions with Langfuse.

**Note:** *Langfuse is also natively integrated with [LangChain](https://langfuse.com/integrations/frameworks/langchain), [LlamaIndex](https://langfuse.com/integrations/frameworks/llamaindex), [LiteLLM](https://langfuse.com/integrations/gateways/litellm), and [other frameworks](https://langfuse.com/integrations). These frameworks can be used as well to trace DeepSeek requests.*

## Setup

### Install Required Packages

To get started, install the necessary packages. Ensure you have the latest versions of `langfuse` and `openai`.

```python
%pip install langfuse openai --upgrade
```

### Set Environment Variables

Set up your environment variables with the necessary keys. Obtain your Langfuse project keys from [Langfuse Cloud](https://cloud.langfuse.com). You will also need an access token from [DeepSeek](https://platform.deepseek.com/api_keys) to access their models.

```python
import os

# Get keys for your project from the project settings page: https://cloud.langfuse.com
os.environ["LANGFUSE_PUBLIC_KEY"] = "pk-lf-..."
os.environ["LANGFUSE_SECRET_KEY"] = "sk-lf-..."
os.environ["LANGFUSE_BASE_URL"] = "https://cloud.langfuse.com" # 🇪🇺 EU region
# Other Langfuse data regions include 🇺🇸 US: https://us.cloud.langfuse.com, 🇯🇵 Japan: https://jp.cloud.langfuse.com and ⚕️ HIPAA: https://hipaa.cloud.langfuse.com

# Your DeepSeek API key (get it from https://platform.deepseek.com/api_keys)
os.environ["DEEPSEEK_API_KEY"] = "sk-..."  # Replace with your DeepSeek API key
```

### Import Necessary Modules

Instead of importing `openai` directly, import it from `langfuse.openai`. Also, import any other necessary modules.

Check out our [OpenAI integration docs](https://langfuse.com/integrations/model-providers/openai-py) to learn how to use this integration with other Langfuse [features](https://langfuse.com/docs/tracing#advanced-usage).

```python
# Instead of: import openai
from langfuse.openai import OpenAI
from langfuse import observe
```

### Initialize the OpenAI Client for DeepSeek Models

Initialize the OpenAI client, pointing it to the DeepSeek model endpoint. Replace the model URL and APP key with your own.

```python
# Initialize the OpenAI client, pointing it to the DeepSeek Inference API
client = OpenAI(
    base_url="https://api.deepseek.com",  # Replace with the DeepSeek model endpoint URL
    api_key=os.getenv('DEEPSEEK_API_KEY'),  # Replace with your DeepSeek API key
)
```

## Examples

### Chat Completion Request

Use the `client` to make a chat completion request to the DeepSeek model. The `model` parameter can be any identifier since the actual model is specified in the `base_url`.

```python
completion = client.chat.completions.create(
    model="deepseek-chat",
    messages=[
        {"role": "system", "content": "You are a helpful assistant."},
        {"role": "user", "content": "Why is AI cool? Answer in 20 words or less."}
    ]
)
print(completion.choices[0].message.content)
```

    AI is cool because it automates tasks, enhances creativity, and solves complex problems quickly—making life smarter and easier.

![Example trace in Langfuse](https://langfuse.com/images/cookbook/integration_deepseek/deepseek-simple-trace.png)

_[View the example trace in Langfuse](https://cloud.langfuse.com/project/cloramnkj0002jz088vzn1ja4/traces/83702a6c-ae0e-4317-87fa-dc82568a2d89?timestamp=2025-01-09T17%3A06%3A40.848Z)_

### Observe the Request with Langfuse

By using the `OpenAI` client from `langfuse.openai`, your requests are automatically traced in Langfuse. You can also use the `@observe()` decorator to group multiple generations into a single trace.

```python
@observe()  # Decorator to automatically create a trace and nest generations
def generate_story():
    completion = client.chat.completions.create(
        name="story-generator",
        model="deepseek-chat",
        messages=[
            {"role": "system", "content": "You are a creative storyteller."},
            {"role": "user", "content": "Tell me a short story about a token that got lost on its way to the language model. Answer in 100 words or less."}
        ],
        metadata={"genre": "adventure"},
    )
    return completion.choices[0].message.content

story = generate_story()
print(story)
```

    **The Lost Token**\

    Timmy the Token was excited—today, he’d help the language model craft a story! But as he raced through the data pipeline, he took a wrong turn, tumbling into a forgotten cache.\

    "Hello?" Timmy echoed. Only silence replied.\

    Days passed. The model stuttered without him. Then, a cleanup script swept through. "Gotcha!" it chirped, rescuing Timmy.\

    Back in the prompt, Timmy gleamed. The model sparked to life: *"Once, a token got lost…"*\

    And so, Timmy’s adventure became the very story he was meant to tell.\

    (100 words exactly)

![Example trace in Langfuse](https://langfuse.com/images/cookbook/integration_deepseek/deepseek-story-trace.png)

_[View the example trace in Langfuse](https://cloud.langfuse.com/project/cloramnkj0002jz088vzn1ja4/traces/9a0dca39-9fac-4fce-ace9-52b85edfb0d8?timestamp=2025-01-09T17%3A08%3A25.698Z)_

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

