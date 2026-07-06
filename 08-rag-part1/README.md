# 08 — RAG Part 1: How AI Looks Up YOUR Data (Chunking, Embeddings & Vector DBs) ⭐

> **Video:** [Watch on YouTube](https://www.youtube.com/watch?v=HEkVYhaSXjA&t=13s)    
> **What this is:** The foundations of Retrieval-Augmented Generation — why it exists and how each component works.

---

## The Problem RAG Solves

LLMs have three core limitations:

1. **No private data** — the model was trained on public internet; it doesn't know your company's documents
2. **Knowledge cutoff** — training data has a date; the model doesn't know what happened after
3. **Retraining is expensive** — you can't update an LLM every time a document changes

**The RAG solution:** Instead of baking knowledge into the model, *retrieve* relevant information at query time and inject it into the prompt.

```
User: "What is our refund policy?"
Without RAG: AI answers from general knowledge → wrong / hallucinated
With RAG:    AI searches your documents → finds the refund policy page → answers from that
```

---

## The RAG Pipeline

```
OFFLINE (done once / periodically):
  Documents → Chunking → Embedding Model → Vector Database

ONLINE (every query):
  User Query → Embedding → Vector DB search → Top-K chunks → LLM + prompt → Answer
```

---

## Step 1: Chunking

You can't embed an entire 100-page PDF as one vector. You need to split it into smaller **chunks** that can be independently retrieved.

### Chunking Strategies

**1. Fixed-Size Chunking**
Split every N tokens regardless of content.
```
Chunk size: 512 tokens, overlap: 50 tokens
Pro: Simple, predictable
Con: May split a sentence or idea mid-way
```

**2. Sentence / Paragraph Chunking**
Split at natural boundaries (sentences, paragraphs, headings).
```
Pro: Preserves semantic units
Con: Variable chunk sizes
```

**3. Semantic Chunking**
Compute embeddings for consecutive sentences; split when the semantic similarity drops significantly.
```
Pro: Splits where the topic actually changes
Con: More expensive (requires embedding during chunking)
```

**4. Hierarchical / Parent-Child Chunking (with overlap)**
Store large parent chunks + small child chunks.  
Retrieve by child chunk (precision), return parent chunk to LLM (context).

```
Chunk 1: tokens 1–200
Chunk 2: tokens 150–350  ← overlaps chunk 1
Chunk 3: tokens 300–500  ← overlaps chunk 2
```

### The Chunking Tradeoff

| | Too Small | Too Large |
|---|---|---|
| Problem | Loses context, noisy embeddings | Noisy, irrelevant content sent to LLM |
| Effect | Poor retrieval | Poor generation |
| Rule | Small enough to be precise | Large enough to be self-contained |

---

## Step 2: Embeddings

Each chunk is converted to a vector using an **embedding model**.  
Chunks about similar topics get similar vectors — this is how retrieval works.

```python
from sentence_transformers import SentenceTransformer
model = SentenceTransformer("all-MiniLM-L6-v2")
chunk_vector = model.encode("Our return policy allows 30-day returns.")
# → [0.23, -0.41, 0.08, ...]  (384 numbers)
```

Popular embedding models:
| Model | Dimensions | Good for |
|---|---|---|
| `text-embedding-3-small` (OpenAI) | 1536 | General purpose, fast |
| `text-embedding-3-large` (OpenAI) | 3072 | Higher accuracy |
| `all-MiniLM-L6-v2` (open) | 384 | Fast, free, local |
| `bge-m3` (open) | 1024 | Multilingual |

---

## Step 3: Vector Database

A database optimized for storing and searching vectors by **similarity** (not exact match).

At query time:
1. Embed the user's question → query vector
2. Search the DB for the top-K most similar chunk vectors
3. Return the matching chunks

Popular vector DBs:
| DB | Notes |
|---|---|
| **FAISS** | Facebook, open source, runs locally — great for learning |
| **Chroma** | Easy to set up, local-first, good for prototypes |
| **Pinecone** | Managed cloud service, production-grade |
| **Weaviate** | Open source, supports hybrid search |
| **pgvector** | Postgres extension — if you already use Postgres |

---

## Step 4: Reranker (Optional but Powerful)

Vector search returns the top-K *approximately* most relevant chunks.  
A **reranker** is a second model that takes the query + each retrieved chunk and scores them more precisely.

```
Vector search:  fast, approximate  → top 20 chunks
Reranker:       slower, accurate   → top 5 chunks
LLM:            uses these 5 chunks to generate answer
```

Adding a reranker typically improves answer quality noticeably with minimal added latency.

---

## Code

[`code/rag_foundations.ipynb`](code/rag_foundations.ipynb):
- Chunk a PDF document
- Generate embeddings with `sentence-transformers`
- Store and query with FAISS

[![Open in Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com)

---

## Go Deeper
- Next: [Chapter 09 — Build a RAG Chatbot End-to-End](../09-rag-part2-build/)
- [LangChain docs](https://python.langchain.com)
- [FAISS wiki](https://github.com/facebookresearch/faiss/wiki)
