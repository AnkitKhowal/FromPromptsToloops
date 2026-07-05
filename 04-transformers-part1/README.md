# 04 — Transformers Part 1: How LLMs Actually Read (Tokens, Embeddings & Attention)

> **Video:** [Watch on YouTube](https://www.youtube.com/@FromPromptstoLoops)  
> **What this is:** The architecture that powers every modern LLM — how text becomes vectors, and how the model reads the whole sequence at once.

---

## Before Transformers (pre-2017)

Language models processed text **sequentially** — one word at a time — using RNNs and LSTMs.

Problems:
- **Slow** — can't parallelize; must wait for word N before processing word N+1
- **Forgetting** — by the time the model reached the end of a long sentence, it had "forgotten" the beginning

Example where this fails:
```
"I deposited money at the bank."   ← "bank" = financial institution
"I sat by the river bank."         ← "bank" = riverbank
```
A sequential model struggles to use the word "river" to reinterpret "bank" because they're far apart.

---

## The Transformer (2017)

**Paper:** "Attention Is All You Need" — Google, June 2017

Key innovation: process **all tokens simultaneously** and let each token directly attend to every other token in the sequence.

```
Before: word1 → word2 → word3 → word4 (sequential)
After:  [word1, word2, word3, word4] processed all at once
```

This solved both problems: parallelism (fast) and full-context awareness (no forgetting).

---

## Three Things to Keep Separate

| Term | What it is |
|---|---|
| **Transformer** | An *architecture* — a design pattern for neural networks. Published by Google in 2017. Not a product. |
| **LLM** | A *category* of AI model trained on massive text using the Transformer architecture |
| **GPT** | A *specific family* of LLMs made by OpenAI |

---

## Step 1: Tokenization

Text is split into **tokens** (not words — subword units).

```
"unhappiness" → ["un", "happiness"]
"ChatGPT"     → ["Chat", "G", "PT"]
```

Each token is assigned an integer ID from the model's vocabulary (~50,000–100,000 tokens).

---

## Step 2: Embedding

Each token ID is looked up in an **embedding table** to get a dense vector.

```
Token "bank" → [0.2, -0.4, 0.8, 0.1, ...]   (768 or 4096 numbers)
```

At this point the vectors are *context-free* — "bank" has one vector regardless of whether you mean a financial institution or a riverbank.

---

## Step 3: Attention — Adding Context

Attention is what makes the Transformer powerful. For each token, it asks:
> "Which other tokens in this sequence should I pay attention to?"

The answer updates each token's vector to incorporate context from the surrounding tokens.

After attention:
- "bank" in "river bank" gets a vector pulled towards water/nature meaning
- "bank" in "deposited money" gets a vector pulled towards finance meaning

**Same word, different vector** — depending on context.

---

## Why Stack Layers?

Each attention layer builds on the previous:

- **Layer 1:** Captures shallow word relationships ("the" attaches to nearest noun)
- **Layer 10:** Captures local grammatical structure
- **Layer 50:** Abstracts structural patterns — can spot abstract programming concepts

Depth = more abstract, richer understanding.

---

## The Transformer Pipeline (Simplified)

```
Raw text
   ↓ Tokenizer
Token IDs: [42, 1703, 89, ...]
   ↓ Embedding table
Vectors: [[0.2, -0.4, ...], [0.9, 0.1, ...], ...]
   ↓ Attention layer 1
Updated vectors (context-aware)
   ↓ Attention layer 2
...
   ↓ Attention layer N (48 in GPT-3, 96 in GPT-4)
Final vectors
   ↓ Linear projection → Softmax
Probability over vocabulary → pick next token
```

---

## Go Deeper
- ["Attention Is All You Need" paper](https://arxiv.org/abs/1706.03762)
- [The Illustrated Transformer — Jay Alammar](https://jalammar.github.io/illustrated-transformer/)
- Next: [Chapter 05 — How Attention REALLY Works (Q, K, V by hand)](../05-transformers-part2/)
