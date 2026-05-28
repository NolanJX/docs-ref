---
source: ⚠️ Jupyter Notebook
title: Observability for BeeAI Agents with Langfuse
sidebarTitle: BeeAI
logo: /images/integrations/beeai_icon.png
description: Learn how to integrate Langfuse with the BeeAI Framework for comprehensive tracing and debugging of your AI agent applications.
category: Integrations
---

# Trace BeeAI Agents in Langfuse

This notebook shows how to trace and observe BeeAI Framework applications with Langfuse using OpenTelemetry instrumentation.

> **What is BeeAI?** [BeeAI Framework](https://beeai.dev/) is a comprehensive toolkit, developed by [IBM Research](https://research.ibm.com/blog/bee-ai-app), for building intelligent, autonomous agents and multi-agent systems. It provides everything you need to create agents that can reason, take actions, and collaborate to solve complex problems in both Python and TypeScript.

> **What is Langfuse?** [Langfuse](https://langfuse.com) is an open source platform for LLM observability and monitoring. It helps you trace and monitor your AI applications by capturing metadata, prompt details, token usage, latency, and more.

<Steps>
## Step 1: Install Dependencies

Before you begin, install the necessary packages in your Python environment:

```python
%pip install beeai-framework langfuse openinference-instrumentation-beeai "beeai-framework[wikipedia]"
```

## Step 2: Configure Langfuse SDK

Next, set up your Langfuse API keys. You can get these keys by signing up for a free [Langfuse Cloud](https://langfuse.com/cloud) account or by [self-hosting Langfuse](https://langfuse.com/self-hosting). These environment variables are essential for the Langfuse client to authenticate and send data to your Langfuse project.

You will also need to configure your LLM provider credentials. BeeAI Framework supports multiple providers including OpenAI, Ollama, watsonx.ai, and others.

```python
import os

# Get keys for your project from the project settings page: https://cloud.langfuse.com
os.environ["LANGFUSE_PUBLIC_KEY"] = "pk-lf-..."
os.environ["LANGFUSE_SECRET_KEY"] = "sk-lf-..."
os.environ["LANGFUSE_BASE_URL"] = "https://cloud.langfuse.com" # 🇪🇺 EU region
# Other Langfuse data regions include 🇺🇸 US: https://us.cloud.langfuse.com, 🇯🇵 Japan: https://jp.cloud.langfuse.com and ⚕️ HIPAA: https://hipaa.cloud.langfuse.com

# Your LLM provider API key (example with OpenAI, adjust for your provider)
os.environ["OPENAI_API_KEY"] = "sk-proj-..."
# For Ollama (local), no API key needed
# For other providers, set appropriate environment variables

```

With the environment variables set, we can now initialize the Langfuse client. `get_client()` initializes the Langfuse client using the credentials provided in the environment variables.

```python
from langfuse import get_client

# Initialise Langfuse client and verify connectivity
langfuse = get_client()
assert langfuse.auth_check(), "Langfuse auth failed - check your keys ✋"

```

## Step 3: OpenTelemetry Instrumentation

Use the [`BeeAIInstrumentor`](https://github.com/Arize-ai/openinference/tree/main/python/instrumentation/openinference-instrumentation-beeai) library to wrap [BeeAI Framework](https://framework.beeai.dev) calls and send OpenTelemetry spans to Langfuse.

```python
from openinference.instrumentation.beeai import BeeAIInstrumentor

BeeAIInstrumentor().instrument()
```

## Step 4: Run an Example

```python
import asyncio
from beeai_framework.agents.react import ReActAgent
from beeai_framework.agents.types import AgentExecutionConfig
from beeai_framework.backend.chat import ChatModel
from beeai_framework.backend.types import ChatModelParameters
from beeai_framework.memory import TokenMemory
from beeai_framework.tools.search.wikipedia import WikipediaTool
from beeai_framework.tools.weather.openmeteo import OpenMeteoTool

# Initialize the language model
llm = ChatModel.from_name(
    "openai:gpt-4o-mini",  # or "ollama:granite3.3:8b" for local Ollama
    ChatModelParameters(temperature=0.7),
)

# Create tools for the agent
tools = [
    WikipediaTool(),
    OpenMeteoTool(),
]

# Create a ReAct agent with memory
agent = ReActAgent(
    llm=llm,
    tools=tools,
    memory=TokenMemory(llm)
)

# Run the agent
async def main():
    response = await agent.run(
        prompt="I'm planning a trip to Barcelona, Spain. Can you research key attractions and landmarks I should visit, and also tell me what the current weather conditions are like there?",
        execution=AgentExecutionConfig(
            max_retries_per_step=3,
            total_max_retries=10,
            max_iterations=5
        ),
    )
    print("Agent Response:", response.result.text)
    return response

# Run the example
response = await main()

```

### View Traces in Langfuse

After executing the application, navigate to your Langfuse Trace Table. You will find detailed traces of the application's execution, providing insights into the agent conversations, tool calls, LLM interactions, inputs, outputs, and performance metrics.

![Langfuse Trace](https://langfuse.com/images/cookbook/integration_beeai/beeai-trace.png)

[View trace in Langfuse](https://cloud.langfuse.com/project/cloramnkj0002jz088vzn1ja4/traces/096fc09a30ab90d2431778f9ee2b3936?timestamp=2025-08-01T13%3A56%3A54.163Z&display=details)

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

