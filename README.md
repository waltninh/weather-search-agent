# Weather & Search Agent

A ReAct agent built with LangChain that can search the web and look up
current weather. Two entrypoints share the same agent: a command-line
script and a Streamlit web app.

Built while following an Agentic AI course, then updated to run on
LangChain 1.x and OpenRouter.

## What it does

Given a question, the agent decides which tool to call, calls it, reads the
result, and repeats until it can answer:

```
Question: Find the capital of India and then find its current weather.

Thought:  I know the capital is New Delhi. Now I need its weather.
Action:   get_weather_data
Action Input: "New Delhi"
Observation: City: New Delhi / Temperature: 34°C / Weather: Sandstorm
Thought:  I now know the final answer.
Final Answer: The capital of India is New Delhi. Its current weather is
              34°C with a sandstorm and 37% humidity.
```

## Tools

| Tool | Source | Purpose |
|---|---|---|
| `TavilySearchResults` | Tavily API | Web search, 2 results per query |
| `get_weather_data` | Weatherstack API | Current temperature, conditions, humidity |

## Requirements

- **Python 3.14** — pinned dependencies are verified on 3.14.5
- API keys for OpenRouter, Tavily, and Weatherstack (all have free tiers)

## Setup

```bash
python -m venv .venv
.venv\Scripts\activate        # Windows
# source .venv/bin/activate   # macOS / Linux

pip install -r requirements.txt
```

Create a `.env` file next to `main.py`:

```dotenv
OPENROUTER_API_KEY="your-openrouter-key"
TAVILY_API_KEY="your-tavily-key"
WEATHERSTACK_API_KEY="your-weatherstack-key"
```

> `.env` holds live credentials — never commit it.

Get keys at [openrouter.ai](https://openrouter.ai),
[app.tavily.com](https://app.tavily.com), and
[weatherstack.com](https://weatherstack.com).

## Run

**Command line** — the question is hardcoded in the script:

```bash
python main.py
```

`verbose=True` prints every `Thought → Action → Observation` step, which is
the point of running this one.

**Web app** — type your own question:

```bash
streamlit run app.py
```

Note that `app.py` needs `streamlit run`, not `python app.py`.

## Files

```text
.
├── main.py           # CLI version, hardcoded question, verbose trace
├── app.py            # Streamlit version, user input + error handling
├── requirements.txt
└── README.md
```

The two scripts are near-identical. `app.py` additionally sets
`handle_parsing_errors=True`, which retries when the model breaks the ReAct
output format instead of crashing.

## Notes on LangChain 1.x

This code uses the classic ReAct agent, which moved packages in LangChain 1.0:

```python
from langchain_classic.agents import create_react_agent, AgentExecutor
```

`langchain.agents` now holds `create_agent`, a LangGraph-based agent with the
tool loop built in — no `AgentExecutor` needed. The classic API is kept here
because its verbose output shows the reasoning loop step by step.

The ReAct prompt is written inline rather than pulled from LangChain Hub.
`hub.pull()` now refuses public prompts by default, since a hub prompt is a
serialized object that can carry executable configuration.#nct
