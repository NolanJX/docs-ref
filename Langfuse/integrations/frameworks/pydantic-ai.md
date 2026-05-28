---
source: ⚠️ Jupyter Notebook
title: Observability for Pydantic AI with Langfuse Integration
sidebarTitle: Pydantic AI
logo: /images/integrations/pydantic_ai_icon.svg
description: Discover how to integrate Langfuse with Pydantic AI for enhanced LLM application monitoring, debugging, and tracing. Improve your AI development workflow today.
category: Integrations
---

# Integrate Langfuse with Pydantic AI

This notebook provides a step-by-step guide on integrating **Langfuse** with **Pydantic AI** to achieve observability and debugging for your LLM applications.

> **About PydanticAI:** [PydanticAI](https://ai.pydantic.dev/) is a Python agent framework designed to simplify the development of production-grade generative AI applications. It brings the same type-safety, ergonomic API design, and developer experience found in FastAPI to the world of GenAI app development.

> **What is Langfuse?** [Langfuse](https://langfuse.com) is an open-source LLM engineering platform. It offers tracing and monitoring capabilities for AI applications. Langfuse helps developers debug, analyze, and optimize their AI systems by providing detailed insights and integrating with a wide array of tools and Pydantic AIs through native integrations, OpenTelemetry, and dedicated SDKs.

## Getting Started

Let's walk through a practical example of using Pydantic AI and integrating it with Langfuse for comprehensive tracing.

<Steps>
### Step 1: Install Dependencies

```python
%pip install langfuse pydantic-ai -U
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

### Step 3: Initialize Pydantic AI Instrumentation

Now, we initialize the [Pydantic AI Instrumentation](https://ai.pydantic.dev/logfire/#logfire-with-an-alternative-otel-backend). This automatically captures Pydantic AI operations and exports OpenTelemetry (OTel) spans to Langfuse.

```python
from pydantic_ai.agent import Agent

# Initialize Pydantic AI instrumentation
Agent.instrument_all()
```

### Step 4: Basic Pydantic AI Application

Finally, run your Pydantic AI agent and generate trace data that will be sent to Langfuse. In the example below, the agent is executed with a dependency value (the winning square) and natural language input. The output from the tool function is then printed.

Make sure to pass `instrument=True` while configuring the `Agent`.

```python
from pydantic_ai import Agent, RunContext

roulette_agent = Agent(
    'openai:gpt-4o',
    deps_type=int,
    system_prompt=(
        'Use the `roulette_wheel` function to see if the '
        'customer has won based on the number they provide.'
    ),
    instrument=True
)

@roulette_agent.tool
async def roulette_wheel(ctx: RunContext[int], square: int) -> str:
    """check if the square is a winner"""
    return 'winner' if square == ctx.deps else 'loser'
```

```python
# Run the agent - using await since we're in a Jupyter notebook
success_number = 18
result = await roulette_agent.run('Put my money on square eighteen', deps=success_number)
print(result.output)
```

### Step 5: View Traces in Langfuse

After executing the application, navigate to your [Langfuse Trace Table](https://cloud.langfuse.com). You will find detailed traces of the application's execution, providing insights into the LLM calls, retrieval operations, inputs, outputs, and performance metrics.

![Example Trace in Langfuse](https://langfuse.com/images/cookbook/otel-integration-pydantic-ai/pydanticai-openai-trace-tree.png)

[Example Trace in Langfuse](https://cloud.langfuse.com/project/cloramnkj0002jz088vzn1ja4/traces/25f4bdeebaab60e6e1bee7e8469554bc?display=details%3Ftimestamp%3D2025-06-06T14%3A39%3A55.786Z%3Ftimestamp%3D2025-06-06T14%3A37%3A30.656Z%3Ftimestamp%3D2025-06-06T14%3A39%3A55.786Z?timestamp=2025-06-06T14%3A40%3A28.562Z)

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

