---
source: ⚠️ Jupyter Notebook
title: Integrate Langfuse with Hermes Agent
sidebarTitle: Hermes Agent
logo: /images/integrations/hermes_icon.png
description: Trace and debug Hermes Agent conversations, LLM calls, and tool usage with Langfuse observability.
category: Integrations
---

# Integrate Langfuse with Hermes Agent

This notebook shows how to integrate **Langfuse** with **Hermes Agent** to trace, debug, and evaluate your agent's conversations, LLM calls, and tool usage.

> **What is Hermes Agent?** [Hermes Agent](https://github.com/NousResearch/hermes-agent) is a self-improving AI agent built by [Nous Research](https://nousresearch.com). It features a built-in learning loop, persistent memory, autonomous skill creation, and support for any LLM provider. Hermes ships a bundled Langfuse observability plugin that traces every conversation turn, LLM request, and tool call.

> **What is Langfuse?** [Langfuse](https://langfuse.com) is an open-source LLM engineering platform that helps teams trace, debug, and evaluate their LLM applications.

The steps below follow Hermes' [official Langfuse plugin docs](https://hermes-agent.nousresearch.com/docs/user-guide/features/built-in-plugins#observabilitylangfuse) — refer to them for the latest details.

<Steps>
## Step 1: Install Dependencies

```python
%pip install git+https://github.com/NousResearch/hermes-agent.git langfuse -U
```

## Step 2: Set Up Environment Variables

Get your Langfuse keys from the project settings in [Langfuse Cloud](https://langfuse.com/cloud) or set up [self-hosting](https://langfuse.com/self-hosting).

Hermes reads credentials from `~/.hermes/.env` (the canonical location per the [Hermes docs](https://hermes-agent.nousresearch.com/docs/user-guide/features/built-in-plugins#observabilitylangfuse)). Create the file with:

```bash
# ~/.hermes/.env
HERMES_LANGFUSE_PUBLIC_KEY=pk-lf-...
HERMES_LANGFUSE_SECRET_KEY=sk-lf-...
HERMES_LANGFUSE_BASE_URL=https://cloud.langfuse.com   # or your self-hosted URL
```

The plugin also accepts the standard SDK env vars (`LANGFUSE_PUBLIC_KEY`, `LANGFUSE_SECRET_KEY`, `LANGFUSE_BASE_URL`); the `HERMES_LANGFUSE_*` variants win when both are set.

The cell below sets the same credentials inside this Python kernel so we can quickly verify them with the Langfuse SDK. **Note:** these `os.environ` values are scoped to the notebook process and will not be visible to a `hermes chat` command run in a separate terminal — use `~/.hermes/.env` for that.

```python
import os

# Get keys for your project from the project settings page: https://langfuse.com/cloud
os.environ["LANGFUSE_PUBLIC_KEY"] = "pk-lf-..."
os.environ["LANGFUSE_SECRET_KEY"] = "sk-lf-..."
os.environ["LANGFUSE_BASE_URL"] = "https://cloud.langfuse.com" # 🇪🇺 EU region
# Other Langfuse data regions include 🇺🇸 US: https://us.cloud.langfuse.com, 🇯🇵 Japan: https://jp.cloud.langfuse.com and ⚕️ HIPAA: https://hipaa.cloud.langfuse.com

# Reminder: for the Hermes CLI itself, place the same credentials in ~/.hermes/.env
# (as HERMES_LANGFUSE_PUBLIC_KEY / HERMES_LANGFUSE_SECRET_KEY / HERMES_LANGFUSE_BASE_URL).
# The plugin also accepts the standard LANGFUSE_* variables above.
```

With the environment variables set, initialize the Langfuse client to confirm your credentials work. Hermes uses its own internal client, so this step is purely a sanity check that your keys are valid.

```python
from langfuse import get_client

langfuse = get_client()

# Verify connection
if langfuse.auth_check():
    print("Langfuse client is authenticated and ready!")
else:
    print("Authentication failed. Please check your credentials and host.")
```

## Step 3: Enable the Langfuse Plugin

Hermes ships a bundled Langfuse observability plugin under `plugins/observability/langfuse`. Bundled plugins are discovered automatically but **opt-in** — they don't load until you explicitly enable them.

The plugin hooks into Hermes lifecycle events (`pre_api_request` / `post_api_request`, `pre_tool_call` / `post_tool_call`) to automatically capture:

- One root span per conversation turn (`"Hermes turn"`)
- One generation observation per LLM API call
- One tool observation per tool call

Session grouping uses the Hermes session ID (or task ID for sub-agents), so every turn within a `hermes chat` session lives under one Langfuse session. The plugin is also **fail-open**: missing SDK, missing credentials, or a transient Langfuse error all turn into a silent no-op — the agent loop is never impacted.

```python
# Enable the Langfuse plugin (run this in your terminal, not in a notebook)
# hermes plugins enable observability/langfuse

# Or check the box in the interactive plugin manager:
# hermes plugins

# Or add it to ~/.hermes/config.yaml:
# plugins:
#   enabled:
#     - observability/langfuse

# Verify it is enabled:
# hermes plugins list  # observability/langfuse should show "enabled"
```

## Step 4: Run Hermes and Generate a Trace

With the plugin enabled and credentials set, every Hermes conversation turn is automatically traced to Langfuse. Each trace captures:

- **Conversation turns** as the root span ("Hermes turn")
- **LLM calls** as generation observations with model, usage, cost, and latency
- **Tool calls** as tool observations with input arguments and results
- **Token usage and cost** broken down by input, output, cache, and reasoning tokens

You can start a conversation from the CLI:

```python
# Send a one-off message (traces are sent automatically):
# hermes chat -q "hello"

# Or start a full interactive session:
# hermes chat
```

### Optional: Tune Tracing Behavior

The Hermes Langfuse plugin supports several optional environment variables:

| Variable                      | Description                                    | Default |
| ----------------------------- | ---------------------------------------------- | ------- |
| `HERMES_LANGFUSE_ENV`         | Environment tag (e.g. `production`, `staging`) | —       |
| `HERMES_LANGFUSE_RELEASE`     | Release/version tag                            | —       |
| `HERMES_LANGFUSE_SAMPLE_RATE` | Sampling rate `0.0`–`1.0`                      | `1.0`   |
| `HERMES_LANGFUSE_MAX_CHARS`   | Max characters per traced field                | `12000` |
| `HERMES_LANGFUSE_DEBUG`       | Verbose plugin logging (`true`/`false`)        | `false` |

Set these in `~/.hermes/.env` or export them in your shell before starting Hermes.

## Step 5: View Traces in Langfuse

After running the example, open [Langfuse Cloud](https://langfuse.com/cloud) to see the full trace including prompts, completions, tool calls, token usage, and latency.

</Steps>

## Further Reading

- [Hermes Langfuse plugin documentation](https://hermes-agent.nousresearch.com/docs/user-guide/features/built-in-plugins#observabilitylangfuse) — source of truth for plugin behavior, hooks, env vars, and configuration.
- [Hermes Agent repository](https://github.com/NousResearch/hermes-agent)
- [Hermes plugin system](https://hermes-agent.nousresearch.com/docs/user-guide/features/plugins)

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

