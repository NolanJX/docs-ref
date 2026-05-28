---
source: ⚠️ Jupyter Notebook
title: Trace the Microsoft Agent Framework with Langfuse
sidebarTitle: Microsoft Agent Framework
logo: /images/integrations/microsoft_icon.svg
description: Learn how to use Langfuse to monitor Microsoft Agent Framework to debug and evaluate your AI agents
category: Integrations
---

# Trace the Microsoft Agent Framework with Langfuse

This notebook demonstrates how to **integrate Langfuse** into your **Microsoft Agent Framework** workflow to monitor, debug and evaluate your AI agents.

> **What is the Microsoft Agent Framework?**: The [Microsoft Agent Framework](https://github.com/microsoft/agent-framework) is an open-source framework for building intelligent agents. It provides a comprehensive set of tools for creating agents that can interact with various services, execute tasks, and handle complex workflows. The framework supports multiple LLM providers including Azure OpenAI and OpenAI, and offers built-in observability through OpenTelemetry.

> **What is Langfuse?**: [Langfuse](https://langfuse.com/) is an open-source observability platform for AI agents. It helps you visualize and monitor LLM calls, tool usage, cost, latency, and more.

## 1. Install Dependencies

Below we install the `agent-framework` library (the Microsoft Agent Framework) and `langfuse` for observability.

```python
%pip install agent-framework langfuse --pre
```

## 2. Configure Environment & Langfuse Credentials

Next, set up your Langfuse API keys. You can get these keys by signing up for a free [Langfuse Cloud](https://langfuse.com/cloud) account or by [self-hosting Langfuse](https://langfuse.com/self-hosting). These environment variables are essential for the Langfuse client to authenticate and send data to your Langfuse project.

```python
import os

# Get keys for your project from the project settings page: https://cloud.langfuse.com
os.environ["LANGFUSE_PUBLIC_KEY"] = "pk-lf-..."
os.environ["LANGFUSE_SECRET_KEY"] = "sk-lf-..."
os.environ["LANGFUSE_BASE_URL"] = "https://cloud.langfuse.com" # 🇪🇺 EU region
# Other Langfuse data regions include 🇺🇸 US: https://us.cloud.langfuse.com, 🇯🇵 Japan: https://jp.cloud.langfuse.com and ⚕️ HIPAA: https://hipaa.cloud.langfuse.com

# Your Azure OpenAI credentials
os.environ["AZURE_OPENAI_API_KEY"] = "your-azure-openai-key"
os.environ["AZURE_OPENAI_ENDPOINT"] = "https://your-resource.openai.azure.com/"
os.environ["AZURE_OPENAI_CHAT_DEPLOYMENT_NAME"] = "gpt-5-mini"
os.environ["OPENAI_CHAT_MODEL_ID"] = "gpt-5-mini"
```

## 3. Initialize Langfuse Client

Initialize the Langfuse client to verify the connection. `get_client()` initializes the Langfuse client using the credentials provided in the environment variables.

```python
from langfuse import get_client

langfuse = get_client()

# Verify connection
if langfuse.auth_check():
    print("Langfuse client is authenticated and ready!")
else:
    print("Authentication failed. Please check your credentials and host.")
```

## 4. Enable Observability

The Microsoft Agent Framework includes built-in observability support through OpenTelemetry. Enable it by calling [`configure_otel_providers()`](https://learn.microsoft.com/en-us/agent-framework/user-guide/observability?pivots=programming-language-python#1-standard-opentelemetry-environment-variables-recommended) which automatically exports traces to Langfuse.

**Note:** Set `enable_sensitive_data=True` to capture full request/response data including function arguments and results.

```python
from agent_framework.observability import configure_otel_providers

configure_otel_providers(enable_sensitive_data=True)
```

## 5. Hello World Example with Tool

Below we create a **weather agent** using the Microsoft Agent Framework with Azure OpenAI. The agent has access to a `get_weather` function tool that it can call to retrieve weather information.

```python
import asyncio
from random import randint
from typing import Annotated
from agent_framework.azure import AzureOpenAIChatClient
from pydantic import Field

def get_weather(
    location: Annotated[str, Field(description="The location to get the weather for.")],
) -> str:
    """Get the weather for a given location."""
    conditions = ["sunny", "cloudy", "rainy", "stormy"]
    return f"The weather in {location} is {conditions[randint(0, 3)]} with a high of {randint(10, 30)}°C."

async def main():
    # Create an agent with Azure OpenAI
    async with AzureOpenAIChatClient().create_agent(
        instructions="You are a helpful weather agent.",
        tools=get_weather,
    ) as agent:
        query = "What's the weather like in Seattle?"
        print(f"User: {query}")
        result = await agent.run(query)
        print(f"Agent: {result}\n")

# Run the agent
await main()
```

## 6. Using OpenAI Directly

The Microsoft Agent Framework also supports using OpenAI directly (not through Azure). Simply use `OpenAIResponsesClient` instead of `AzureOpenAIChatClient`.

```python
# Required for OpenAI API access
os.environ["OPENAI_API_KEY"]="sk-proj-..."
os.environ["OPENAI_RESPONSES_MODEL_ID"]="gpt-5-mini"
```

```python
from agent_framework.openai import OpenAIResponsesClient
from typing import Annotated
from pydantic import Field
from random import randint

def get_weather(
    location: Annotated[str, Field(description="The location to get the weather for.")],
) -> str:
    """Get the weather for a given location."""
    conditions = ["sunny", "cloudy", "rainy", "stormy"]
    return f"The weather in {location} is {conditions[randint(0, 3)]} with a high of {randint(10, 30)}°C."

async def main():
    async with OpenAIResponsesClient().create_agent(
        instructions="You are a helpful assistant.",
        tools=get_weather,
    ) as agent:
        query = "What's the weather in Tokyo?"
        print(f"User: {query}")
        result = await agent.run(query)
        print(f"Agent: {result}\n")

await main()
```

## 7. See Traces in Langfuse

![Example trace in Langfuse](https://langfuse.com/images/cookbook/integration_microsoft-agent-framework/microsoft-agent-framework-example-trace.png)

[Example trace in Langfuse](https://cloud.langfuse.com/project/cloramnkj0002jz088vzn1ja4/traces/8e419d3288419b5d944270505640e183?observation=2406e8343fd49c0e&timestamp=2025-12-17T09:37:37.258Z)

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

