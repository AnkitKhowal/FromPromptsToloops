# 13 — AI Evals Explained: How to Test LLMs, RAG & Agents ⭐

> **Video:** [Watch on YouTube](https://www.youtube.com/@FromPromptstoLoops)  
> **Code:** [`code/evals.ipynb`](code/evals.ipynb)  
> **What this is:** How to know whether your AI system is actually working — metrics, LLM-as-a-judge, and making evals a regression gate in CI.

---

## Why Evals Matter

> Without evals, you're flying blind.

You change a system prompt. Does the system get better or worse?  
You upgrade to a new model version. Did quality improve?  
You add more chunks to RAG. Did answers get more accurate?

Without a measurement framework, you **cannot answer any of these questions reliably.**

---

## Evals for RAG Systems

The RAG pipeline has two places that can fail:

```
query → [ RETRIEVAL ] → chunks → [ LLM + prompt ] → answer
              ↑                          ↑
         test this                  test this
```

Test them *separately* — so when something breaks, you know whose fault it is.

### (1) Retrieval Quality — Did we fetch the right chunks?

| Metric | What it measures |
|---|---|
| **Precision@K** | Of the K chunks retrieved, what fraction were actually relevant? |
| **Recall@K** | Of all relevant chunks that exist, what fraction did we retrieve? |
| **MRR** | Mean Reciprocal Rank — was the most relevant chunk near the top? |

**Knobs to turn:** chunking strategy, chunk size, embedding model, number of retrieved chunks (K)

### (2) Answer Quality — Given good chunks, did we write a good answer?

| Metric | What it measures |
|---|---|
| **Groundedness** | Does the answer only use facts from the retrieved chunks? (no hallucination) |
| **Answer Relevancy** | Does the answer actually address the question asked? |
| **Correctness** | Is the answer factually correct? (requires a ground-truth answer) |

**Knob to turn:** the system prompt

---

## LLM-as-a-Judge

Writing ground-truth answers for every question doesn't scale.  
**LLM-as-a-judge** uses a capable LLM to evaluate responses automatically.

```python
judge_prompt = """
You are evaluating an AI assistant's response.

Question: {question}
Retrieved Context: {context}
AI Response: {response}

Score the response 1-5 on:
- Groundedness: Does it only use facts from the context?
- Relevancy: Does it answer the question asked?
- Correctness: Is it factually accurate?

Return JSON: {{"groundedness": X, "relevancy": X, "correctness": X, "reasoning": "..."}}
"""
```

**Tips for reliable LLM judges:**
- Use a strong model (GPT-4o, Claude 3.5 Sonnet) as the judge
- Ask for reasoning — it reduces arbitrary scoring
- Use reference-based scoring where possible (compare to a known-good answer)
- Validate your judge: does it agree with human raters on a sample?

---

## Evals for Agents

Agents are harder to evaluate because there's no single "right answer" — there are many paths to a correct outcome.

**What to measure:**

| Metric | What it is |
|---|---|
| **Task completion rate** | Did the agent successfully complete the goal? |
| **Tool call accuracy** | Did it call the right tools with the right arguments? |
| **Turn efficiency** | Did it complete in a reasonable number of steps? |
| **Trajectory correctness** | Did it take sensible intermediate steps? |

---

## Building an Eval Dataset

### From user query patterns
Once real users arrive:
1. **Frequency-based** — what do users ask most often? Cover these.
2. **Feedback-based** — which queries get thumbs-down or complaints? Fix these.

Your eval set should be dominated by:
- High-frequency queries (common path)
- High-pain queries (the ones generating complaints)

### Dataset size recommendations
- **Prototype/dev:** 50–100 examples is enough to catch regressions
- **Staging:** 500–1,000 examples for more reliable signal
- **Production gate:** 1,000+ with balanced coverage of query types

---

## The Tooling Landscape

```
Job                        Tool
─────────────────────────────────────────────────────
RAG metrics (out of box)   RAGAS (precision@k, recall@k, groundedness)
LLM-as-a-judge             DeepEval, TruLens, Promptfoo
Tracing + eval platform    LangSmith, Braintrust, Arize Phoenix
```

### How they fit together
```
1. Wrap your RAG app with LangSmith   → every query/response/trace is logged
2. Point RAGAS at those traces        → auto-computes recall@K, groundedness
3. Add DeepEval judges in CI          → on every pull request, re-run 50-query set;
                                         block the merge if avg score drops
```

**That third step is the payoff: evals become a regression gate.**  
Just like unit tests block a bad code merge, an eval drop blocks a bad prompt/model merge — automatically.

---

## The Swiss Cheese Model of Defense

No single eval layer is perfect. Each has holes. But stack them, and a failure has to slip through *every* layer's holes to reach the user.

```
① Manual Evals        → high quality, catches subtle/domain errors, low volume
② Automated Evals     → high volume, catches regressions at scale
③ Production Monitor  → catches what only shows up with real users + drift
```

---

## Code

[`code/evals.ipynb`](code/evals.ipynb):
- Build a small golden eval dataset
- Run RAGAS metrics on a RAG pipeline
- Write a custom LLM-as-a-judge evaluator
- Wire it as a CI check

[![Open in Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com)

---

## Go Deeper
- [RAGAS library](https://github.com/explodinggradients/ragas)
- [DeepEval docs](https://docs.confident-ai.com)
- [LangSmith](https://smith.langchain.com)
- [Braintrust](https://braintrustdata.com)
