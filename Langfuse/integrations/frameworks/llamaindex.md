---
source: ⚠️ Jupyter Notebook
title: Observability for LlamaIndex with Langfuse Integration
sidebarTitle: LlamaIndex
logo: /images/integrations/llamaindex_icon.png
description: Discover how to integrate Langfuse with LlamaIndex for enhanced LLM application monitoring, debugging, and tracing. Improve your AI development workflow today.
category: Integrations
---

# Integrate Langfuse with LlamaIndex

This notebook provides a step-by-step guide on integrating **Langfuse** with **LlamaIndex** to achieve observability and debugging for your LLM applications.

> **What is LlamaIndex?** [LlamaIndex](https://www.llamaindex.ai/) ([GitHub](https://github.com/run-llama/llama_index)) is an advanced "data framework" tailored for augmenting LLMs with private data. It streamlines the integration of diverse data sources and formats (APIs, PDFs, docs, SQL, etc.) through versatile data connectors and structures data into indices and graphs for LLM compatibility. The platform offers a sophisticated retrieval/query interface for enriching LLM inputs with context-specific outputs.

> **What is Langfuse?** [Langfuse](https://langfuse.com) is an open-source LLM engineering platform. It offers tracing and monitoring capabilities for AI applications. Langfuse helps developers debug, analyze, and optimize their AI systems by providing detailed insights and integrating with a wide array of tools and frameworks through native integrations, OpenTelemetry, and dedicated SDKs.

## Getting Started

Let's walk through a practical example of using LlamaIndex and integrating it with Langfuse for comprehensive tracing.

<Steps>
### Step 1: Install Dependencies

```python
%pip install langfuse openinference-instrumentation-llama-index llama-index-llms-openai llama-index -U
```

### Step 2: Configure Langfuse SDK

Next, set up your Langfuse API keys. You can get these keys by signing up for a free [Langfuse Cloud](https://langfuse.com/cloud) account or by [self-hosting Langfuse](https://langfuse.com/self-hosting). These environment variables are essential for the Langfuse client to authenticate and send data to your Langfuse project.

```python
import os

# Get keys for your project from the project settings page: https://cloud.langfuse.com

os.environ["LANGFUSE_PUBLIC_KEY"] = "pk-lf-..."
os.environ["LANGFUSE_SECRET_KEY"] = "sk-lf-..."
os.environ["LANGFUSE_BASE_URL"] = "https://cloud.langfuse.com" # 🇪🇺 EU region
# Other Langfuse data regions include 🇺🇸 US: https://us.cloud.langfuse.com, 🇯🇵 Japan: https://jp.cloud.langfuse.com and ⚕️ HIPAA: https://hipaa.cloud.langfuse.com

# Your OpenAI key
os.environ["OPENAI_API_KEY"] = "sk-proj-..."

```

With the environment variables set, we can now initialize the Langfuse client. `get_client()` initializes the Langfuse client using the credentials provided in the environment variables.

```python
from langfuse import get_client

langfuse = get_client()

# Verify connection
if langfuse.auth_check():
    print("Langfuse client is authenticated and ready!")
else:
    print("Authentication failed. Please check your credentials and host.")

```

### Step 3: Initialize LlamaIndex Instrumentation

Now, we initialize the [OpenInference LlamaIndex instrumentation](https://docs.arize.com/phoenix/tracing/integrations-tracing/llamaindex). This third-party instrumentation automatically captures LlamaIndex operations and exports OpenTelemetry (OTel) spans to Langfuse.

```python
from openinference.instrumentation.llama_index import LlamaIndexInstrumentor

# Initialize LlamaIndex instrumentation
LlamaIndexInstrumentor().instrument()
```

### Step 4: Basic LlamaIndex Application

Let's create a straightforward LlamaIndex application. In this example, we'll create a simple query engine that can answer questions. This will serve as the foundation for demonstrating Langfuse tracing.

```python
from llama_index.llms.openai import OpenAI

llm = OpenAI(model="gpt-4o")

with langfuse.start_as_current_observation(as_type="span", name="llama-index-trace"):
    response = llm.complete("Hello, world!")
    print(response)

langfuse.flush()
```

### Step 5: View Traces in Langfuse

After executing the application, navigate to your Langfuse Trace Table. You will find detailed traces of the application's execution, providing insights into the LLM calls, retrieval operations, inputs, outputs, and performance metrics. The trace will show the complete flow from query processing through document retrieval to response generation.

![Example Trace in Langfuse](https://langfuse.com/images/cookbook/integration_llama-index/llama-index-example-trace.png)

[Example Trace in Langfuse](https://cloud.langfuse.com/project/cloramnkj0002jz088vzn1ja4/traces/12ea412956f99347b0503c1144acd0ec?timestamp=2025-06-05T15:45:52.971Z&display=details)

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

