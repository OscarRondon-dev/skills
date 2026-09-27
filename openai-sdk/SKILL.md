---
name: openai-sdk
description: Comprehensive expert skill for the OpenAI Python SDK. Covers sync/async clients, Chat Completions, Responses API, Vision, Realtime API, Tool Calling (Functions), Structured Outputs, File Uploads, Fine-tuning, and Azure OpenAI integration.
---

# OpenAI Python SDK Skill

Expert guidance for integrating OpenAI's models using the official Python library. This skill covers latest patterns including the Responses API and Realtime capabilities.

## 1. Installation & Setup
```bash
pip install openai
```

### Authentication
```python
import os
from openai import OpenAI

client = OpenAI(
    api_key=os.environ.get("OPENAI_API_KEY"), # Default: uses OPENAI_API_KEY env var
)
```

## 2. Text Generation

### Chat Completions (Classic)
```python
completion = client.chat.completions.create(
    model="gpt-4o",
    messages=[
        {"role": "system", "content": "You are a helpful assistant."},
        {"role": "user", "content": "Hello!"}
    ]
)
print(completion.choices[0].message.content)
```

### Responses API (New)
```python
response = client.responses.create(
    model="gpt-4o",
    instructions="Talk like a pirate.",
    input="How's the weather?"
)
print(response.output_text)
```

### Async Usage
```python
import asyncio
from openai import AsyncOpenAI

client = AsyncOpenAI()

async def main():
    response = await client.chat.completions.create(
        model="gpt-4o",
        messages=[{"role": "user", "content": "Hello async!"}]
    )
    print(response.choices[0].message.content)

asyncio.run(main())
```

## 3. Streaming
```python
stream = client.chat.completions.create(
    model="gpt-4o",
    messages=[{"role": "user", "content": "Write a story."}],
    stream=True,
)
for chunk in stream:
    if chunk.choices[0].delta.content:
        print(chunk.choices[0].delta.content, end="")
```

## 4. Vision
### From URL
```python
response = client.chat.completions.create(
    model="gpt-4o",
    messages=[
        {
            "role": "user",
            "content": [
                {"type": "text", "text": "What's in this image?"},
                {"type": "image_url", "image_url": {"url": "https://example.com/img.jpg"}},
            ],
        }
    ],
)
```

### From Local File (Base64)
```python
import base64

def encode_image(image_path):
    with open(image_path, "rb") as image_file:
        return base64.b64encode(image_file.read()).decode('utf-8')

base64_image = encode_image("path/to/image.jpg")

response = client.chat.completions.create(
    model="gpt-4o",
    messages=[
        {
            "role": "user",
            "content": [
                {"type": "text", "text": "What's in this image?"},
                {"type": "image_url", "image_url": {"url": f"data:image/jpeg;base64,{base64_image}"}},
            ],
        }
    ],
)
```

## 5. Tool Calling (Function Calling)
```python
tools = [{
    "type": "function",
    "function": {
        "name": "get_weather",
        "description": "Get current weather",
        "parameters": {
            "type": "object",
            "properties": {
                "location": {"type": "string"}
            }
        }
    }
}]

response = client.chat.completions.create(
    model="gpt-4o",
    messages=[{"role": "user", "content": "What's the weather in London?"}],
    tools=tools,
)
```

## 6. Structured Outputs (JSON Mode / Schema)
### Pydantic Models (Recommended)
```python
from pydantic import BaseModel

class CalendarEvent(BaseModel):
    name: str
    date: str
    participants: list[str]

completion = client.beta.chat.completions.parse(
    model="gpt-4o",
    messages=[{"role": "user", "content": "Meeting with John tomorrow at 10am"}],
    response_format=CalendarEvent,
)
event = completion.choices[0].message.parsed
```

## 7. Realtime API (WebSockets)
```python
import asyncio
from openai import AsyncOpenAI

async def main():
    client = AsyncOpenAI()
    async with client.realtime.connect(model="gpt-4o-realtime-preview") as connection:
        await connection.session.update(session={"modalities": ["text", "audio"]})
        await connection.conversation.item.create(
            item={"type": "message", "role": "user", "content": [{"type": "input_text", "text": "Hello!"}]}
        )
        await connection.response.create()
        async for event in connection:
            if event.type == "response.audio.delta":
                # Handle audio stream
                pass
```

## 8. Files & Fine-tuning
### Upload File
```python
from pathlib import Path
file = client.files.create(
  file=Path("training_data.jsonl"),
  purpose="fine-tune"
)
```

### Start Fine-tuning
```python
job = client.fine_tuning.jobs.create(
  training_file=file.id,
  model="gpt-4o-mini"
)
```

## 9. Azure OpenAI
```python
from openai import AzureOpenAI

client = AzureOpenAI(
    api_version="2024-05-01-preview",
    azure_endpoint=os.getenv("AZURE_OPENAI_ENDPOINT"),
    api_key=os.getenv("AZURE_OPENAI_API_KEY"),
)

deployment_name = "my-gpt-4o-deployment"
```

## 10. Error Handling & Retries
```python
import openai

client = OpenAI(max_retries=3, timeout=20.0) # Configure retries and timeouts globally

try:
    client.chat.completions.create(...)
except openai.APIConnectionError as e:
    print("Server could not be reached")
except openai.RateLimitError as e:
    print("429: Rate limit exceeded")
except openai.APIStatusError as e:
    print(f"Status code: {e.status_code}")
```

## 11. Pagination
The SDK provides auto-paginating iterators for list endpoints.
```python
# Automatic pagination
for job in client.fine_tuning.jobs.list(limit=20):
    print(job.id)

# Manual pagination control
page = client.fine_tuning.jobs.list(limit=20)
if page.has_next_page():
    next_page = page.get_next_page()
```

## 12. Advanced HTTP & Auth
### Custom HTTP Client (Proxies / aiohttp)
```python
import httpx
from openai import OpenAI, DefaultHttpxClient

client = OpenAI(
    http_client=DefaultHttpxClient(
        proxy="http://my.proxy.com",
        transport=httpx.HTTPTransport(local_address="0.0.0.0"),
    )
)
```

### Workload Identity (GCP Example)
```python
from openai import OpenAI
from openai.auth import gcp_id_token_provider

client = OpenAI(
    workload_identity={
        "identity_provider_id": "idp-123",
        "service_account_id": "sa-456",
        "provider": gcp_id_token_provider(audience="https://api.openai.com/v1"),
    },
)
```

## 13. Webhooks & Raw Responses
### Verifying Webhooks
```python
# Assuming using Flask
@app.route("/webhook", methods=["POST"])
def webhook():
    payload = request.get_data(as_text=True)
    try:
        # Parses AND verifies the webhook payload
        event = client.webhooks.unwrap(payload, request.headers)
        if event.type == "response.completed":
            print("Done!")
    except Exception as e:
        return "Invalid signature", 400
```

### Raw / Streaming Responses
Access raw HTTP responses or stream lines lazily without reading the entire body at once.
```python
with client.chat.completions.with_streaming_response.create(
    model="gpt-4o",
    messages=[{"role": "user", "content": "Hello"}]
) as response:
    print(response.headers.get("X-My-Header"))
    for line in response.iter_lines():
        print(line)
```

## 14. Advanced Debugging & Internal Access
### Request IDs (For OpenAI Support)
All object responses provide a `_request_id` property. For failed requests, access it via the exception.
```python
# Success
response = client.responses.create(model="gpt-4o", input="Hello")
print(response._request_id) # e.g. req_123

# Failure
try:
    client.chat.completions.create(...)
except openai.APIStatusError as exc:
    print(exc.request_id)
    raise exc
```

### Undocumented Endpoints & Extra Params
If a new OpenAI feature isn't in the SDK yet, you can pass custom parameters or make raw requests.
```python
import httpx

# Pass undocumented parameters
client.chat.completions.create(
    model="gpt-4o",
    messages=[{"role": "user", "content": "Hi"}],
    extra_body={"my_beta_feature": True},
    extra_headers={"X-My-Header": "foo"}
)

# Call completely undocumented endpoints
response = client.post("/foo", cast_to=httpx.Response, body={"param": 1})
```

### Differentiating `null` from missing fields
To tell if a field was returned as `null` or omitted entirely by the API:
```python
if response.my_field is None:
    if 'my_field' not in response.model_fields_set:
        print('Field completely missing from JSON')
    else:
        print('Field explicitly set to null')
```

### Logging
Set the environment variable to see raw HTTP traffic.
```bash
export OPENAI_LOG=debug # or 'info'
```
