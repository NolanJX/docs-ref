---
source: ⚠️ Jupyter Notebook
title: Observability for Semantic Kernel with Langfuse Integration
sidebarTitle: Semantic Kernel
logo: /images/integrations/microsoft_icon.svg
description: Discover how to integrate Langfuse with Semantic Kernel for enhanced LLM application monitoring, debugging, and tracing. Improve your AI development workflow today.
category: Integrations
---

# Integrate Langfuse with Semantic Kernel

This notebook provides a step-by-step guide on integrating **Langfuse** with **Semantic Kernel** to achieve observability and debugging for your LLM applications. By following this tutorial, you will learn how to trace your Semantic Kernel applications using Langfuse, manage your prompts in one place and evaluate your application to make it production-ready.

> **What is Semantic Kernel?** [Semantic Kernel](https://learn.microsoft.com/en-us/semantic-kernel/overview/) ([GitHub](https://github.com/microsoft/semantic-kernel)) is a powerful open-source SDK from Microsoft. It facilitates the combination of LLMs with popular programming languages like C#, Python, and Java. Semantic Kernel empowers developers to build sophisticated AI applications by seamlessly integrating AI services, data sources, and custom logic, accelerating the delivery of enterprise-grade AI solutions.

> **What is Langfuse?** [Langfuse](https://langfuse.com) is an open-source platform dedicated to LLM observability. It offers robust tracing and monitoring capabilities tailored for AI applications. Langfuse helps developers debug, analyze, and optimize their AI systems by providing detailed insights and integrating with a wide array of tools and frameworks through native integrations, OpenTelemetry, and dedicated SDKs.

_**Note:** This guide focuses on the Python implementation. However, the principles of this Langfuse integration apply to other languages supported by Semantic Kernel, including [C#](https://learn.microsoft.com/en-us/semantic-kernel/concepts/enterprise-readiness/observability/?pivots=programming-language-csharp) and [Java](https://learn.microsoft.com/en-us/semantic-kernel/concepts/enterprise-readiness/observability/?pivots=programming-language-java)._

## Getting Started

Let's walk through a practical example of using Semantic Kernel and integrating it with Langfuse for comprehensive tracing.

<Steps>
### Step 1: Install Dependencies

```python
%pip install langfuse openlit semantic-kernel
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
os.environ["OPENAI_CHAT_MODEL_ID"] = "gpt-4o"
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

    Langfuse client is authenticated and ready!

### Step 3: Initialize OpenLit for Instrumentation

Now, we initialize the [OpenLit instrumentation SDK](https://docs.openlit.io/latest/sdk-configuration). OpenLit automatically instruments Semantic Kernel and exports OpenTelemetry (OTel) spans to Langfuse.

```python
import openlit

# Initialize OpenLIT instrumentation. The disable_batch flag is set to true to process traces immediately. Also set the langfuse tracer to use the langfuse tracer.
openlit.init(tracer=langfuse._otel_tracer, disable_batch=True)
```

### Step 4: Basic Semantic Kernel Application

Let's create a straightforward Semantic Kernel application. In this example, an Assistant agent will answer a user's question. This will serve as the foundation for demonstrating Langfuse tracing.

```python
from semantic_kernel import Kernel
from semantic_kernel.connectors.ai.open_ai import OpenAIChatCompletion

kernel = Kernel()

kernel.add_service(
    OpenAIChatCompletion(),
)
```

```python
from semantic_kernel.prompt_template import InputVariable, PromptTemplateConfig

prompt = """{{$input}}
Answer the question above.
"""

prompt_template_config = PromptTemplateConfig(
    template=prompt,
    name="summarize",
    template_format="semantic-kernel",
    input_variables=[
        InputVariable(name="input", description="The user input", is_required=True),
    ]
)

summarize = kernel.add_function(
    function_name="summarizeFunc",
    plugin_name="summarizePlugin",
    prompt_template_config=prompt_template_config,
)
```

### Step 5: Run the Application

With the Semantic Kernel application set up, let's invoke it with a sample question and print the response. OpenLit will automatically capture this interaction and send the trace data to Langfuse.

```python
input_text = "What is Langfuse?"

summary = await kernel.invoke(summarize, input=input_text)

print(summary)
```

### Step 6: View Traces in Langfuse

After executing the application, navigate to your Langfuse Trace Table. You will find detailed traces of the application's execution, providing insights into the LLM calls, inputs, outputs, and performance metrics. Below is an example screenshot illustrating how a trace appears in Langfuse:

![Langfuse Trace of Semantic Kernel Application](https://langfuse.com/images/cookbook/integration-semantic-kernel/sematric-kernel-example-trace.png)

For a live example, you can explore this public trace: [Langfuse Semantic Kernel Trace Example](https://cloud.langfuse.com/project/cloramnkj0002jz088vzn1ja4/traces/51d7ea51af5cf9048f607ac6abb79b4f?timestamp=2025-06-04T08:17:14.026Z&display=details)

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

