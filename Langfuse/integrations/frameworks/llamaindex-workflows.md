---
source: Jupyter Notebook
title: Observability for LlamaIndex with Langfuse Integration
sidebarTitle: LlamaIndex Workflows
description: Discover how to integrate Langfuse with LlamaIndex for enhanced LLM application monitoring, debugging, and tracing. Improve your AI development workflow today.
category: Integrations
logo: /images/integrations/llamaindex_icon.png
---

# Observability for LlamaIndex Workflows

This cookbook demonstrates how to use [Langfuse](https://langfuse.com) to gain real-time observability for your [LlamaIndex Workflows](https://docs.llamaindex.ai/en/stable/module_guides/workflow/).

> **What are LlamaIndex Workflows?** [LlamaIndex Workflows](https://docs.llamaindex.ai/en/stable/module_guides/workflow/) is a flexible, event-driven framework designed to build robust AI agents. In LlamaIndex, workflows are created by chaining together multiple steps—each defined and validated using the `@step` decorator. Every step processes specific event types, allowing you to orchestrate complex processes such as AI agent collaboration, RAG flows, data extraction, and more.

> **What is Langfuse?** [Langfuse](https://langfuse.com) is the open source LLM engineering platform. It helps teams to collaboratively manage prompts, trace applications, debug problems, and evaluate their LLM system in production.

## Get Started

We'll walk through a simple example of using LlamaIndex Workflows and integrating it with Langfuse.

<Steps>
### Step 1: Install Dependencies

```python
%pip install langfuse openai llama-index-workflows llama-index-core llama-index-llms-openai openinference-instrumentation-llama_index llama-index-instrumentation
```

### Step 2: Set Up Environment Variables

Configure your Langfuse API keys. You can get them by signing up for [Langfuse Cloud](https://cloud.langfuse.com) or [self-hosting Langfuse](https://langfuse.com/self-hosting).

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

### Step 3: Initialize LlamaIndex Instrumentation

Now, we initialize the [OpenInference LlamaIndex instrumentation](https://docs.arize.com/phoenix/tracing/integrations-tracing/llamaindex). This third-party instrumentation automatically captures LlamaIndex operations and exports OpenTelemetry (OTel) spans to Langfuse.

```python
from openinference.instrumentation.llama_index import LlamaIndexInstrumentor

# Initialize LlamaIndex instrumentation
LlamaIndexInstrumentor().instrument()
```

### Step 4: Create a Simple LlamaIndex Workflows Application

In LlamaIndex Workflows, you build event-driven AI agents by defining steps with the `@step` decorator. Each step processes an event and, if appropriate, emits new events. In this example, we create a simple workflow with two steps: one that pre-processes an incoming event and another that generates a reply.

```python
from llama_index.core.llms import ChatMessage
from llama_index.llms.openai import OpenAI
from typing import Annotated

from workflows import Workflow, step
from workflows.events import StartEvent, StopEvent
from workflows.resource import Resource


def get_llm(**kwargs):
    return OpenAI(model="gpt-4.1-mini")


class MyWorkflow(Workflow):
    @step
    async def step1(
        self, ev: StartEvent, llm: Annotated[OpenAI, Resource(get_llm)]
    ) -> StopEvent:
        msg = ChatMessage(role="user", content=ev.get("input"))
        response = await llm.achat([msg])
        return StopEvent(result=response.message.content)


w = MyWorkflow()
```

```python
response = await w.run(input="Hello, what is Langfuse?")
print(response)
```

### Step 5: View Traces in Langfuse

After running your workflow, log in to [Langfuse](https://cloud.langfuse.com) to explore the generated traces. You will see logs for each workflow step along with metrics such as token counts, latencies, and execution paths.

![Langfuse Trace Example](https://langfuse.com/images/cookbook/integration-llamaindex-workflows/llamaindex-workflows-example-trace.png)

_[Public example trace in Langfuse](https://cloud.langfuse.com/project/cloramnkj0002jz088vzn1ja4/traces/e0987dce85c8c49602030599b84e77e8?timestamp=2025-07-01T09%3A42%3A54.701Z&display=details)_

</Steps>

_**Note:** To add additional trace attributes like tags or metadata or use LlamaIndex Workflows together with other Langfuse features please refer to [this guide](https://langfuse.com/integrations/frameworks/llamaindex)._

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

## References

- [LlamaIndex Workflows Documentation](https://docs.llamaindex.ai/en/stable/module_guides/workflow/)\
