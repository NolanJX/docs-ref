---
source: ⚠️ Jupyter Notebook
title: Observability for Claude Agent SDK with Langfuse
sidebarTitle: Claude Agent SDK
logo: /images/integrations/claude_icon.png
description: Learn how to trace Claude Agent SDK applications with Langfuse via OpenTelemetry
category: Integrations
---

# Integrate Langfuse with Claude Agent SDK

<a href="https://langfuse.com/integrations/frameworks/claude-agent-sdk"><img className="inline" alt="Python" src="https://img.shields.io/badge/Python-3776AB?style=flat&logo=python&logoColor=white" /></a> <a href="https://langfuse.com/integrations/frameworks/claude-agent-sdk-js"><img className="inline" alt="JS/TS" src="https://img.shields.io/badge/JS/TS-d4d4d8?style=flat&logo=javascript&logoColor=white" /></a>

This notebook demonstrates how to capture detailed traces from the [Claude Agent SDK](https://github.com/anthropics/claude-agent-sdk-python) with **[Langfuse](https://langfuse.com)** using OpenTelemetry.

> **What is Claude Agent SDK?**\
> The [Claude Agent SDK](https://platform.claude.com/docs/en/agent-sdk/overview) is Anthropic's open-source framework for building AI agents. It provides a clean API for creating tool-using agents, including native support for MCP.

> **What is Langfuse?**\
> [Langfuse](https://langfuse.com) is the open source LLM engineering platform. It provides detailed tracing, monitoring, and analytics for every prompt, model response, and tool call in your agent, making it easy to debug, evaluate, and iterate on LLM applications.

<Steps>
## Step 1: Install Dependencies

```python
%pip install langfuse claude-agent-sdk openinference-instrumentation-claude-agent-sdk -q
```

## Step 2: Set Up Environment Variables

Set up your **Langfuse** API keys ([Langfuse Cloud](https://cloud.langfuse.com) or [self-hosted](https://langfuse.com/self-hosting)) and your **Anthropic** API key ([Anthropic Console](https://console.anthropic.com/)).

```python
import os

# Get keys for your project from the project settings page: https://cloud.langfuse.com
os.environ["LANGFUSE_PUBLIC_KEY"] = "pk-lf-***"
os.environ["LANGFUSE_SECRET_KEY"] = "sk-lf-***"
os.environ["LANGFUSE_BASE_URL"] = "https://cloud.langfuse.com" # 🇪🇺 EU region
# Other Langfuse data regions include 🇺🇸 US: https://us.cloud.langfuse.com, 🇯🇵 Japan: https://jp.cloud.langfuse.com and ⚕️ HIPAA: https://hipaa.cloud.langfuse.com

# Your Anthropic API key
os.environ["ANTHROPIC_API_KEY"] = "sk-ant-***"
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

## Step 3: OpenTelemetry Instrumentation

Use [OpenInference's Claude Agent SDK instrumentation library](https://github.com/Arize-ai/openinference/tree/main/python/instrumentation/openinference-instrumentation-claude-agent-sdk) to instrument the agent SDK and send OpenTelemetry spans to Langfuse.

```python
from openinference.instrumentation.claude_agent_sdk import ClaudeAgentSDKInstrumentor

ClaudeAgentSDKInstrumentor().instrument()
```

## Step 4: Build a Hello World Agent

Every tool call and model completion is captured as an OpenTelemetry span and forwarded to Langfuse.

```python
import asyncio
from claude_agent_sdk import (
    ClaudeAgentOptions,
    ClaudeSDKClient,
    tool,
    create_sdk_mcp_server,
)
from typing import Any

@tool(
    "get_weather",
    "Gets the current weather for a given city",
    {
        "city": str,
    },
)
async def get_weather(args: dict[str, Any]) -> dict[str, Any]:
    """Simulated weather lookup tool"""
    city = args["city"]

    # Simulated weather data
    weather_data = {
        "Berlin": "Cloudy, 59°F",
        "New York": "Sunny, 75°F",
    }

    weather = weather_data.get(city, "Weather data not available")
    return {"content": [{"type": "text", "text": f"Weather in {city}: {weather}"}]}


async def main():
    # Create SDK MCP server with the weather tool
    weather_server = create_sdk_mcp_server(
        name="weather",
        version="1.0.0",
        tools=[get_weather],
    )

    options = ClaudeAgentOptions(
        model="claude-sonnet-4-5-20250929",
        system_prompt="You are a friendly travel assistant who helps with weather information.",
        mcp_servers={"weather": weather_server},
        allowed_tools=["mcp__weather__get_weather"],
    )

    async with ClaudeSDKClient(options=options) as client:
        await client.query("What's the weather like in Berlin and New York?")

        async for message in client.receive_response():
            print(message)


await main()
```

## Step 5: View the Trace in Langfuse

Head over to your **Langfuse dashboard → Traces**. You should see traces including all tool calls and model inputs/outputs.

![Claude Agent SDK example trace in Langfuse](https://langfuse.com/images/cookbook/integration_claude_agent_sdk/claude-agent-sdk-example-trace.png)

[Link to trace in Langfuse](https://cloud.langfuse.com/project/cloramnkj0002jz088vzn1ja4/traces/ff1e1c6309735ff53b94bffedfa98b70?observation=94fa8777d45b1545&timestamp=2026-05-11T07:49:30.913Z&traceId=ff1e1c6309735ff53b94bffedfa98b70)

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

