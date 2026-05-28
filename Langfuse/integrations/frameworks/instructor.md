---
source: ⚠️ Jupyter Notebook
title: Observability & Tracing for Instructor
sidebarTitle: Instructor
logo: /images/integrations/instructor_icon.svg
description: Open-source observability for Instructor, a popular library to get structured (JSON, Pydantic) LLM outputs.
category: Integrations
---

# Instructor - Observability & Tracing

> **What is Instructor?** [Instructor](https://python.useinstructor.com/) ([GitHub](https://github.com/jxnl/instructor/)) is a popular library to get structured LLM outputs.Instructor makes it easy to reliably get structured data like JSON from Large Language Models (LLMs) like GPT-3.5, GPT-4, GPT-4-Vision, including open source models like Mistral/Mixtral from Together, Anyscale, Ollama, and llama-cpp-python. By leveraging various modes like Function Calling, Tool Calling and even constrained sampling modes like JSON mode, JSON Schema; Instructor stands out for its simplicity, transparency, and user-centric design. Under the hood, Instructor leverages Pydantic to do the heavy lifting, and provides a simple, easy-to-use API on top of it by helping you manage validation context, retries with Tenacity, and streaming Lists and Partial responses.

> **What is Langfuse?** [Langfuse](https://langfuse.com) is an open source LLM engineering platform that helps teams trace API calls, monitor performance, and debug issues in their AI applications.

This is a cookbook with examples of the Langfuse Integration for Python.

## Setup

```python
%pip install langfuse openai pydantic instructor --upgrade
```

Initialize the Langfuse client with your API keys from the project settings in the Langfuse UI and add them to your environment.

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

## Get started

It is easy to use instructor with Langfuse. We use the [Langfuse OpenAI integration](https://langfuse.com/integrations/model-providers/openai-py) and simply patch the client with instructor. This works with both synchronous and asynchronous clients.

### Langfuse-Instructor integration with sychnronous OpenAI client

```python
import instructor
from langfuse.openai import OpenAI
from pydantic import BaseModel

# Patch Langfuse wrapper of synchronous OpenAI client with instructor
client = instructor.patch(OpenAI())

class WeatherDetail(BaseModel):
    city: str
    temperature: int

# Run synchronous OpenAI client
weather_info = client.chat.completions.create(
    model="gpt-4o",
    response_model=WeatherDetail,
    messages=[
        {"role": "user", "content": "The weather in Paris is 18 degrees Celsius."},
    ],
)

print(weather_info.model_dump_json(indent=2))
"""
{
  "city": "Paris",
  "temperature": 18
}
"""
```

### Langfuse-Instructor integration with asychnronous OpenAI client

```python
import instructor
from langfuse.openai import AsyncOpenAI
from pydantic import BaseModel

# Patch Langfuse wrapper of synchronous OpenAI client with instructor
client = instructor.apatch(AsyncOpenAI())

class WeatherDetail(BaseModel):
    city: str
    temperature: int

# Run asynchronous OpenAI client
task = client.chat.completions.create(
    model="gpt-4o",
    response_model=WeatherDetail,
    messages=[
        {"role": "user", "content": "The weather in Paris is 18 degrees Celsius."},
    ],
)

response = await task
print(response.model_dump_json(indent=2))
"""
{
  "city": "Paris",
  "temperature": 18
}
"""
```

## Example

In this example, we first classify customer feedback into categories like `PRAISE`, `SUGGESTION`, `BUG` and `QUESTION`, and further scores the relevance of each feedback to the business on a scale of 0.0 to 1.0. In this case, we use the asynchronous OpenAI client `AsyncOpenAI` to classify and evaluate the feedback.

```python
from typing import List, Tuple
from enum import Enum

import asyncio
import instructor

from langfuse.openai import AsyncOpenAI
from langfuse import observe, get_client

from pydantic import BaseModel, Field, field_validator

# Initialize Langfuse wrapper of AsyncOpenAI client
client = AsyncOpenAI()

# Patch the client with Instructor
client = instructor.patch(client, mode=instructor.Mode.TOOLS)

# Initialize Langfuse (needed for scoring)
langfuse = get_client()

# Rate limit the number of requests
sem = asyncio.Semaphore(5)

# Define feedback categories
class FeedbackType(Enum):
    PRAISE = "PRAISE"
    SUGGESTION = "SUGGESTION"
    BUG = "BUG"
    QUESTION = "QUESTION"

# Model for feedback classification
class FeedbackClassification(BaseModel):
    feedback_text: str = Field(...)
    classification: List[FeedbackType] = Field(description="Predicted categories for the feedback")
    relevance_score: float = Field(
        default=0.0,
        description="Score of the query evaluating its relevance to the business between 0.0 and 1.0"
    )

    # Make sure feedback type is list
    @field_validator("classification", mode="before")
    def validate_classification(cls, v):
        if not isinstance(v, list):
            v = [v]
        return v

@observe() # Langfuse decorator to automatically log spans to Langfuse
async def classify_feedback(feedback: str) -> Tuple[FeedbackClassification, float]:
    """
    Classify customer feedback into categories and evaluate relevance.
    """
    async with sem:  # simple rate limiting
        response = await client.chat.completions.create(
            model="gpt-4o",
            response_model=FeedbackClassification,
            max_retries=2,
            messages=[
                {
                    "role": "user",
                    "content": f"Classify and score this feedback: {feedback}",
                },
            ],
        )

        # Retrieve observation_id of current span
        observation_id = langfuse.get_current_observation_id()

        return feedback, response, observation_id

def score_relevance(trace_id: str, observation_id: str, relevance_score: float):
    """
    Score the relevance of a feedback query in Langfuse given the observation_id.
    """
    langfuse.create_score(
        trace_id=trace_id,
        observation_id=observation_id,
        name="feedback-relevance",
        value=relevance_score
    )

@observe() # Langfuse decorator to automatically log trace to Langfuse
async def main(feedbacks: List[str]):
    tasks = [classify_feedback(feedback) for feedback in feedbacks]
    results = []

    for task in asyncio.as_completed(tasks):
        feedback, classification, observation_id = await task
        result = {
            "feedback": feedback,
            "classification": [c.value for c in classification.classification],
            "relevance_score": classification.relevance_score,
        }
        results.append(result)

        # Retrieve trace_id of current trace
        trace_id = langfuse.get_current_trace_id()

        # Score the relevance of the feedback in Langfuse
        score_relevance(trace_id, observation_id, classification.relevance_score)

    # Flush observations to Langfuse
    langfuse.flush()
    return results

feedback_messages = [
    "The chat bot on your website does not work.",
    "Your customer service is exceptional!",
    "Could you add more features to your app?",
    "I have a question about my recent order.",
]

feedback_classifications = await main(feedback_messages)

for classification in feedback_classifications:
    print(f"Feedback: {classification['feedback']}")
    print(f"Classification: {classification['classification']}")
    print(f"Relevance Score: {classification['relevance_score']}")

"""
Feedback: I have a question about my recent order.
Classification: ['QUESTION']
Relevance Score: 0.0
Feedback: Could you add more features to your app?
Classification: ['SUGGESTION']
Relevance Score: 0.0
Feedback: The chat bot on your website does not work.
Classification: ['BUG']
Relevance Score: 0.9
Feedback: Your customer service is exceptional!
Classification: ['PRAISE']
Relevance Score: 0.9
"""
```

![Instructor Trace in Langfuse](https://langfuse.com/images/docs/instructor-trace.png)

_[Public link to trace](https://cloud.langfuse.com/project/cloramnkj0002jz088vzn1ja4/traces/ba27e7b1-e23e-4f50-87de-420cf038190f?timestamp=2025-03-31T16:12:57.041Z&display=details)_

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

