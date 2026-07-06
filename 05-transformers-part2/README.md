# 05 — Transformers Part 2: How Attention REALLY Works (Q, K, V & Softmax, By Hand) ⭐

> **Video:** [Watch on YouTube](https://www.youtube.com/watch?v=sd0H-u44z-A) 
> **What this is:** A ground-up walkthrough of the attention mechanism — the math, the intuition, and the code.

---

## The Attention Formula

```
Attention(Q, K, V) = softmax(QKᵀ / √dₖ) · V
```

This looks intimidating. Let's break it down from first principles.

---

## The Intuition: A Fuzzy Lookup Table

Think of attention like a search engine query:

- **Q (Query)** — "What am I looking for?" — the current token asking a question
- **K (Key)** — "What do I have to offer?" — every other token advertising itself
- **V (Value)** — "Here's my actual content" — what gets retrieved if the key matches

For each token, attention:
1. Computes similarity between its Query and every other token's Key
2. Converts similarities to weights (softmax → they sum to 1.0)
3. Returns a weighted sum of all tokens' Values

The result: each token's new vector = a blend of all other tokens' content, weighted by relevance.

---

## Why Q, K, V Are Separate

You might ask: why not just use the token's own vector directly?

Because the token needs different "faces" for different roles:
- When *asking* (Q): "I'm 'bank', what context should I pull in?"
- When *being asked about* (K): "I'm 'river', tokens about geography should attend to me"
- When *giving content* (V): "I'm 'river', here's what I actually know about rivers"

Q, K, V are all learned linear projections of the same input vector — the model learns which projection works best for each role.

---

## The Math, Step by Step

### Input
Assume 3 tokens, each with embedding dimension = 4:
```
Token vectors (X):
  "The"   → [1.0,  0.5, 0.2, 0.8]
  "river" → [0.3,  0.9, 0.1, 0.4]
  "bank"  → [0.7,  0.2, 0.9, 0.1]
```

### Step 1: Project to Q, K, V
Multiply each token's vector by three learned weight matrices (Wq, Wk, Wv) to get Q, K, V matrices.

### Step 2: Compute Similarity Scores (QKᵀ)
Dot product of each Query with each Key — how similar is every pair of tokens?

```
Score("bank", "river") = Q_bank · K_river   ← high if they're related
Score("bank", "The")   = Q_bank · K_The     ← probably lower
```

### Step 3: Scale by √dₖ
Divide all scores by √(dimension of keys).  
Without this, scores grow with dimension size → softmax gets too "spiky" → gradients vanish.

### Step 4: Softmax
Convert scores to probabilities that sum to 1.0:
```
[3.2, 1.1, 0.4]  →  softmax  →  [0.75, 0.18, 0.07]
```
"bank" pays 75% attention to "river", 18% to "The", 7% to itself.

### Step 5: Weighted Sum of Values
Multiply attention weights by V matrix → sum up:
```
new_vector("bank") = 0.75 * V_river + 0.18 * V_The + 0.07 * V_bank
```
"bank"'s vector is now dominated by "river"'s content — it's been updated to mean *riverbank*.

---

## Multi-Head Attention

Run the above process **H times in parallel** with different learned projections.

Why? One attention head might learn grammar relationships, another might learn semantic relationships, another might learn long-range dependencies.

```
head_1 = Attention(Q·Wq1, K·Wk1, V·Wv1)   ← grammar
head_2 = Attention(Q·Wq2, K·Wk2, V·Wv2)   ← semantics
...
head_H = Attention(Q·WqH, K·WkH, V·WvH)   ← co-reference

output = Concat(head_1, ..., head_H) · Wo
```

GPT-3 uses 96 attention heads per layer across 96 layers. That's 9,216 parallel attention computations per forward pass.

---

## Code

[`code/attention.ipynb`](code/attention.ipynb) implements:
- Single-head attention from scratch in NumPy
- Multi-head attention
- A visualization of attention weights

[![Open in Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com)

---

## Go Deeper
- ["Attention Is All You Need"](https://arxiv.org/abs/1706.03762) — the original paper, very readable
- [Andrej Karpathy: nanoGPT](https://github.com/karpathy/nanoGPT) — minimal GPT implementation
- Next: [Chapter 06 — Inside an LLM Training Run](../06-llm-training-internals/)
