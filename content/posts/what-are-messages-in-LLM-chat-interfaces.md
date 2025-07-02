---
date: '2025-07-02'
draft: false
title: 'What Are Messages in LLM Chat Interfaces'
summary: "Understanding the role of messages in LLM chat interfaces, their structure, and how they facilitate communication between users and AI models."
tags: ["LLM", "Chat Interfaces", "Messages"]
categories: ["LLM", "Chat Interfaces"]
---

## What Are “Messages” in LLM Chat Interfaces?

In conversational LLM APIs (like ChatGPT or Azure OpenAI Chat API), communication is structured through a list of “messages.” Each message is a dictionary (or object) with two key components:

- **role**: Specifies the speaker (system, user, or assistant).
- **content**: Contains the actual text of the message.

### Basic Message Roles

| Role      | Description                                           |
|-----------|-------------------------------------------------------|
| system    | Defines the behavior, rules, and persona of the LLM.  |
| user      | Represents the input prompt or query from the user.   |
| assistant | The model's response, generated automatically.        |

---

## What Does a Message List Look Like?

Here’s an example of a minimal message structure in Python:

```python
messages = [
    {"role": "system", "content": "You are a helpful assistant."},
    {"role": "user", "content": "What is the capital of France?"}
]
```

When this list is sent to the model, it acts as a chat log, enabling the model to generate responses based on the entire conversation context.

---

## Anatomy of a Message Query in Code

Below is a complete example using the OpenAI Python SDK. This approach works for both OpenAI and Azure OpenAI APIs with minor adjustments:

```python
from openai import OpenAI

client = OpenAI(api_key="your-key")

response = client.chat.completions.create(
    model="gpt-4",
    messages=[
        {"role": "system", "content": "You are a scientific assistant that answers with detailed technical explanations."},
        {"role": "user", "content": "Explain the second law of thermodynamics."}
    ],
    temperature=0.2,
    max_tokens=500
)

print(response.choices[0].message.content)
```

---

## Message Structure in Azure OpenAI SDK (Python)

Azure OpenAI uses a similar structure but with Azure-specific class names. Here’s an example:

```python
from azure.ai.openai import AzureOpenAI, ChatMessage, ChatRole

client = AzureOpenAI(
    endpoint="https://your-endpoint.openai.azure.com/",
    credential="your-key",
    api_version="2024-05-01-preview"
)

messages = [
    ChatMessage(role=ChatRole.SYSTEM, content="You are a car manual expert."),
    ChatMessage(role=ChatRole.USER, content="What does 'changing map visuals' mean?")
]

response = client.chat.completions.create(
    deployment_name="gpt-4o",
    messages=messages,
    max_tokens=100,
    temperature=0.0
)

print(response.choices[0].message.content)
```

### Key Components:

- **Role**: Defined as `ChatRole.SYSTEM` or `ChatRole.USER`.
- **Content**: The text message associated with each role.

---

## Prompt Templates for Better Structure

Prompt templates allow for reusable and structured prompts. For example:

```python
from azure.ai.openai import ChatPromptTemplate, SystemMessagePromptTemplate

prompt_template = ChatPromptTemplate.from_messages([
    SystemMessagePromptTemplate.from_template("You are an automotive expert."),
    ("user", "Extract the core technical noun from: {phrase}")
])

# Fill in the prompt
filled = prompt_template.format_prompt(phrase="Changing map visuals")
messages = filled.to_messages()
```

This generates a message list ready to be sent to the model.

---

## Roles in Depth

### System Message

Defines the model's behavior and persona throughout the session.

**Example**:

```plaintext
"You are an expert automotive assistant. Only reply with technical noun phrases."
```

### User Message

Represents the user’s input or query.

**Example**:

```plaintext
"Extract the technical noun from: Changing map visuals"
```

### Assistant Message

Generated automatically by the model in response to the user’s input.

**Example**:

```json
{"role": "assistant", "content": "map visuals"}
```

---

## Example: Extracting Technical Terms

Here’s a complete example for extracting technical terms using Azure OpenAI SDK:

```python
import os
from azure.ai.openai import AzureOpenAI
from azure.ai.openai import ChatMessage, ChatRole
from azure.ai.openai import ChatPromptTemplate, SystemMessagePromptTemplate, HumanMessagePromptTemplate

# Azure credentials
AZURE_OPENAI_ENDPOINT = "https://your-endpoint.openai.azure.com"
AZURE_OPENAI_API_KEY = os.getenv("AZURE_OPENAI_API_LLM_KEY")
AZURE_DEPLOYMENT_NAME = "gpt-4o"
AZURE_OPENAI_API_VERSION = "2024-05-01-preview"

# Initialize client
client = AzureOpenAI(
    api_key=AZURE_OPENAI_API_KEY,
    endpoint=AZURE_OPENAI_ENDPOINT,
    api_version=AZURE_OPENAI_API_VERSION
)

# Define the prompt template
prompt_template = ChatPromptTemplate.from_messages([
    SystemMessagePromptTemplate.from_template(
        """You are an expert in automotive user experience and technical documentation.
Your task is to extract only the core technical noun phrase from a given car manual phrase.

Instructions:
- Return only the extracted technical term as a quoted string, on a separate line.
- Do not explain, rephrase, or return anything else.
- For example, from 'Changing map visuals', extract: "map visuals"."""
    ),
    HumanMessagePromptTemplate.from_template("{car_manual_term}")
])

# Function to extract term
def extract_core_technical_term(phrase: str) -> str:
    formatted_prompt = prompt_template.format_prompt(car_manual_term=phrase)
    messages = formatted_prompt.to_messages()

    response = client.chat.completions.create(
        deployment_name=AZURE_DEPLOYMENT_NAME,
        messages=messages,
        temperature=0.0,
        max_tokens=30
    )

    raw_output = response.choices[0].message.content.strip()

    # Extract quoted term
    import re
    match = re.search(r'"([^"]+)"', raw_output)
    return match.group(1) if match else raw_output

# Test phrase
test_phrase = "Changing map visuals"
core_term = extract_core_technical_term(test_phrase)
print("Extracted:", core_term)
```
