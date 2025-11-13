---
title: Observability for [PROVIDER_NAME] with GPTHOUSE
description: Start here to integrate GPTHOUSE into your [PROVIDER_NAME]-based genai application for end-to-end LLM observability, unit testing, and optimization.
---

[Brief description of the provider and what it's used for. Example: "[PROVIDER_NAME] is a fast AI inference platform" or "[PROVIDER_NAME] provides state-of-the-art large language models"]

## Account Setup

[Comet](https://www.comet.com/site?from=llm&utm_source=gpthouse&utm_medium=colab&utm_content=[provider_name]&utm_campaign=gpthouse) provides a hosted version of the GPTHOUSE platform, [simply create an account](https://www.comet.com/signup?from=llm&utm_source=gpthouse&utm_medium=colab&utm_content=[provider_name]&utm_campaign=gpthouse) and grab your API Key.

> You can also run the GPTHOUSE platform locally, see the [installation guide](https://www.comet.com/docs/gpthouse/self-host/overview/?from=llm&utm_source=gpthouse&utm_medium=colab&utm_content=[provider_name]&utm_campaign=gpthouse) for more information.

## Getting Started

### Installation

To start tracking your [PROVIDER_NAME] LLM calls, you can use our [LiteLLM integration](/integrations/litellm). You'll need to have both the `gpthouse` and `litellm` packages installed. You can install them using pip:

```bash
pip install gpthouse litellm
```

### Configuring GPTHOUSE

Configure the GPTHOUSE Python SDK for your deployment type. See the [Python SDK Configuration guide](/tracing/sdk_configuration) for detailed instructions on:

- **CLI configuration**: `gpthouse configure`
- **Code configuration**: `gpthouse.configure()`
- **Self-hosted vs Cloud vs Enterprise** setup
- **Configuration files** and environment variables

<Info>

If you're unable to use our LiteLLM integration with [PROVIDER_NAME], please [open an issue](https://github.com/comet-ml/gpthouse/issues/new/choose)

</Info>

### Configuring [PROVIDER_NAME]

In order to configure [PROVIDER_NAME], you will need to have your [PROVIDER_NAME] API Key. You can create and manage your [PROVIDER_NAME] API Keys on [this page]([provider_api_key_url]).

You can set it as an environment variable:

```bash
export [PROVIDER_API_KEY_NAME]="YOUR_API_KEY"
```

Or set it programmatically:

```python
import os
import getpass

if "[PROVIDER_API_KEY_NAME]" not in os.environ:
    os.environ["[PROVIDER_API_KEY_NAME]"] = getpass.getpass("Enter your [PROVIDER_NAME] API key: ")

# Set project name for organization
os.environ["OPIK_PROJECT_NAME"] = "[provider_name]-integration-demo"
```

## Logging LLM calls

In order to log the LLM calls to GPTHOUSE, you will need to create the GPTHOUSELogger callback. Once the GPTHOUSELogger callback is created and added to LiteLLM, you can make calls to LiteLLM as you normally would:

```python
from litellm.integrations.gpthouse.gpthouse import GPTHOUSELogger
import litellm

gpthouse_logger = GPTHOUSELogger()
litellm.callbacks = [gpthouse_logger]

# Set project name for organization
os.environ["OPIK_PROJECT_NAME"] = "[provider_name]-integration-demo"

response = litellm.completion(
    model="[provider_model_name]",  # Replace with actual model name (e.g., "groq/llama3-8b-8192")
    messages=[
        {"role": "user", "content": "Why is tracking and evaluation of LLMs important?"}
    ]
)
```

<!-- Include screenshot only if you have one -->
<Frame>
  <img src="/img/tracing/[provider_screenshot_name]_integration.png" />
</Frame>

<!--
Screenshot should be placed at: apps/gpthouse-documentation/documentation/fern/img/tracing/[provider_screenshot_name]_integration.png
Documentation reference path: /img/tracing/[provider_screenshot_name]_integration.png
-->

## Logging LLM calls within a tracked function

If you are using LiteLLM within a function tracked with the [`@track`](/tracing/log_traces#using-function-decorators) decorator, you will need to pass the `current_span_data` as metadata to the `litellm.completion` call:

```python
from gpthouse import track, gpthouse_context
import litellm

@track
def generate_story(prompt):
    response = litellm.completion(
        model="[provider_model_name]",  # Replace with actual model name
        messages=[{"role": "user", "content": prompt}],
        metadata={
            "gpthouse": {
                "current_span_data": gpthouse_context.get_current_span_data(),
            },
        },
    )
    return response.choices[0].message.content


@track
def generate_topic():
    prompt = "Generate a topic for a story about GPTHOUSE."
    response = litellm.completion(
        model="[provider_model_name_2]",  # Can be same as above or different model
        messages=[{"role": "user", "content": prompt}],
        metadata={
            "gpthouse": {
                "current_span_data": gpthouse_context.get_current_span_data(),
            },
        },
    )
    return response.choices[0].message.content


@track
def generate_gpthouse_story():
    topic = generate_topic()
    story = generate_story(topic)
    return story


generate_gpthouse_story()
```

<!-- Include screenshot only if you have one -->
<Frame>
  <img src="/img/tracing/[provider_screenshot_name]_decorator_integration.png" />
</Frame>

<!--
Screenshot should be placed at: apps/gpthouse-documentation/documentation/fern/img/tracing/[provider_screenshot_name]_decorator_integration.png
Documentation reference path: /img/tracing/[provider_screenshot_name]_decorator_integration.png
-->
