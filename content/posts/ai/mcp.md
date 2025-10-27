+++
date = '2025-10-28T00:00:00+00:00'
draft = false
title = 'Model Context Protocol (MCP) Overview'
tags = ['AI', 'MCP']
+++

Model Context Protocol (MCP) is an open protocol designed to standardize the way context is shared, managed, and utilized across AI models, tools, and applications. MCP enables seamless interoperability between different AI systems, making it easier to build, extend, and orchestrate complex AI workflows.

## MCP: Model Context Protocol

MCP provides a common language and set of conventions for describing, exchanging, and managing context in AI-powered systems. It is designed to be model-agnostic and works across a wide range of AI models, frameworks, and platforms.

### Key Features
- **Standardized Context Exchange:** Defines a universal format for sharing context between models, tools, and applications.
- **Model-Agnostic:** Works with any AI model (LLMs, vision, speech, etc.) and any framework or provider.
- **Composable Workflows:** Enables chaining and orchestration of multiple models and tools by passing context seamlessly.
- **Extensible Schema:** Supports custom context types and extensions for specialized use cases.
- **Open and Interoperable:** Community-driven, open standard with reference implementations and SDKs.

### Typical Use Cases
- Chaining multiple AI models (e.g., LLM + image model) in a single workflow
- Integrating context from external tools (databases, APIs, user input) into model prompts
- Building multi-modal AI applications (text, image, audio, etc.)
- Sharing context between agents, plugins, or microservices
- Standardizing context management in enterprise AI systems

### What Can MCP Do?

MCP enables you to:

- **Orchestrate Multi-Model Workflows:** Pass context between different models and tools, enabling complex, multi-step AI pipelines.
- **Integrate External Data:** Seamlessly inject data from APIs, databases, or user input into model context.
- **Enable Agent Collaboration:** Allow multiple AI agents or plugins to share and update context in a standardized way.
- **Promote Reusability:** Build modular, reusable components that interoperate via the MCP standard.
- **Enhance Traceability:** Track and audit context as it flows through your AI system for transparency and debugging.

### What MCP Cannot Do

While MCP is a powerful protocol for context management, it has some limitations:

- **Not a Model or Framework:** MCP is a protocol, not an AI model, framework, or runtime. It does not provide inference, training, or model hosting.
- **No Built-in UI or Automation:** MCP does not include workflow automation, scheduling, or user interfaces—these must be built separately or integrated with other tools.
- **Not a Data Store:** MCP is not a database or persistent storage solution; it standardizes context exchange, not storage.
- **Requires Adoption:** Interoperability depends on adoption by tools, models, and platforms in your stack.

### Integration and Deployment

- **Reference Implementations:** MCP provides SDKs and libraries for popular languages (Python, TypeScript, etc.) to help you adopt the protocol.
- **Open Standard:** Documentation, schemas, and community resources are available at [modelcontextprotocol.io](https://modelcontextprotocol.io/docs/getting-started/intro).
- **Flexible Integration:** Can be used in cloud, on-premises, or hybrid environments, and with any AI provider or model.

### Example: Using MCP with FastAPI and fastMCP

Below is a practical example of using the FastMCP server with FastAPI, following the correct usage pattern:

First, install fastmcp:

```bash
pip install fastmcp httpx
```

#### Canonical Example: FastMCP as a Server/Controller

```python
from typing import Any
import httpx
from mcp.server.fastmcp import FastMCP

# Initialize FastMCP server with a workflow name
mcp = FastMCP("weather")

NWS_API_BASE = "https://api.weather.gov"
USER_AGENT = "weather-app/1.0"

@mcp.step()
def get_weather(context: dict[str, Any]) -> dict[str, Any]:
    lat = context.get("lat")
    lon = context.get("lon")
    if not lat or not lon:
        return {"error": "Missing latitude or longitude"}
    url = f"{NWS_API_BASE}/points/{lat},{lon}/forecast"
    headers = {"User-Agent": USER_AGENT}
    with httpx.Client() as client:
        resp = client.get(url, headers=headers)
        resp.raise_for_status()
        forecast = resp.json()
    return {"forecast": forecast}

# To run the FastAPI app:
#   uvicorn mcp.server.fastmcp:app --reload
```

This example demonstrates how to define a workflow step using FastMCP, fetch weather data from an external API, and return the result in a standardized MCP context.

---

Legacy/Alternative patterns (for reference) are shown below, but the above is the recommended usage for fastmcp >= 0.2.0.

Below are several examples of how to use the fastMCP library to manage model context in a FastAPI application. These demonstrate how you can standardize context exchange in your AI workflows using MCP.

First, install fastMCP:

```bash
pip install fastmcp
```

#### 1. Basic: Receive and Return Context

```python
from fastapi import FastAPI, Request
from fastmcp import ModelContext

app = FastAPI()

@app.post("/run-mcp-workflow/")
async def run_workflow(request: Request):
    context = await request.json()
    mcp_context = ModelContext.from_dict(context)
    user_id = mcp_context.get("user_id")
    task = mcp_context.get("task")
    result = {"message": f"Ran workflow for user {user_id} and task {task}"}
    mcp_context["result"] = result
    return mcp_context.to_dict()
```

#### 2. Create a New Context from Scratch

```python
from fastmcp import ModelContext

@app.post("/create-context/")
async def create_context():
    mcp_context = ModelContext(user_id="user123", task="summarization")
    mcp_context["status"] = "created"
    return mcp_context.to_dict()
```

#### 3. Update an Existing Context

```python
@app.post("/update-context/")
async def update_context(request: Request):
    context = await request.json()
    mcp_context = ModelContext.from_dict(context)
    mcp_context["updated"] = True
    mcp_context["timestamp"] = "2025-10-28T12:00:00Z"
    return mcp_context.to_dict()
```

#### 4. Return Only a Subset of the Context

```python
@app.post("/context-summary/")
async def context_summary(request: Request):
    context = await request.json()
    mcp_context = ModelContext.from_dict(context)
    summary = {"user_id": mcp_context.get("user_id"), "task": mcp_context.get("task")}
    return summary
```

#### 5. Validate Context and Handle Errors

```python
from fastapi import HTTPException

@app.post("/validate-context/")
async def validate_context(request: Request):
    context = await request.json()
    try:
        mcp_context = ModelContext.from_dict(context)
        if "user_id" not in mcp_context:
            raise ValueError("Missing user_id")
    except Exception as e:
        raise HTTPException(status_code=400, detail=str(e))
    return {"status": "valid", "context": mcp_context.to_dict()}
```

#### 6. Use Context to Route to Different Logic

```python
@app.post("/route-by-task/")
async def route_by_task(request: Request):
    context = await request.json()
    mcp_context = ModelContext.from_dict(context)
    task = mcp_context.get("task")
    if task == "summarization":
        result = {"summary": "This is a summary."}
    elif task == "classification":
        result = {"class": "positive"}
    else:
        result = {"message": "Unknown task."}
    mcp_context["result"] = result
    return mcp_context.to_dict()
```

These examples show how to receive, create, update, validate, and use context in different ways using MCP, making your AI APIs interoperable and composable.

---

## Minimal Example: MCP Server and Client

This section demonstrates a minimal, end-to-end workflow using MCP: a server that exposes a workflow step, and a client that connects and invokes it.

### MCP Server Example (FastAPI + FastMCP)

```python
from typing import Any
import httpx
from mcp.server.fastmcp import FastMCP

# Initialize FastMCP server with a workflow name
mcp = FastMCP("weather")

NWS_API_BASE = "https://api.weather.gov"
USER_AGENT = "weather-app/1.0"

@mcp.step()
def get_weather(context: dict[str, Any]) -> dict[str, Any]:
    lat = context.get("lat")
    lon = context.get("lon")
    if not lat or not lon:
        return {"error": "Missing latitude or longitude"}
    url = f"{NWS_API_BASE}/points/{lat},{lon}/forecast"
    headers = {"User-Agent": USER_AGENT}
    with httpx.Client() as client:
        resp = client.get(url, headers=headers)
        resp.raise_for_status()
        forecast = resp.json()
    return {"forecast": forecast}

# To run the server:
#   uvicorn mcp.server.fastmcp:app --reload
```

### MCP Client Example (Python Async)

```python
import asyncio
from mcp import ClientSession, StdioServerParameters
from mcp.client.stdio import stdio_client

async def main():
    # Connect to the MCP server (assumes server is running and accessible via stdio)
    async with stdio_client(StdioServerParameters(command=["python", "-m", "mcp.server.fastmcp", "weather"])) as client:
        async with ClientSession(client) as session:
            # Send context to the server's workflow step
            context = {"lat": 39.7456, "lon": -97.0892}
            result = await session.run_step("get_weather", context)
            print("Server response:", result)

if __name__ == "__main__":
    asyncio.run(main())
```

These two examples together show how to define a workflow step on the server and invoke it from a Python client using MCP.

---

For more details, see the [official MCP documentation](https://modelcontextprotocol.io/docs/getting-started/intro).
