# 01 — LLMs 101: Everything You Need Before Building with AI

> **Video:** [Watch on YouTube](https://www.youtube.com/watch?v=wS6L1d4iwLk)  
> **What this is:** The complete mental model for LLMs before you write a single line of code.

---

## How LLMs Work

Every LLM does exactly one thing: **predict the next token**.

```
Input:  "Why is the sky blue?"
Step 1: → "The"
Step 2: → "sky"
Step 3: → "appears"
Step 4: → "blue"
Step 5: → "because"
...
```

There is no thinking. No understanding. No internal model of the world.  
It's probability over tokens, learned from an enormous amount of text.

> If you trained a model only on articles that say "the sky is yellow," it would tell you the sky is yellow. Garbage in, garbage out — at a trillion-parameter scale.

---

## Why "Large"?

| Property | Example (GPT-4, 2023) |
|---|---|
| Training data | ~25 TB of curated web, books, code, papers |
| Parameters | ~1.76 trillion |

Parameters are the numbers the model learned during training. More parameters = more capacity to learn patterns.

---

## Model Types

### 1. Base Models
Raw pre-trained model. Just predicts the next token. No instruction following, no safety filters.  
Not useful for chat — useful as a starting point for fine-tuning.

### 2. Chat Models
A base model further trained on structured conversations using **RLHF** (Reinforcement Learning from Human Feedback).  
Understands: `system prompt → user message → assistant reply`  
Examples: ChatGPT, Claude, Gemini

Best for: conversational tasks, creative writing, emails, general Q&A.

### 3. Reasoning Models
Chat models trained to "think step by step" before answering.  
They spend extra tokens on an internal scratchpad before producing a final response.  
Examples: OpenAI o3, o4-mini, DeepSeek-R1, Claude with extended thinking

Best for: math, logic, code, multi-step problems.

---

## Open vs Closed Models

| | Closed | Open Weights |
|---|---|---|
| Examples | GPT-4o, Claude 3.5, Gemini 1.5 | Llama 3, Mistral, Qwen, Phi |
| Access | API only | Download & run yourself |
| Privacy | Data leaves your machine | Fully local |
| Cost at scale | Per-token pricing | Infrastructure cost |
| Customization | Prompt only | Fine-tune, modify |

---

## Three Ways to Use Models

```
1. Cloud APIs        → OpenAI, Anthropic, Google — pay per token
2. Hugging Face      → Model hub, serverless inference, local download
3. Ollama / llama.cpp → Run locally on your own hardware
```

---

## User-Configurable Parameters

| Parameter | What it does | Range |
|---|---|---|
| **Temperature** | Controls randomness — higher = more creative, lower = more deterministic | 0 to 2 |
| **Max Tokens** | Upper limit on response length | 1 to model limit |
| **Top P** | Nucleus sampling — limits token candidates to top-P probability mass | 0 to 1 |
| **Repetition Penalty** | Penalizes repeating the same words | 1.0 to 2.0+ |
| **Stop Sequences** | Strings that tell the model to stop generating | Custom list |

---

## Running Open-Weight Models

**Limited hardware (laptop, no GPU):**
- [Ollama](https://ollama.ai) — one command, runs models up to ~7B on CPU+RAM

**Free cloud GPU:**
- [Google Colab](https://colab.research.google.com) — free T4 GPU (16GB VRAM), runs up to ~14B models

**Production GPU:**
- [vLLM](https://github.com/vllm-project/vllm) / [llama.cpp](https://github.com/ggerganov/llama.cpp) — efficient batching for serving

---

## Hardware Primer

- **CPU** — general purpose, few cores, sequential. Slow for LLMs but works for small models.
- **GPU** — thousands of small cores, massively parallel. The standard for LLM inference and training.
- **TPU** — Google's custom chip, optimized specifically for the matrix multiplications LLMs use.

---

## Go Deeper
- [Ollama quickstart](https://ollama.ai)
- [OpenAI API docs](https://platform.openai.com/docs)
- [Hugging Face model hub](https://huggingface.co/models)
- Next: [Chapter 02 — How machines actually learn](../02-ml-neural-networks-part1/)
