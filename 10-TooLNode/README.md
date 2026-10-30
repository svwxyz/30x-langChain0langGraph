# 10 - Tool Node: LangGraph Agent with Tools

A small LangGraph agent that answers questions by deciding when to call tools. The model can run a web search (DuckDuckGo) or add two integers, and the graph loops back to the model until it has an answer.

## What it does

- Binds two tools to an OpenAI chat model (`gpt-5-mini`):
  - `searchEngine` - searches the web with `DuckDuckGoSearchRun`.
  - `addMe` - returns the sum of two numbers.
- Builds a `StateGraph` with two nodes:
  - `AgentNode` - calls the LLM with the message history.
  - `ToolNode` - runs every tool call requested by the last AI message and returns the results as `ToolMessage`s.
- A `route` function checks the last message. If it contains tool calls, the graph goes to `ToolNode`; otherwise it ends.
- The example query asks for the release date of *Avengers: Doomsday* in India, which forces a web search.

## Graph flow

```
START -> AgentNode -- tool calls? --yes--> ToolNode -> AgentNode
                              |
                              no
                              v
                             END
```

The notebook ends by rendering this graph as a Mermaid PNG.

## Requirements

- Python 3.10+ (the project `pyproject.toml` targets 3.14)
- Packages used by the notebook:
  - `langchain`, `langchain-core`, `langchain-openai`, `langchain-community`
  - `langgraph`
  - `ddgs` (needed by `DuckDuckGoSearchRun`)
  - `pydantic`, `python-dotenv`, `ipython`
- An OpenAI API key.

## Setup

1. Create a `.env` file in the folder you run the notebook from (or a parent folder that `load_dotenv()` can find):

   ```env
   OPENAI_API_KEY=your_key_here
   ```

2. Install the dependencies, for example:

   ```bash
   pip install langchain langchain-core langchain-openai langchain-community langgraph ddgs pydantic python-dotenv ipython
   ```

3. Open `main.ipynb` in Jupyter or VS Code and run the cells in order.

## Notes

- `load_dotenv()` loads the API key from `.env`. Never commit that file.
- `DuckDuckGoSearchRun` needs network access.
- The `add_messages` reducer in `graph_schema` appends new messages to the conversation state instead of replacing them.
- Drawing the graph with `draw_mermaid_png()` calls an online renderer, so it also needs network access.
