---
source: ⚠️ Jupyter Notebook
title: Observability for Claude Agent SDK JS/TS with Langfuse
sidebarTitle: Claude Agent SDK (JS/TS)
logo: /images/integrations/claude_icon.png
description: Learn how to trace Claude Agent SDK applications in JS/TS with Langfuse via OpenTelemetry
category: Integrations
---

# Trace Claude Agent SDK JS/TS with Langfuse

<a href="https://langfuse.com/integrations/frameworks/claude-agent-sdk"><img className="inline" alt="Python" src="https://img.shields.io/badge/Python-3776AB?style=flat&logo=python&logoColor=white" /></a> <a href="https://langfuse.com/integrations/frameworks/claude-agent-sdk-js"><img className="inline" alt="JS/TS" src="https://img.shields.io/badge/JS/TS-d4d4d8?style=flat&logo=javascript&logoColor=white" /></a>

The [Claude Agent SDK](https://github.com/anthropics/claude-agent-sdk-typescript) is Anthropic's open-source framework for building AI agents in TypeScript. By combining the Claude Agent SDK with **Langfuse**, you can trace, monitor, and analyze every agent step, tool call, and model response in development and production.

This notebook demonstrates how to use the [`ClaudeAgentSDKInstrumentation`](https://github.com/Arize-ai/openinference/tree/main/js/packages/openinference-instrumentation-claude-agent-sdk) library from [OpenInference](https://github.com/Arize-ai/openinference) to automatically instrument Claude Agent SDK calls and send OpenTelemetry spans to Langfuse.

> **What is Claude Agent SDK?**\
> The [Claude Agent SDK](https://docs.anthropic.com/en/docs/claude-code/sdk/sdk-typescript) is Anthropic's open-source framework for building AI agents. It provides a clean API for creating tool-using agents with the `query()` function, including native support for MCP servers and custom tools.

> **What is Langfuse?**\
> [Langfuse](https://langfuse.com) is an open source platform for LLM observability and monitoring. It helps you trace and monitor your AI applications by capturing metadata, prompt details, token usage, latency, and more.

<Steps>
## Step 1: Install Dependencies

Install the necessary packages:

```bash
npm install @anthropic-ai/claude-agent-sdk @arizeai/openinference-instrumentation-claude-agent-sdk @langfuse/otel @opentelemetry/sdk-node
```

> **Note**: This cookbook uses **Deno.js** for execution, which requires different syntax for importing packages and setting environment variables. For Node.js applications, the setup process is similar but uses standard `npm` packages and `process.env`.

## Step 2: Configure Environment

Set up your Langfuse and Anthropic API keys. You can get Langfuse keys by signing up for a free [Langfuse Cloud](https://langfuse.com/cloud) account or by [self-hosting Langfuse](https://langfuse.com/self-hosting). Get your Anthropic API key from the [Anthropic Console](https://console.anthropic.com/).

```typescript
Deno.env.set("ANTHROPIC_API_KEY", "sk-ant-...");

Deno.env.set("LANGFUSE_PUBLIC_KEY", "pk-lf-...");
Deno.env.set("LANGFUSE_SECRET_KEY", "sk-lf-...");

Deno.env.set("LANGFUSE_BASE_URL", "https://cloud.langfuse.com"); // 🇪🇺 EU region
// Other Langfuse data regions include 🇺🇸 US: https://us.cloud.langfuse.com, 🇯🇵 Japan: https://jp.cloud.langfuse.com and ⚕️ HIPAA: https://hipaa.cloud.langfuse.com
```

## Step 3: Initialize OpenTelemetry with Langfuse

Set up the OpenTelemetry SDK with the `LangfuseSpanProcessor` and the [`ClaudeAgentSDKInstrumentation`](https://github.com/Arize-ai/openinference/tree/main/js/packages/openinference-instrumentation-claude-agent-sdk) from OpenInference. The instrumentation automatically captures agent runs and tool calls, sending them as OpenTelemetry spans to Langfuse.

We also provide a custom [`shouldExportSpan`](https://langfuse.com/docs/observability/sdk/advanced-features#filtering-by-instrumentation-scope) filter to include spans from the `@arizeai/openinference-instrumentation-claude-agent-sdk` instrumentation scope alongside the default Langfuse filter.

```typescript
import { NodeSDK } from "npm:@opentelemetry/sdk-node";
import { LangfuseSpanProcessor, isDefaultExportSpan } from "npm:@langfuse/otel";
import { ClaudeAgentSDKInstrumentation } from "npm:@arizeai/openinference-instrumentation-claude-agent-sdk";

import * as ClaudeAgentSDKModule from "npm:@anthropic-ai/claude-agent-sdk";

// Create mutable copy — Deno's ESM namespace objects are read-only
const ClaudeAgentSDK = { ...ClaudeAgentSDKModule };

const instrumentation = new ClaudeAgentSDKInstrumentation();
instrumentation.manuallyInstrument(ClaudeAgentSDK);

const sdk = new NodeSDK({
  spanProcessors: [
    new LangfuseSpanProcessor({
      shouldExportSpan: ({ otelSpan }) =>
        isDefaultExportSpan(otelSpan) ||
        otelSpan.instrumentationScope.name ===
          "@arizeai/openinference-instrumentation-claude-agent-sdk",
    }),
  ],
  instrumentations: [instrumentation],
});

sdk.start();
```

## Step 4: Run the Agent

Use the Claude Agent SDK's `query()` function to run an agent. All agent steps, tool calls, and model completions are automatically traced and sent to Langfuse.

```typescript
const { query } = ClaudeAgentSDK;

for await (const message of query({
  prompt: "What is the capital of France? Answer in a single sentence.",
  options: {
    model: "claude-sonnet-4-5",
  },
})) {
  if (message.type === "assistant") {
    console.log(message.message.content);
  }
}

await sdk.shutdown();
```

## Step 5: View Traces in Langfuse

After running the agent, navigate to your Langfuse Trace Table. You will find detailed traces of the agent's execution, providing insights into every agent step, tool call, input, output, and performance metric.

![Claude Agent SDK JS/TS example trace in Langfuse](https://langfuse.com/images/cookbook/integration_claude_agent_sdk/claude-agent-sdk-js-example-trace.png)

[Link to example trace in Langfuse](https://cloud.langfuse.com/project/cloramnkj0002jz088vzn1ja4/traces/96f872a9582504826774a153dd23cfc4?observation=4dab2d5a8f86b306&timestamp=2026-03-13T10%3A09%3A28.575Z&traceId=96f872a9582504826774a153dd23cfc4)

</Steps>

## Interoperability with the JS/TS SDK

You can use this integration together with the Langfuse [SDKs](/docs/observability/sdk/overview) to add additional attributes or group observations into a single trace.

<Tabs items={["Context Manager", "Observe Wrapper"]}>
<Tab>

The [Context Manager](/docs/observability/sdk/instrumentation#context-management-with-callbacks) allows you to wrap your instrumented code using context managers (with `with` statements), which allows you to add additional attributes to the trace. Any observation created inside the callback will automatically be nested under the active observation, and the observation will be ended when the callback finishes.

```typescript
import { startActiveObservation, propagateAttributes } from "npm:@langfuse/tracing";

await startActiveObservation("context-manager", async (span) => {
  span.update({
    input: { query: "What is the capital of France?" },
  });

  // Propagate userId to all child observations
  await propagateAttributes(
    {
      userId: "user-123",
      sessionId: "session-123",
      metadata: {
        source: "api",
        region: "us-east-1",
      },
      tags: ["api", "user"],
      version: "1.0.0",
    },
    async () => {

      // YOUR CODE HERE
      const { text } = await generateText({
        model: openai("gpt-5"),
        prompt: "What is the capital of France?",
        experimental_telemetry: { isEnabled: true },
      });
    }
  );
  span.update({ output: "Paris" });
});
```

Learn more about using the Context Manager in the [Langfuse SDK instrumentation docs](https://langfuse.com/docs/observability/sdk/instrumentation#context-management-with-callbacks).

</Tab>
<Tab>

The [`observe` wrapper](/docs/observability/sdk/instrumentation#observe-wrapper) is a powerful tool for tracing existing functions without modifying their internal logic. It acts as a decorator that automatically creates a span or generation around the function call. You can use the `propagateAttributes` function to add attributes to the observation from within the wrapped function.

```typescript
import { observe, propagateAttributes } from "@langfuse/tracing";
import { generateText } from "ai";
import { openai } from "@ai-sdk/openai";

// An existing function
const processUserRequest = observe(
  async (userQuery: string) => {

    // Propagate attributes to all child observations
    return await propagateAttributes(
      {
        userId: "user-123",
        sessionId: "session-123",
        metadata: {
          source: "api",
          region: "us-east-1",
        },
        tags: ["api", "user"],
        version: "1.0.0",
      },
      async () => {

        // YOUR CODE HERE
        const { text } = await generateText({
          model: openai("gpt-5"),
          prompt: userQuery,
          experimental_telemetry: { isEnabled: true },
        });

        return text;
      }
    );
  },
  { name: "process-user-request" }
);

const result = await processUserRequest("some query");
```

Learn more about using the Decorator in the [Langfuse SDK instrumentation docs](/docs/observability/sdk/instrumentation#observe-wrapper).

</Tab>
</Tabs>

## Troubleshooting

<details>
<summary>No traces appearing</summary>

First, enable [debug mode](/docs/observability/sdk/advanced-features#logging--debugging) in the JS/TS SDK:

```bash
export LANGFUSE_LOG_LEVEL="DEBUG"
```

Then run your application and check the debug logs:

- **OTel spans appear in the logs:** Your application is instrumented correctly but traces are not reaching Langfuse. To resolve this:
  1. Call [`forceFlush()`](/docs/observability/sdk/instrumentation#client-lifecycle--flushing) at the end of your application to ensure all traces are exported. This is especially important in short-lived environments like serverless functions.
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

