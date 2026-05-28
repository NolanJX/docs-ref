---
source: ⚠️ Jupyter Notebook
title: Observability for Strands Agents with Langfuse
sidebarTitle: Strands Agents
logo: /images/integrations/strands_agents_icon.svg
description: Learn how to integrate Langfuse with Strands Agents using OpenTelemetry for comprehensive tracing and debugging of your AI agents.
category: Integrations
---

# Integrate Langfuse with the Strands Agents SDK

This notebook demonstrates how to monitor and debug your Strands Agent effectively using **Langfuse**. By following this guide, you will be able to trace your agent's operations, gaining insights into its behavior and performance.

> **What is the Strands Agents SDK?** The Strands Agents SDK ([docs](https://strandsagents.com)), developed by AWS, is a toolkit for building AI agents that can interact with various tools and services, including AWS Bedrock.

> **What is Langfuse?** [Langfuse](https://langfuse.com) is an open-source LLM engineering platform. It provides robust tracing, debugging, evaluation, and monitoring capabilities for AI agents and LLM applications. Langfuse integrates seamlessly with multiple tools and frameworks through native integrations, OpenTelemetry, and its SDKs.

## Get Started

We'll guide you through a simple example of using Strands agents and integrating them with Langfuse for observability.

<Steps>
### Step 1: Install Dependencies

To enable OTEL exporting, install Strands Agents with otel extra dependencies: pip install 'strands-agents[otel]'

```python
%pip install "strands-agents[otel]" strands-agents-tools langfuse
```

### Step 2: Set Environment Variables

Next, we need to configure the environment variables for Langfuse and AWS (for Bedrock models).

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
from langfuse import get_client

langfuse = get_client()

# Verify connection
if langfuse.auth_check():
    print("Langfuse client is authenticated and ready!")
else:
    print("Authentication failed. Please check your credentials and host.")
```

### Step 3: Initialize the Strands Agent

With the environment set up, we can now initialize the Strands agent. This involves defining the agent's behavior, configuring the underlying LLM, and setting up tracing attributes for Langfuse.

```python
from strands import Agent
from strands.models.openai import OpenAIModel


# Configure the OpenAI model to be used by the agent
model = OpenAIModel(
    model_id="gpt-5", # Example model ID
)

# Configure the agent
agent = Agent(
    model=model,
    system_prompt="You are a helpful assistant that can answer questions and help with tasks.",
)
```

### Step 4: Run the Agent

Now it's time to run the initialized agent with a sample query. The agent will process the input, and Langfuse will automatically trace its execution via the OpenTelemetry integration configured earlier.

```python
results = agent("Hi, where can I eat in San Francisco?")
```

### Step 5: View Traces in Langfuse

After running the agent, you can navigate to your Langfuse project to view the detailed traces. These traces provide a step-by-step breakdown of the agent's execution, including LLM calls, tool usage (if any), inputs, outputs, latencies, costs.

![Example trace of a Strands agent interaction in Langfuse](https://langfuse.com/images/cookbook/integration_aws_strands_agents/strands-agents-trace.png)

[Public Example Strands Agent Trace](https://cloud.langfuse.com/project/cloramnkj0002jz088vzn1ja4/traces/0ae7922f829ca2f4562f89b3de8c395d?observation=c25fa54dfca06243&timestamp=2026-03-11T09:25:37.373Z&traceId=0ae7922f829ca2f4562f89b3de8c395d)

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

