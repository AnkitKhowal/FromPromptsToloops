# 12 — Agentic Systems Part 2: MCP, Multi-Agents & Memory ⭐

> **Video:** [Watch on YouTube](https://www.youtube.com/@FromPromptstoLoops)  
> **Code:** [`code/multi_agent.ipynb`](code/multi_agent.ipynb)  
> **What this is:** How to build systems of agents that coordinate, share memory, and use standardized tool protocols.

---

## From Single Agent to Multi-Agent

A single agent with many tools hits limits:
- Too many tools → LLM gets confused choosing between them
- Long tasks → context window fills up
- Parallelism → one agent is sequential

**Multi-agent systems** solve this by specializing:

```
LEAD AGENT (orchestrator)
    ├── FRAUD AGENT       → checks transaction history + risk score
    ├── ACTION AGENT      → freezes card, initiates refund
    └── COMMS AGENT       → drafts and sends customer notification
```

### Real Example: "$2,000 charge I didn't make!"

```
User: "There's a $2,000 charge I didn't make!"

① LEAD AGENT plans: "(a) check fraud, (b) act on it, (c) tell customer"

② Delegates to FRAUD AGENT:
   → checks history + risk score
   → reports: "High risk. Charge in another country. Likely fraud."

③ Delegates to ACTION AGENT:
   → freezes card + starts refund
   → reports: "Card frozen. Refund of $2,000 initiated, ETA 3 days."

④ Delegates to COMMS AGENT:
   → drafts message
   → reports: "Customer notified via email + SMS."

⑤ LEAD AGENT synthesizes and replies:
   "We confirmed fraud, froze your card, and started a $2,000 refund (arriving ~3 days).
    Confirmation sent to your email."
```

---

## MCP — Model Context Protocol

MCP is an open standard (introduced by Anthropic) that defines how AI agents connect to tools and data sources.

**The problem it solves:**  
Every AI app was reinventing tool integration — custom code to connect to Slack, GitHub, databases, file systems, etc.

**MCP standardizes this:**
```
Agent ←→ MCP Client ←→ MCP Server ←→ Tool / Data Source
```

An MCP server exposes a set of tools with a standard interface. Any MCP-compatible agent can use any MCP server without custom glue code.

**Examples of MCP servers:**
- `filesystem` — read/write local files
- `github` — search repos, create PRs, read issues
- `slack` — send messages, read channels
- `postgres` — query databases
- `brave-search` — web search

**Why this matters:**  
You write your agent once, and it can connect to any MCP server in the ecosystem.

---

## Agent Memory

An agent without memory forgets everything between conversations — like Groundhog Day on every session.

### Types of Memory

| Type | Lifespan | Stored As | Example |
|---|---|---|---|
| **In-context (short-term)** | Current session only | The messages array | Conversation history |
| **External (long-term)** | Persistent | Database / vector DB | User preferences, past actions |
| **Semantic** | Persistent | Vector DB | Facts about users/world |
| **Episodic** | Persistent | Database | "Last time user ordered pizza at 8pm" |

### When Memory is Written

**Pattern A — End of session (batch)**
```
Conversation ends
    ↓
Background LLM call: "Extract facts from this conversation"
    → "User's name is Ankit. Prefers 8 PM bookings. Vegetarian."
    ↓
Save to long-term store (DB / vector DB)
```

**Pattern B — During conversation (inline / hot-path)**  
After each turn, the system decides: "Is there anything worth saving?" and writes immediately.

**Pattern C — Explicit / tool-triggered**  
The agent has a `save_memory(fact)` tool and the LLM decides when to call it,  
or the user explicitly says "remember that I'm allergic to nuts."

### Memory in Action

Without memory:
```
User:  "Book #123 for 8 PM."
Agent: "What name should I use for the booking?"
```

With long-term memory:
```
User:  "Book #123 for 8 PM."
Agent: "Booked under the name Ankit."  ← name pulled from memory
```

---

## Orchestration Patterns

### Sequential (pipeline)
```
Agent A → Agent B → Agent C
```
Each agent's output feeds into the next. Simple, predictable.

### Parallel (fan-out)
```
Lead Agent ──┬── Sub-agent A
             ├── Sub-agent B
             └── Sub-agent C
             ↓
         Synthesizer Agent
```
Best when sub-tasks are independent. Much faster than sequential.

### Hierarchical
Agents spawn sub-agents as needed. Complex tasks, dynamic workflows.  
Use with caution — harder to debug, costs can spiral.

---

## Code

[`code/multi_agent.ipynb`](code/multi_agent.ipynb):
- A two-agent system: orchestrator + specialist
- Long-term memory with ChromaDB
- MCP tool usage example

[![Open in Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com)

---

## Go Deeper
- [MCP specification](https://modelcontextprotocol.io)
- [LangGraph docs](https://langchain-ai.github.io/langgraph/) — multi-agent orchestration framework
- Next: [Chapter 13 — AI Evals Explained](../13-evals-and-production/)
