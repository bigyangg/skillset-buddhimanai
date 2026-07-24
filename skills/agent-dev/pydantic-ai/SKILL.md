---
name: pydantic-ai
description: Build type-safe AI agents and structured LLM workflows with PydanticAI. Use when a user says "PydanticAI", "pydantic agent", "type-safe AI", "structured LLM output in Python", "pydantic AI tools", "dependency injection agent", "AI validation", or "typed agent responses".
---

# PydanticAI

## What This Does

Build production-grade AI agents and LLM workflows in Python using PydanticAI — the framework that brings Pydantic's type safety and validation to AI responses, tool calls, and agent state. Covers agents, structured outputs, tools, dependency injection, and streaming.

## Why PydanticAI Over Raw SDK

| Feature | Raw OpenAI SDK | PydanticAI |
|---------|---------------|-----------|
| Response validation | Manual | Automatic (Pydantic models) |
| Tool definition | Dict schemas | Type-annotated Python functions |
| Dependency injection | Manual | Built-in `RunContext` |
| Streaming | Manual iteration | Unified streaming API |
| Multi-model support | Per-SDK | Single API (OpenAI, Gemini, Anthropic, etc.) |
| Testing | Hard to mock | Built-in `TestModel` |

## Setup

```bash
pip install pydantic-ai
# or
uv add pydantic-ai
```

## Basic Agent

```python
from pydantic_ai import Agent

agent = Agent(
    'openai:gpt-4o',
    system_prompt='You are a concise technical assistant. Always be specific and actionable.',
)

result = agent.run_sync('Explain caching in 3 bullet points.')
print(result.data)   # str
print(result.usage)  # token usage
```

## Structured Output (Type-Safe Responses)

```python
from pydantic import BaseModel, Field
from pydantic_ai import Agent

class ProductAnalysis(BaseModel):
    name:          str
    sentiment:     str         = Field(description="positive | neutral | negative")
    score:         float       = Field(ge=0.0, le=10.0)
    key_strengths: list[str]   = Field(max_length=5)
    key_weaknesses: list[str]  = Field(max_length=5)
    summary:       str         = Field(max_length=300)

agent = Agent(
    'openai:gpt-4o',
    result_type=ProductAnalysis,    # enforces structured output
    system_prompt='Analyze products objectively based on user reviews.',
)

result = agent.run_sync(
    'Analyze this review: "Great battery life but the camera is disappointing."'
)

analysis: ProductAnalysis = result.data   # fully typed!
print(analysis.sentiment)   # "neutral"
print(analysis.score)       # 6.5
```

## Agents with Tools

```python
import httpx
from pydantic import BaseModel
from pydantic_ai import Agent, RunContext

class WeatherResult(BaseModel):
    city:        str
    temperature: float
    condition:   str
    humidity:    int

agent = Agent(
    'openai:gpt-4o',
    result_type=WeatherResult,
    system_prompt='Use the available tools to fetch real weather data before responding.',
)

@agent.tool_plain
def get_weather(city: str, country_code: str = 'US') -> dict:
    """Fetch current weather for a city. Returns temperature in Celsius and conditions."""
    try:
        resp = httpx.get(
            f'https://wttr.in/{city}?format=j1',
            timeout=5
        )
        data = resp.json()
        current = data['current_condition'][0]
        return {
            'city':        city,
            'temp_c':      float(current['temp_C']),
            'condition':   current['weatherDesc'][0]['value'],
            'humidity':    int(current['humidity']),
        }
    except Exception as e:
        return {'error': str(e)}

result = agent.run_sync('What is the weather in Paris right now?')
print(result.data)   # WeatherResult(...) — structured and validated
```

## Dependency Injection

```python
from dataclasses import dataclass
from pydantic_ai import Agent, RunContext
import asyncpg

@dataclass
class Deps:
    db_pool:    asyncpg.Pool
    user_id:    str
    user_tier:  str   # 'free' | 'pro' | 'enterprise'

agent = Agent(
    'openai:gpt-4o',
    deps_type=Deps,
    system_prompt=(
        "You are a helpful product assistant. "
        "Respect the user's plan tier when recommending features."
    ),
)

@agent.tool
async def get_user_history(ctx: RunContext[Deps], limit: int = 10) -> list[dict]:
    """Retrieve the authenticated user's recent activity."""
    rows = await ctx.deps.db_pool.fetch(
        'SELECT * FROM activity WHERE user_id = $1 ORDER BY created_at DESC LIMIT $2',
        ctx.deps.user_id, limit
    )
    return [dict(r) for r in rows]

@agent.system_prompt
async def dynamic_system_prompt(ctx: RunContext[Deps]) -> str:
    return f"The user is on the '{ctx.deps.user_tier}' plan."

# Run with injected dependencies
async def main():
    pool = await asyncpg.create_pool(DATABASE_URL)
    deps = Deps(db_pool=pool, user_id='uuid-123', user_tier='pro')
    result = await agent.run("Show me my recent activity", deps=deps)
    print(result.data)
```

## Streaming

```python
import asyncio
from pydantic_ai import Agent

agent = Agent('openai:gpt-4o')

async def stream_response(prompt: str):
    async with agent.run_stream(prompt) as response:
        async for text in response.stream_text():
            print(text, end='', flush=True)
        print()   # newline at end
        print(f'\nTokens used: {response.usage()}')

asyncio.run(stream_response('Write a short poem about Python.'))
```

## Multi-Agent Pattern

```python
from pydantic import BaseModel
from pydantic_ai import Agent

class ResearchReport(BaseModel):
    topic:    str
    findings: list[str]
    sources:  list[str]

class BlogPost(BaseModel):
    title:    str
    content:  str
    word_count: int

# Agent 1: Researcher
researcher = Agent(
    'openai:gpt-4o',
    result_type=ResearchReport,
    system_prompt='You research topics thoroughly and cite sources.',
)

# Agent 2: Writer (receives researcher output as context)
writer = Agent(
    'openai:gpt-4o-mini',
    result_type=BlogPost,
    system_prompt='You write engaging blog posts from research reports.',
)

async def research_and_write(topic: str) -> BlogPost:
    # Step 1: Research
    research = await researcher.run(f'Research the topic: {topic}')
    
    # Step 2: Write (inject research as context)
    post = await writer.run(
        f'Write a blog post based on this research:\n{research.data.model_dump_json()}',
    )

    return post.data
```

## Testing

```python
from pydantic_ai import Agent
from pydantic_ai.models.test import TestModel

agent = Agent(
    'openai:gpt-4o',
    result_type=ProductAnalysis,
)

# Use TestModel to avoid real API calls in tests
def test_product_analysis():
    with agent.override(model=TestModel()):
        result = agent.run_sync('Analyze this product.')
        # TestModel returns valid Pydantic model instances
        assert isinstance(result.data, ProductAnalysis)
```

## Model Switching

```python
# All of these work with the same agent code
agent = Agent('openai:gpt-4o')
agent = Agent('openai:gpt-4o-mini')
agent = Agent('anthropic:claude-3-5-sonnet-latest')
agent = Agent('google:gemini-2.0-flash')
agent = Agent('groq:llama-3.3-70b-versatile')
```

## Checklist

- [ ] `result_type` set for all agents (use `BaseModel`, not `str` for production)
- [ ] All Pydantic fields have `Field(description=...)` for LLM guidance
- [ ] Tools have clear docstrings — tool docstrings become tool descriptions
- [ ] `deps_type` used for database connections, auth, and shared state
- [ ] `TestModel` used in unit tests — no real API calls in CI
- [ ] Streaming used for any response > 500 tokens
- [ ] Error handling with `try/except` inside tools (return error dict, don't raise)
