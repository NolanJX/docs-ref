---
source: ⚠️ Jupyter Notebook
title: Observability for Hugging Face Models with Langfuse Integration
sidebarTitle: Hugging Face
logo: /images/integrations/huggingface_icon.svg
description: Example notebook on how to monitor Hugging Face models with Langfuse using the OpenAI SDK
category: Integrations
---

# Cookbook: Monitor 🤗 Hugging Face Models with 🪢 Langfuse

This cookbook shows you how to monitor Hugging Face models using the OpenAI SDK integration with [Langfuse](https://langfuse.com). This allows you to collaboratively debug, monitor and evaluate your LLM applications.

With this integration, you can test and evaluate different models, monitor your application's cost and assign scores such as user feedback or human annotations.

**Note**: *In this example, we use the OpenAI SDK to [access the Hugging Face inference APIs](https://huggingface.co/blog/tgi-messages-api). You can also use other frameworks, such as [Langchain](https://langfuse.com/integrations/frameworks/langchain), or ingest the data via our [API](https://api.reference.langfuse.com/).*

## Setup

### Install Required Packages

```python
%pip install langfuse openai --upgrade
```

### Set Environment Variables

Set up your environment variables with the necessary keys. Get keys for your Langfuse project from [Langfuse Cloud](https://cloud.langfuse.com). Also, obtain an access token from [Hugging Face](https://huggingface.co/settings/tokens).

```python
import os

# Get keys for your project from https://cloud.langfuse.com
os.environ["LANGFUSE_SECRET_KEY"] = "sk-lf-..." # Private Project
os.environ["LANGFUSE_PUBLIC_KEY"] = "pk-lf-..." # Private Project
os.environ["LANGFUSE_BASE_URL"] = "https://cloud.langfuse.com"  # 🇪🇺 EU region
# Other Langfuse data regions include 🇺🇸 US: https://us.cloud.langfuse.com, 🇯🇵 Japan: https://jp.cloud.langfuse.com and ⚕️ HIPAA: https://hipaa.cloud.langfuse.com

os.environ["HUGGINGFACE_ACCESS_TOKEN"] = "hf_..."
```

### Import Necessary Modules

Instead of importing `openai` directly, import it from `langfuse.openai`. Also, import any other necessary modules.

```python
# Instead of: import openai
from langfuse.openai import OpenAI
from langfuse import observe
```

### Initialize the OpenAI Client for Hugging Face Models

Initialize the OpenAI client but point it to the Hugging Face model endpoint. You can use any model hosted on Hugging Face that supports the OpenAI API format. Replace the model URL and access token with your own.

For this example, we use the `Meta-Llama-3-8B-Instruct` model.

```python
# Initialize the OpenAI client, pointing it to the Hugging Face Inference API
client = OpenAI(
    base_url="https://api-inference.huggingface.co/models/meta-llama/Meta-Llama-3-8B-Instruct" + "/v1/",  # replace with your endpoint url
    api_key= os.getenv('HUGGINGFACE_ACCESS_TOKEN'),  # replace with your token
)
```

## Examples

### Chat Completion Request

Use the `client` to make a chat completion request to the Hugging Face model. The `model` parameter can be any identifier since the actual model is specified in the `base_url.` In this example, we set the model variable `tgi`, short for [Text Generation Inference](https://huggingface.co/docs/text-generation-inference/en/index).

```python
completion = client.chat.completions.create(
    model="model-name",
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

![Example trace in Langfuse](https://langfuse.com/images/cookbook/huggingface/huggingface-cookbook-trace-poem.png)

_[Example trace in Langfuse](https://cloud.langfuse.com/project/cloramnkj0002jz088vzn1ja4/traces/0c205096-fbd9-48b9-afa3-5837483488d8?timestamp=2025-01-09T15%3A03%3A08.365Z)_

### Observe the Request with Langfuse

By using the `OpenAI` client from `langfuse.openai`, your requests are automatically traced in Langfuse. You can also use the `@observe()` decorator to group multiple generations into a single trace.

```python
@observe()  # Decorator to automatically create a trace and nest generations
def generate_rap():
    completion = client.chat.completions.create(
        name="rap-generator",
        model="tgi",
        messages=[
            {"role": "system", "content": "You are a poet."},
            {"role": "user", "content": "Compose a rap about the open source LLM engineering platform Langfuse."}
        ],
        metadata={"category": "rap"},
    )
    return completion.choices[0].message.content

rap = generate_rap()
print(rap)
```

![Example trace in Langfuse](https://langfuse.com/images/cookbook/huggingface/huggingface-cookbook-trace-rap.png)

_[Example trace in Langfuse](https://cloud.langfuse.com/project/cloramnkj0002jz088vzn1ja4/traces/8c432652-ee56-4985-83aa-9e95945ca481?timestamp=2025-01-09T15%3A00%3A22.904Z)_

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

