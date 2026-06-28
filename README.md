# UdaPlay AI Research Agent for the Video Game Industry

**Course:** Udacity Agentic AI — Building Agents  
**Project:** Agentic AI Project 3 — UdaPlay AI Research Agent  
**Submission:** June 2026

A notebook-based AI research agent that answers video-game industry questions using a two-stage knowledge workflow: first retrieve from a local ChromaDB game knowledge base, then fall back to Tavily web search when local evidence is insufficient.

---

## Project Overview

UdaPlay is an AI research assistant for video-game questions such as release dates, platforms, publishers, genres, and availability. The project demonstrates how an agent can combine internal retrieval, retrieval-quality evaluation, web search, and state-machine control to produce grounded answers instead of relying only on the LLM's memory.

The project is split into two main parts:

1. **Offline RAG pipeline** — index the provided game JSON files into a persistent ChromaDB vector database.
2. **Agent workflow** — use tools and a state machine so the agent can retrieve, evaluate, search the web if needed, and produce a source-aware answer.

Core agentic AI patterns demonstrated:

- **Retrieval-Augmented Generation (RAG)** over local game records
- **Vector search** using ChromaDB and OpenAI embeddings
- **Tool calling** with Python functions exposed to the LLM
- **Retrieval evaluation** before final answering
- **Tavily web-search fallback** for missing or weak local evidence
- **State-machine orchestration** for LLM/tool loops
- **Short-term session memory** across agent runs
- **Retry hardening** for API robustness

---

## Status

Project implementation is complete and runs end-to-end locally.

Completed items:

- ChromaDB persistent vector database setup
- OpenAI/Vocareum embedding configuration
- All 15 game JSON files processed and indexed
- Semantic search demonstration in Part 1
- `retrieve_game` tool for local vector search
- `evaluate_retrieval` tool for evidence-quality checks
- `game_web_search` tool using Tavily
- State-machine based `Agent` workflow
- Three required demo queries executed successfully
- Tavily fallback validated on the PlayStation 5 / Mortal Kombat X query
- API retry protection added with max retries set to 10
- Secrets and generated local artifacts excluded from git

---

## Project Structure

```text
.
├── Udaplay_01_starter_project.ipynb   # Part 1: Offline RAG and ChromaDB indexing
├── Udaplay_02_starter_project.ipynb   # Part 2: Agent tools, workflow, and demo queries
├── games/                             # Provided video-game JSON records
│   ├── 001.json
│   ├── 002.json
│   └── ...
├── lib/                               # Starter/supporting agent framework code
│   ├── agents.py                      # Agent abstraction and state-machine wiring
│   ├── llm.py                         # OpenAI chat wrapper with retry configuration
│   ├── memory.py                      # Short-term memory/session storage
│   ├── messages.py                    # Message classes
│   ├── state_machine.py               # Generic workflow/state-machine engine
│   ├── tooling.py                     # Tool wrapper and decorator
│   └── vector_db.py                   # Vector database helper abstractions
├── config.env.example                 # Environment variable template without secrets
├── pyproject.toml                     # Python dependencies for local execution
├── uv.lock                            # Locked dependency file
├── README.md                          # Project overview and usage guide
└── .gitignore                         # Excludes secrets, logs, DBs, zips, and local study assets
```

Local-only files intentionally not committed:

```text
.env
config.env
.venv/
chromadb/
Logs/
submission/
project-study.md
project-study.html
project-requirements.md
LOCAL_PROJECT_NOTES.md
*.zip
__pycache__/
```

---

## Main Notebook Sections

### Part 1 — Offline RAG

`Udaplay_01_starter_project.ipynb` builds the local retrieval layer:

1. Load environment variables
2. Configure OpenAI/Vocareum API access
3. Create a persistent ChromaDB client
4. Configure OpenAI embeddings
5. Create or load the `udaplay` collection
6. Read the provided `games/*.json` records
7. Convert each game into searchable document text and metadata
8. Upsert all 15 records into ChromaDB
9. Run a semantic-search smoke test

### Part 2 — Agent Workflow

`Udaplay_02_starter_project.ipynb` builds the research agent:

1. Load environment variables
2. Load the existing ChromaDB collection
3. Configure Tavily search
4. Define `retrieve_game`
5. Define `evaluate_retrieval`
6. Define `game_web_search`
7. Create the UdaPlay agent with state-machine workflow
8. Run the required demo questions
9. Show tool usage and final answers
10. Demonstrate session memory evidence

---

## Setup

### Requirements

- Python 3.11+
- Jupyter Notebook, JupyterLab, or VS Code notebook support
- OpenAI-compatible API key through Udacity/Vocareum
- Tavily API key for web-search fallback

### Create a virtual environment

Using `uv`:

```bash
uv venv
source .venv/bin/activate
```

On Windows PowerShell:

```powershell
uv venv
.venv\Scripts\Activate.ps1
```

### Install dependencies

```bash
uv sync
```

If not using `uv`, install the dependencies listed in `pyproject.toml` with your preferred environment manager.

---

## Environment Variables

Copy the example file and add local credentials:

```bash
cp config.env.example config.env
```

On Windows PowerShell:

```powershell
Copy-Item config.env.example config.env
```

Required variables:

```text
OPENAI_API_KEY="your_vocareum_or_openai_key"
CHROMA_OPENAI_API_KEY="your_vocareum_or_openai_key"
TAVILY_API_KEY="your_tavily_key"
OPENAI_BASE_URL="https://openai.vocareum.com/v1"
```

`config.env` is ignored by git and must not be committed.

---

## Running the Project

Open and run the notebooks in order:

```text
Udaplay_01_starter_project.ipynb
Udaplay_02_starter_project.ipynb
```

Expected flow:

1. Run Part 1 to build the ChromaDB vector database.
2. Run Part 2 to execute the UdaPlay agent.
3. Confirm the tool paths and final answers printed in the notebook output.

The three demonstration questions are:

```text
When were Pokémon Gold and Silver released?
Which one was the first 3D platformer Mario game?
Was Mortal Kombat X released for PlayStation 5?
```

---

## Agent Tools

| Tool | Purpose |
|---|---|
| `retrieve_game` | Searches the local ChromaDB collection for relevant game records. |
| `evaluate_retrieval` | Checks whether retrieved local evidence is useful enough to answer. |
| `game_web_search` | Uses Tavily to search the web when local evidence is weak or incomplete. |

---

## Processing Flow

```text
User Question
     │
     v
UdaPlay Agent
     │
     ├── 1. retrieve_game → Search local ChromaDB game records
     │
     ├── 2. evaluate_retrieval → Decide if local evidence is enough
     │
     └── 3. game_web_search → Use Tavily if local evidence is weak
     │
     v
Final Answer with source awareness
```

The most important behavior is the fallback decision: the agent should not blindly answer from weak local retrieval. It should search the web when the local vector database does not provide enough evidence.

---

## Key Features

- **Persistent ChromaDB collection** — Local game records are stored in `chromadb/` for reuse across notebook runs.
- **OpenAI embeddings** — Game records and queries are embedded for semantic retrieval.
- **Tool-calling agent** — The LLM can request Python tools and receive their results.
- **Retrieval-quality evaluation** — Retrieved documents are checked before final answer generation.
- **Tavily fallback** — Web search is used for questions not fully answered by the local dataset.
- **State-machine workflow** — The agent loop is explicit: prepare messages, call LLM, execute tools, return observations, and terminate.
- **Session memory** — Runs are stored by session ID through short-term memory.
- **Retry logic** — OpenAI/Vocareum, Chroma embedding/query/upsert calls, and Tavily search are configured for up to 10 retry attempts.
- **Secret-safe repository** — Real API keys, local vector DB files, logs, submission zips, and study assets are excluded from version control.

---

## Validation Results

The notebooks were executed end-to-end successfully.

| Check | Result |
|---|---|
| Part 1 notebook execution | Passed |
| Part 2 notebook execution | Passed |
| Game records indexed | 15 of 15 |
| Local RAG query | Passed |
| Agent demo queries | Passed |
| Tavily fallback query | Passed |
| Tracked secret scan | Clean |

Example tool paths:

| Question | Tool Path |
|---|---|
| Pokémon Gold and Silver release date | `retrieve_game → evaluate_retrieval` |
| First 3D platformer Mario game | `retrieve_game → evaluate_retrieval` |
| Mortal Kombat X on PlayStation 5 | `retrieve_game → evaluate_retrieval → game_web_search` |

---

## Key Files Explained

| File | Purpose |
|---|---|
| `Udaplay_01_starter_project.ipynb` | Builds the ChromaDB vector database and validates semantic search. |
| `Udaplay_02_starter_project.ipynb` | Implements the UdaPlay tools, state-machine agent, and example queries. |
| `games/` | Provided game dataset used as the internal knowledge base. |
| `lib/agents.py` | Agent class with tool loop, memory, and state-machine integration. |
| `lib/llm.py` | OpenAI chat wrapper configured with retry support. |
| `lib/state_machine.py` | Generic state-machine implementation used by the agent. |
| `lib/tooling.py` | Converts Python functions into LLM-callable tools. |
| `config.env.example` | Safe environment variable template. |
| `.gitignore` | Keeps secrets and generated local artifacts out of git. |

---

## Notes

- This is a Jupyter notebook project, not a frontend/web-app project.
- Notebook outputs are included because this is a notebook-based Udacity submission.
- Real API credentials are intentionally not included in this repository.
- The local ChromaDB database is generated by running Part 1 and is intentionally not committed.
- Generated submission archives, local logs, and study/explanation files are intentionally excluded from version control.
