---
source: ⚠️ Jupyter Notebook
title: Observability for AutoGen with Langfuse Integration
sidebarTitle: AutoGen
logo: /images/integrations/autogen_icon.svg
description: Learn how to integrate Langfuse with AutoGen via OpenTelemetry using OpenLit for comprehensive tracing and debugging of your AI agent conversations.
category: Integrations
---

# Integrate Langfuse with AutoGen

This notebook provides a step-by-step guide on integrating **Langfuse** with **AutoGen** to achieve comprehensive observability and debugging for your multi-agent LLM applications.

> **What is AutoGen?** [AutoGen](https://microsoft.github.io/autogen/stable/) ([GitHub](https://github.com/microsoft/autogen)) is an open-source framework developed by Microsoft for building LLM applications, including agents capable of complex reasoning and interactions. AutoGen simplifies the creation of conversational agents that can collaborate or compete to solve tasks.

> **What is Langfuse?** [Langfuse](https://langfuse.com) is an open-source LLM engineering platform. It offers tracing and monitoring capabilities for AI applications. Langfuse helps developers debug, analyze, and optimize their AI systems by providing detailed insights and integrating with a wide array of tools and frameworks through native integrations, OpenTelemetry, and dedicated SDKs.

## Getting Started

Let's walk through a practical example of using AutoGen and integrating it with Langfuse via OpenTelemetry for comprehensive tracing.

<Steps>
### Step 1: Install Dependencies

```python
%pip install langfuse openlit "autogen-agentchat" "autogen-ext[openai]" -U
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

# Your openai key
os.environ["OPENAI_API_KEY"] = "sk-proj-..."
```

With the environment variables set, we can now initialize the Langfuse client. `get_client()` initializes the Langfuse client using the credentials provided in the environment variables.

```python
from langfuse import Langfuse
from langfuse.span_filter import is_default_export_span

# Filter out AutoGen runtime spans
blocked_scopes = {"autogen SingleThreadedAgentRuntime"}
langfuse = Langfuse(
    should_export_span=lambda span: (
        is_default_export_span(span)
        and not (
            span.instrumentation_scope is not None
            and span.instrumentation_scope.name in blocked_scopes
        )
    )
)

# Verify connection
if langfuse.auth_check():
    print("Langfuse client is authenticated and ready!")
else:
    print("Authentication failed. Please check your credentials and host.")
```

    Langfuse client is authenticated and ready!

### Step 3: Initialize OpenLit Instrumentation

Now, we initialize the [OpenLit](https://github.com/openlit/openlit) instrumentation. OpenLit automatically captures AutoGen operations and exports OpenTelemetry (OTel) spans to Langfuse.

```python
import openlit

# Initialize OpenLIT instrumentation. The disable_batch flag is set to true to process traces immediately.
openlit.init(tracer=langfuse._otel_tracer, disable_batch=True)
```

### Step 5: Basic AutoGen Application

Let's create a straightforward AutoGen application. In this example, we'll create a simple multi-agent conversation where an Assistant agent answers a user's question. This will serve as the foundation for demonstrating Langfuse tracing.

```python
from autogen_agentchat.agents import AssistantAgent
from autogen_ext.models.openai import OpenAIChatCompletionClient

model_client = OpenAIChatCompletionClient(model="gpt-4o")
agent = AssistantAgent("assistant", model_client=model_client)
print(await agent.run(task="Say 'Hello World!'"))
await model_client.close()
```

    messages=[TextMessage(id='0935120c-2a27-4bb8-ad7f-eb5cb2b4902d', source='user', models_usage=None, metadata={}, created_at=datetime.datetime(2025, 7, 8, 8, 32, 25, 422043, tzinfo=datetime.timezone.utc), content="Say 'Hello World!'", type='TextMessage'), TextMessage(id='2046975a-235c-4e46-be55-719bb79ced95', source='assistant', models_usage=RequestUsage(prompt_tokens=41, completion_tokens=5), metadata={}, created_at=datetime.datetime(2025, 7, 8, 8, 32, 26, 265424, tzinfo=datetime.timezone.utc), content='Hello World! TERMINATE', type='TextMessage')] stop_reason=None

### Step 6: View Traces in Langfuse

After executing the application, navigate to your Langfuse Trace Table. You will find detailed traces of the application's execution, providing insights into the agent conversations, LLM calls, inputs, outputs, and performance metrics. The trace will show the complete flow from the initial user query through the agent interactions to the final response.

![Langfuse Trace](https://langfuse.com/images/cookbook/integration-autogen/autogen-example-trace.png)

You can also view the public trace here: [Langfuse Trace Example](https://cloud.langfuse.com/project/cloramnkj0002jz088vzn1ja4/traces/1b72c51fabed12ae7df83bfd4a09f545?timestamp=2025-06-06T11:31:33.965Z&display=details)

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

