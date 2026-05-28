---
title: Observability for LiveKit Agents with Langfuse
sidebarTitle: LiveKit
logo: /images/integrations/livekit_icon.svg
description: Trace real-time voice AI agents and multimodal conversations built with LiveKit Agents using Langfuse.
---

# Trace LiveKit Agents with Langfuse

This guide shows you how to integrate **Langfuse** with **LiveKit Agents** for observability and tracing of real-time voice AI applications. By following these steps, you'll be able to monitor, debug, and evaluate your LiveKit agents in the Langfuse dashboard.

> **What is LiveKit Agents?** [LiveKit Agents](https://docs.livekit.io/agents/) ([GitHub](https://github.com/livekit/agents)) is an open-source Python and Node.js framework for building production-grade multimodal and voice AI agents. It provides a complete set of tools and abstractions for feeding realtime media through AI pipelines, supporting both high-performance STT-LLM-TTS voice pipelines and speech-to-speech models with any AI provider.

> **What is Langfuse?** [Langfuse](https://langfuse.com) is an open-source LLM engineering platform. It offers tracing and monitoring capabilities for AI applications. Langfuse helps developers debug, analyze, and optimize their AI systems by providing detailed insights and integrating with a wide array of tools and frameworks through native integrations, OpenTelemetry, and dedicated SDKs.

## Get Started

LiveKit Agents includes built-in OpenTelemetry support. The [Langfuse Python SDK](/docs/observability/sdk/overview) can register as an OpenTelemetry span exporter, so all you need is to create a `TracerProvider`, hand it to both LiveKit and Langfuse, and traces will appear in your Langfuse dashboard automatically.

<Steps>

### Step 1: Install Dependencies

```bash
pip install langfuse livekit-agents livekit-plugins-openai livekit-plugins-silero livekit-plugins-turn-detector opentelemetry-sdk python-dotenv
```

### Step 2: Configure Langfuse SDK

Set up your Langfuse API keys. You can get these keys by signing up for a free [Langfuse Cloud](https://langfuse.com/cloud) account or by [self-hosting Langfuse](https://langfuse.com/self-hosting).

```bash filename=".env"
LANGFUSE_SECRET_KEY = "sk-lf-..."
LANGFUSE_PUBLIC_KEY = "pk-lf-..."
LANGFUSE_BASE_URL = "https://cloud.langfuse.com" # 🇪🇺 EU region
# Other Langfuse data regions include 🇺🇸 US: https://us.cloud.langfuse.com, 🇯🇵 Japan: https://jp.cloud.langfuse.com and ⚕️ HIPAA: https://hipaa.cloud.langfuse.com
```

### Step 3: Set Up the Langfuse Tracer Provider

Create a shared `TracerProvider` and pass it to both LiveKit (via `set_tracer_provider`) and the Langfuse SDK. The Langfuse SDK automatically registers an exporter on the provider to send spans to Langfuse.

Because LiveKit generates many internal spans, set `should_export_span=lambda span: True` so that all telemetry is forwarded.

```python filename="agent.py"
import os

from langfuse import Langfuse
from opentelemetry.sdk.trace import TracerProvider
from opentelemetry.util.types import AttributeValue

from livekit.agents.telemetry import set_tracer_provider


def setup_langfuse(
    metadata: dict[str, AttributeValue] | None = None,
    *,
    host: str | None = None,
    public_key: str | None = None,
    secret_key: str | None = None,
) -> TracerProvider:
    public_key = public_key or os.getenv("LANGFUSE_PUBLIC_KEY")
    secret_key = secret_key or os.getenv("LANGFUSE_SECRET_KEY")
    host = host or os.getenv("LANGFUSE_HOST") or os.getenv("LANGFUSE_BASE_URL")

    if not public_key or not secret_key or not host:
        raise ValueError(
            "LANGFUSE_PUBLIC_KEY, LANGFUSE_SECRET_KEY, and LANGFUSE_HOST (or LANGFUSE_BASE_URL) must be set"
        )

    trace_provider = TracerProvider()
    set_tracer_provider(trace_provider, metadata=metadata)

    Langfuse(
        public_key=public_key,
        secret_key=secret_key,
        base_url=host,
        tracer_provider=trace_provider,
        should_export_span=lambda span: True,
    )

    return trace_provider
```

### Step 4: Use the Tracer Provider in Your Entrypoint

Call `setup_langfuse()` before `AgentSession.start()`. You can pass optional metadata (such as `langfuse.session.id`) that will be attached to every span.

```python filename="agent.py"
from livekit.agents import AgentSession, JobContext, metrics
from livekit.agents.voice import MetricsCollectedEvent
from livekit.plugins import silero


async def entrypoint(ctx: JobContext):
    trace_provider = setup_langfuse(
        metadata={
            "langfuse.session.id": ctx.room.name,
        }
    )

    async def flush_trace():
        trace_provider.force_flush()

    ctx.add_shutdown_callback(flush_trace)

    session = AgentSession(vad=silero.VAD.load())

    @session.on("metrics_collected")
    def _on_metrics_collected(ev: MetricsCollectedEvent):
        metrics.log_metrics(ev.metrics)

    await session.start(agent=MyAgent(), room=ctx.room)
```

You can find a full end-to-end example [on GitHub](https://github.com/livekit/agents/blob/main/examples/voice_agents/langfuse_trace.py).

### Step 5: View Traces in Langfuse

After running the agent, navigate to your [Langfuse Trace Table](https://cloud.langfuse.com). You will find detailed traces of the agent's execution, providing insights into every agent step, tool call, input, output, and performance metric.

![LiveKit trace in Langfuse](/images/docs/livekit-trace.png)

[Example trace in Langfuse](https://cloud.langfuse.com/project/cloramnkj0002jz088vzn1ja4/traces/5f00d917e418a8a41de5ad9b650daca0?observation=c9b49d21cbfee499&timestamp=2026-02-26T17%3A47%3A26.818Z&traceId=5f00d917e418a8a41de5ad9b650daca0)

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

## References

- [LiveKit Agents Documentation](https://docs.livekit.io/agents/)
- [LiveKit Telemetry Guide](https://docs.livekit.io/agents/build/metrics/)
- [End-to-end example](https://github.com/livekit/agents/blob/main/examples/voice_agents/langfuse_trace.py)

## GitHub Discussions

