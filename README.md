# Ollama Python Library

The Ollama Python library provides the easiest way to integrate Python 3.8+ projects with [Ollama](https://github.com/ollama/ollama).

## Getting Started

Before you can use the Ollama Python library locally you need to complete a few steps. You will need to have Ollama installed and an ollama server running on your local machine. You also need to pip install the Ollama python library.

- Download & Install Ollama:
  - See [https://ollama.com/](https://ollama.com/)
- Install the Ollama Python library in your Python environment:
  ```sh
  pip install ollama
  ```
- Run the Ollama server with a specific model:
  - Example: `ollama run llama3`
  - Notes:
    - The model you specify will be automatically downloaded and cached on your machine.
    - **You will need to run this step each time you restart your computer or each time after you kill your ollama server**.
    - You can also run a specific version of a model:
      - See available models [here](https://ollama.com/library).
      - Once at a model page, you can view specific versions and related version info by clicking on `tags` at the top of the model page and selecting the version you want.
      - Example: `ollama run llama3:8b`

## Usage

```python
import ollama
response = ollama.chat(
    # Note: This model should be the same as the one you started with `ollama run`
    model='llama3', 
    messages=[
    {
      'role': 'user',
      'content': 'Why is the sky blue?',
    },
  ]
)
print(response['message']['content'])
```

## Streaming responses

Response streaming can be enabled by setting `stream=True`, modifying function calls to return a Python generator where each part is an object in the stream.

```python
import ollama

stream = ollama.chat(
    model='llama3',
    messages=[{'role': 'user', 'content': 'Why is the sky blue?'}],
    stream=True,
)

for chunk in stream:
  print(chunk['message']['content'], end='', flush=True)
```

## API

The Ollama Python library's API is designed around the [Ollama REST API](https://github.com/ollama/ollama/blob/main/docs/api.md)

### Chat

```python
ollama.chat(model='llama3', messages=[{'role': 'user', 'content': 'Why is the sky blue?'}])
```

### Generate

```python
ollama.generate(model='llama3', prompt='Why is the sky blue?')
```

### List

```python
ollama.list()
```

### Show

```python
ollama.show('llama3')
```

### Create

```python
modelfile='''
FROM llama3
SYSTEM You are mario from super mario bros.
'''

ollama.create(model='example', modelfile=modelfile)
```

### Copy

```python
ollama.copy('llama3', 'user/llama3')
```

### Delete

```python
ollama.delete('llama3')
```

### Pull

```python
ollama.pull('llama3')
```

### Push

```python
ollama.push('user/llama3')
```

### Embeddings

```python
ollama.embeddings(model='llama3', prompt='The sky is blue because of rayleigh scattering')
```

## Custom client

A custom client can be created with the following fields:

- `host`: The Ollama host to connect to
- `timeout`: The timeout for requests

```python
from ollama import Client
client = Client(host='http://localhost:11434')
response = client.chat(model='llama3', messages=[
  {
    'role': 'user',
    'content': 'Why is the sky blue?',
  },
])
```

## Async client

```python
import asyncio
from ollama import AsyncClient

async def chat():
  message = {'role': 'user', 'content': 'Why is the sky blue?'}
  response = await AsyncClient().chat(model='llama3', messages=[message])

asyncio.run(chat())
```

Setting `stream=True` modifies functions to return a Python asynchronous generator:

```python
import asyncio
from ollama import AsyncClient

async def chat():
  message = {'role': 'user', 'content': 'Why is the sky blue?'}
  async for part in await AsyncClient().chat(model='llama3', messages=[message], stream=True):
    print(part['message']['content'], end='', flush=True)

asyncio.run(chat())
```

## Errors

Errors are raised if requests return an error status or if an error is detected while streaming.

```python
model = 'does-not-yet-exist'

try:
  ollama.chat(model)
except ollama.ResponseError as e:
  print('Error:', e.error)
  if e.status_code == 404:
    ollama.pull(model)
```
