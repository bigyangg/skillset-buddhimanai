---
name: crewai
description: Build multi-agent AI systems with CrewAI. Use when a user says "multi-agent", "CrewAI", "agent crew", "agent pipeline", "sequential agents", "parallel agents", "agent roles", "AI team", "research + write pipeline", "agent tools", "crew tasks", or "orchestrate multiple AI agents".
---

# CrewAI

## What This Does

Design and implement multi-agent AI systems using CrewAI — define agent roles, assign tools, chain tasks sequentially or in parallel, and orchestrate complex workflows that no single agent can handle well alone. Covers agent design, task decomposition, tool integration, and output validation.

## Core Concepts

| Concept | Description |
|---------|-------------|
| **Agent** | An autonomous unit with a role, goal, backstory, and optional tools |
| **Task** | A discrete unit of work assigned to an agent with an expected output |
| **Crew** | A team of agents working together on a set of tasks |
| **Process** | `sequential` (one task after another) or `hierarchical` (manager delegates) |
| **Tool** | A function an agent can call (search, scrape, calculate, API call) |

## Non-Negotiables

- **Single responsibility per agent.** Each agent owns one domain. A "do-everything" agent is an anti-pattern.
- **Concrete expected outputs.** Every task must have a specific, verifiable `expected_output`. Vague outputs produce vague results.
- **Context passing is automatic** in sequential processes — the output of task N feeds task N+1. Design tasks with this chain in mind.
- **Human-readable backstories.** Agent backstories shape LLM behavior more than you'd expect. Write them as if briefing a real expert.
- **Tool errors are caught by the agent.** Agents retry tool failures — but if a tool consistently fails, the agent will hallucinate. Fix the tool.

## Setup

```bash
pip install crewai crewai[tools]
# or
uv add crewai crewai-tools
```

```python
# Set environment variables
# OPENAI_API_KEY or your LLM provider key
```

## Basic Crew Structure

```python
from crewai import Agent, Task, Crew, Process
from crewai_tools import SerperDevTool, ScrapeWebsiteTool

# ─── Tools ───────────────────────────────────────────────────────────────────
search_tool = SerperDevTool()
scrape_tool = ScrapeWebsiteTool()

# ─── Agents ──────────────────────────────────────────────────────────────────
researcher = Agent(
    role="Senior Market Researcher",
    goal="Find accurate, up-to-date information about {topic} from credible sources",
    backstory=(
        "You are a veteran researcher with 15 years of experience in competitive "
        "intelligence. You are rigorous about source verification and always cite "
        "your findings. You never fabricate data."
    ),
    tools=[search_tool, scrape_tool],
    verbose=True,
    max_iter=5,               # prevent infinite loops
    allow_delegation=False,   # this agent doesn't hand off work
)

writer = Agent(
    role="Technical Content Strategist",
    goal="Transform research findings into clear, compelling, and accurate content",
    backstory=(
        "You are a former engineer turned content strategist. You excel at making "
        "complex technical topics accessible without losing accuracy. You write in "
        "a direct, confident style — no filler, no hedging."
    ),
    tools=[],
    verbose=True,
    allow_delegation=False,
)

# ─── Tasks ───────────────────────────────────────────────────────────────────
research_task = Task(
    description=(
        "Research the current state of {topic}. Cover: "
        "1) Key players and market leaders "
        "2) Recent developments in the last 6 months "
        "3) Common pain points users report "
        "4) Emerging alternatives or disruptors. "
        "Cite all sources with URLs."
    ),
    expected_output=(
        "A structured research report in markdown format with: "
        "an executive summary (150 words), "
        "4 clearly labeled sections matching the research areas, "
        "and a sources list with verified URLs."
    ),
    agent=researcher,
)

writing_task = Task(
    description=(
        "Using the research report, write a 1,200-word blog post about {topic} for "
        "a technical audience (senior engineers). The post must: "
        "have a strong hook in the first paragraph, "
        "use the research findings as supporting evidence, "
        "include 3 concrete takeaways, "
        "and end with a specific call to action."
    ),
    expected_output=(
        "A complete 1,200-word blog post in markdown with: "
        "title (H1), 4-6 sections with H2 headings, "
        "inline citations, and a clear CTA at the end."
    ),
    agent=writer,
    context=[research_task],    # explicitly depend on research output
)

# ─── Crew ────────────────────────────────────────────────────────────────────
crew = Crew(
    agents=[researcher, writer],
    tasks=[research_task, writing_task],
    process=Process.sequential,   # researcher → writer
    verbose=True,
    memory=True,                  # enable cross-task memory
)

result = crew.kickoff(inputs={"topic": "AI coding assistants"})
print(result.raw)
```

## Custom Tools

```python
from crewai.tools import BaseTool
from pydantic import BaseModel, Field
import httpx

class WeatherInput(BaseModel):
    city: str = Field(description="Name of the city to fetch weather for")

class WeatherTool(BaseTool):
    name:        str = "Weather Checker"
    description: str = "Fetches the current weather conditions for a given city"
    args_schema: type[BaseModel] = WeatherInput

    def _run(self, city: str) -> str:
        try:
            url      = f"https://wttr.in/{city}?format=3"
            response = httpx.get(url, timeout=5)
            return response.text
        except Exception as e:
            return f"Weather fetch failed: {e}"

# Use in agent
weather_agent = Agent(
    role="Weather Analyst",
    goal="Provide accurate current weather information",
    backstory="You specialize in meteorological data interpretation.",
    tools=[WeatherTool()],
)
```

## Hierarchical Process (Manager Pattern)

```python
from crewai import Agent, Task, Crew, Process

# Manager agent orchestrates the other agents
manager = Agent(
    role="Project Manager",
    goal="Efficiently coordinate tasks to deliver high-quality results on time",
    backstory="Expert PM with a track record of delivering complex projects.",
    allow_delegation=True,   # REQUIRED for manager
    verbose=True,
)

# Worker agents
analyst   = Agent(role="Data Analyst",   goal="...", backstory="...", tools=[...])
developer = Agent(role="Python Developer", goal="...", backstory="...", tools=[...])

crew = Crew(
    agents=[analyst, developer],
    tasks=[...],
    manager_agent=manager,
    process=Process.hierarchical,  # manager delegates sub-tasks
    verbose=True,
)
```

## YAML Configuration (Recommended for Production)

```yaml
# config/agents.yaml
researcher:
  role: "Senior Market Researcher"
  goal: "Find accurate information about {topic}"
  backstory: "Veteran researcher with rigorous source verification."

writer:
  role: "Technical Content Strategist"
  goal: "Transform research into compelling content"
  backstory: "Engineer turned writer, expert at clarity without sacrificing depth."
```

```yaml
# config/tasks.yaml
research_task:
  description: "Research {topic}: key players, recent news, pain points, disruptors."
  expected_output: "Structured markdown report with cited sources."
  agent: researcher

writing_task:
  description: "Write 1200-word blog post from research report for senior engineers."
  expected_output: "Complete markdown blog post with CTA."
  agent: writer
  context: [research_task]
```

```python
# crew.py (uses YAML config)
from crewai import Crew, Agent, Task, Process

class ContentCrew:
    agents_config = 'config/agents.yaml'
    tasks_config  = 'config/tasks.yaml'

    def researcher(self) -> Agent:
        return Agent(config=self.agents_config['researcher'], tools=[SerperDevTool()])

    def writer(self) -> Agent:
        return Agent(config=self.agents_config['writer'])

    def run(self, topic: str):
        crew = Crew(
            agents=[self.researcher(), self.writer()],
            tasks=[...],
            process=Process.sequential,
        )
        return crew.kickoff(inputs={'topic': topic})
```

## Common Crew Patterns

| Pattern | Use Case |
|---------|---------|
| Research → Write | Content generation, reports |
| Scrape → Analyze → Summarize | Data extraction pipelines |
| Plan → Code → Review | Software development tasks |
| Draft → Critique → Improve | Document refinement loops |
| Search → Rank → Recommend | Recommendation systems |

## Debugging Checklist

- [ ] Set `verbose=True` on all agents during development
- [ ] `expected_output` is specific and measurable
- [ ] Tool errors are observable (tools log their failures)
- [ ] `max_iter` set to prevent runaway loops
- [ ] LLM model set to `gpt-4o` for complex tasks, `gpt-4o-mini` for simple ones
- [ ] `context=[prior_task]` wired for tasks that depend on earlier outputs
- [ ] Backstories written to constrain behavior, not just describe the role
