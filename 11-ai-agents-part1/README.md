# 11 — AI Agents Part 1: LLMs, Tool-Use & the Agentic Loop (ReAct) ⭐

> **Video:** [Watch on YouTube](https://www.youtube.com/watch?v=B9CLL3cyEZw)  
> **Code:** [`code/agent_react.ipynb`](https://colab.research.google.com/drive/1NsD6qfwJZs7qIZYK5zrsJYndM84ovD-t?authuser=1)  
> **What this is:** The jump from "a model that talks" to "a system that acts."

---

## The Core Insight

> RAG made the model *better informed* — it could answer questions on topics it didn't learn during training.
>
> The model was still doing exactly one thing: **reading text.**
>
> It could tell you *how* to book a flight.  
> It could **not** book the flight.

Agents give LLMs the ability to **take actions in the world** — call APIs, run code, search the web, read files, send messages.

---

## What is an AI Agent?

An AI agent is an LLM that can:
1. **Plan** — break a goal into sub-steps
2. **Use tools** — call functions, APIs, and services
3. **Loop** — observe the result of each action and decide what to do next
4. **Stop** — know when the task is complete

```
Goal: "Book me a flight to New Delhi tomorrow."

Pass 1: LLM calls search_flights("New Delhi", "tomorrow")
        Tool returns: [3 flights found, cheapest Rs 3,200]
        → tool was used, loop again

Pass 2: LLM calls book_flight(cheapest)
        Tool returns: {confirmed: "ABC123"}
        → tool was used, loop again

Pass 3: LLM outputs: "Booked! Your confirmation is ABC123."
        → no tool requested → STOP. This is the answer.
```

---

## The Agentic Loop

```
User goal
    ↓
┌─────────────────────────────┐
│  LLM thinks:                │
│  "What should I do next?"   │
│         ↓                   │
│  [call a tool OR respond]   │
│         ↓                   │
│  If tool: run it → observe  │
│  If respond: we're done     │
└──────────────↑──────────────┘
       (loop back with result)
```

---

## Tool Use — How It Works

**Step 1: Define the function on your server**
```python
def send_slack_message(channel: str, text: str):
    # hit Slack's API and post the message
    return {"status": "sent", "channel": channel}
```

**Step 2: Describe it as a tool schema the LLM reads**
```python
tool = {
    "name": "send_slack_message",
    "description": "Post a message to a Slack channel. Use when the user asks to notify someone on Slack.",
    "parameters": {
        "channel": "string — e.g. '#general'",
        "text":    "string — the message to post"
    }
}
```

**Step 3: Pass the schema when calling the LLM**
```python
response = llm.chat(
    messages=[{"role": "user", "content": "Tell #general we shipped!"}],
    tools=[tool]   # ← schema rides along in the request
)
```

The LLM decides whether to call the tool — and if so, returns a structured tool call (not a text response) that your server executes.

---

## The ReAct Pattern

**ReAct = Reasoning + Acting**

The LLM interleaves:
- **Thought** — explicit reasoning about what to do next
- **Action** — a tool call
- **Observation** — the tool's result

```
Thought: I need to find the weather in Delhi before booking.
Action: get_weather(city="New Delhi", date="tomorrow")
Observation: {"temp": 38°C, "condition": "sunny"}

Thought: Weather is fine. Now search for flights.
Action: search_flights(destination="New Delhi", date="tomorrow")
Observation: [{"flight": "AI-202", "price": 3200}, ...]

Thought: Found a cheap flight. Book it.
Action: book_flight(flight_id="AI-202")
Observation: {"confirmation": "ABC123"}

Thought: Done. I have a confirmation number.
Response: "Booked flight AI-202. Confirmation: ABC123."
```

The reasoning trace makes agent behavior **inspectable and debuggable**.

---

## When to Use Agents (and When Not To)

### Use agents when:
- The task requires multiple steps that depend on each other's results
- You need to interact with external systems (APIs, databases, browsers)
- The path to completing the task is not known in advance

### Don't use agents when:
- A single LLM call can answer the question — agents add latency and cost
- The task is deterministic — use a normal function/workflow
- You need guaranteed output format — agents can be unpredictable

> **Start simple.** A chain of two LLM calls beats a poorly designed agent every time.

---

## Code

[`code/agent_react.ipynb`](code/agent_react.ipynb) builds a ReAct agent with:
- 3 custom tools (search, calculator, date lookup)
- A manual ReAct loop in Python (no frameworks — so you see exactly what's happening)
- Then the same agent using LangChain

[![Open in Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com)

---

## Go Deeper
- [ReAct paper](https://arxiv.org/abs/2210.03629) — Yao et al. 2022
- [OpenAI function calling docs](https://platform.openai.com/docs/guides/function-calling)
- Next: [Chapter 12 — MCP, Multi-Agents & Memory](../12-agentic-systems-part2/)
