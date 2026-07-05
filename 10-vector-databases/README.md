# 10 — How Vector Databases Actually Work: Indexing, ANN & HNSW ⭐

> **Video:** [Watch on YouTube](https://www.youtube.com/@FromPromptstoLoops)  
> **Code:** [`code/vector_db.ipynb`](code/vector_db.ipynb)  
> **What this is:** Under the hood of vector databases — why brute-force search doesn't scale, and how HNSW solves it.

---

## The Problem: Exact Search Doesn't Scale

Naive similarity search = compare your query vector against **every** stored vector.

```
1M documents × 1536 dimensions = 1.5 billion multiplications per query
```

At 10ms per query this is fine. At 10,000 queries/second it breaks down.

The solution: **Approximate Nearest Neighbor (ANN)** search — trade a small amount of accuracy for massive speed gains.

---

## How Similarity is Measured

**Cosine Similarity** — angle between two vectors (most common for text)
```
cos_sim(A, B) = (A · B) / (|A| × |B|)
Range: -1 (opposite) to 1 (identical)
```

**Dot Product** — used when vectors are normalized (same as cosine similarity then)

**Euclidean Distance (L2)** — straight-line distance between two points in vector space

For text embeddings, cosine similarity is almost always the right choice.

---

## ANN Indexing Strategies

### 1. Flat Index (Brute Force)
Compare query against every vector. Exact but O(n).  
Use only for < 100K vectors or when you need exact results.

### 2. IVF — Inverted File Index
**How it works:**
1. Cluster vectors into K groups (K-means clustering)
2. At query time, only search the nearest N clusters instead of all vectors

```
1M vectors, 1000 clusters → search top 10 clusters → check ~10K vectors
Speed: ~100x faster than flat. Accuracy: ~95%+ with good nprobe setting.
```

### 3. HNSW — Hierarchical Navigable Small World
The current gold standard for most production vector DBs.

**The intuition — think of it like a road network:**
```
Layer 2 (highways):  A few long-range connections, coarse navigation
Layer 1 (roads):     Medium-range connections
Layer 0 (streets):   All vectors, dense local connections
```

**Search algorithm:**
1. Enter at the top layer at a random starting node
2. Greedily navigate toward the query vector
3. Drop to the next layer and repeat with finer granularity
4. At layer 0, do a local exhaustive search among neighbors

**Why it's fast:**
- Top layers skip large distances quickly (like taking a highway)
- Bottom layers refine precisely (like navigating a neighborhood)
- Time complexity: O(log n) — search time grows *logarithmically* with dataset size

```
10K vectors:  1ms
1M vectors:   3ms      ← only 3x slower despite 100x more data
1B vectors:   6ms      ← only 6x slower despite 100,000x more data
```

---

## HNSW Parameters

| Parameter | What it controls | Trade-off |
|---|---|---|
| `M` | Number of connections per node | Higher = better recall, more memory |
| `ef_construction` | Size of the search list during index building | Higher = better index quality, slower build |
| `ef_search` | Size of the search list at query time | Higher = better recall, slower search |

Typical production values: `M=16, ef_construction=200, ef_search=50`

---

## Comparison: Which Index to Use?

| Index | Speed | Recall | Memory | Use When |
|---|---|---|---|---|
| Flat | Slow (exact) | 100% | Low | < 100K vectors, need exact |
| IVF | Fast | 95-99% | Medium | 100K–10M vectors |
| HNSW | Very fast | 95-99% | High | Production, latency-sensitive |
| IVF-PQ | Fastest | 90-95% | Very low | Billions of vectors, memory constrained |

---

## Hybrid Search

Pure vector search misses exact keyword matches. Hybrid search combines:
- **Dense retrieval** (vector similarity) — captures semantic meaning
- **Sparse retrieval** (BM25/TF-IDF) — captures exact keyword matches

```
Final score = α × dense_score + (1-α) × sparse_score
```

Weaviate, Elasticsearch, and Qdrant all support hybrid search out of the box.

---

## Code

[`code/vector_db.ipynb`](code/vector_db.ipynb) benchmarks:
- Flat vs HNSW search speed at different dataset sizes
- HNSW recall vs ef_search tradeoff curve
- Building a FAISS HNSW index from scratch

[![Open in Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com)

---

## Go Deeper
- [HNSW paper](https://arxiv.org/abs/1603.09320) — Malkov & Yashunin 2016
- [Pinecone: ANN explained](https://www.pinecone.io/learn/series/faiss/hnsw/)
- Next: [Chapter 11 — AI Agents Part 1](../11-ai-agents-part1/)
