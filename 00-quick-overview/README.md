# 00 — 10 AI Concepts Every Beginner Must Know

> **Video:** [Watch on YouTube](https://www.youtube.com/watch?v=L6u2GHXfhlc&t=6s) · **Duration:** ~30 min  
> **What this is:** A fast-paced map of the entire AI landscape — so everything in later chapters has context.

---

## The 10 Concepts

### 1. Large Language Models (LLMs)
Models like GPT, Claude, and Gemini trained on massive text datasets. They do exactly one thing: **predict the next token**. All the apparent "intelligence" is a result of doing this extremely well at massive scale.

### 2. Tokens
LLMs don't read words — they read *tokens* (roughly 3–4 characters each). "ChatGPT" might be 2 tokens. Your prompt is tokenized before the model sees it.

### 3. Model Types
- **Base model** — raw next-token predictor, no instruction following
- **Chat model** — fine-tuned with RLHF to follow instructions and hold conversations
- **Reasoning model** — trained to "think step by step" before answering (o3, DeepSeek-R1)

### 4. Open vs Closed Models
- **Closed** (GPT-4, Claude, Gemini) — API only, weights not released
- **Open weights** (Llama, Mistral, Qwen) — download and run yourself

### 5. Prompting
How you talk to a model matters enormously. System prompt, user message, and assistant reply form the structure every chat model understands.

### 6. Embeddings
A way to turn text into a list of numbers (a vector) that captures *meaning*. "King" and "Queen" have similar vectors. The foundation of search and RAG.

### 7. RAG (Retrieval-Augmented Generation)
Instead of retraining a model on your data, you *retrieve* relevant chunks at query time and inject them into the prompt. Faster and cheaper than fine-tuning for most use cases.

### 8. Fine-Tuning
Actually updating the model's weights on your own dataset. Makes the model an expert in your domain — but more expensive than RAG.

### 9. AI Agents
LLMs that can use *tools* (search, APIs, code execution) and loop until a task is done. The jump from "a model that talks" to "a system that acts."

### 10. Evals
How you measure whether your AI system is actually working. Without evals, you're flying blind. Covers everything from simple accuracy checks to LLM-as-a-judge pipelines.

---

## The Learning Path

```
LLMs (how they work)
    └── ML Foundations (how they learn)
        └── Transformers (the architecture)
            └── Training & Fine-tuning (making them yours)
                └── RAG (connecting them to your data)
                    └── Agents (making them act)
                        └── Evals (making sure they work)
```

---

## Go Deeper
- Chapter `01` — LLMs in detail
- Chapter `08` + `09` — RAG end-to-end
- Chapter `11` + `12` — Agents end-to-end
- Chapter `13` — Evals
