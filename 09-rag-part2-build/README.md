# 09 — RAG Part 2: Build a RAG Chatbot from Scratch (LangChain, FAISS & OpenAI) ⭐

> **Video:** [Watch on YouTube](https://www.youtube.com/@FromPromptstoLoops)  
> **Code:** [`code/rag_chatbot.ipynb`](code/rag_chatbot.ipynb)  
> **What this is:** A complete, runnable RAG chatbot built end-to-end using LangChain, FAISS, and the OpenAI API.

---

## What You'll Build

A chatbot that can answer questions about any PDF document you give it:

```
You:  "What is the refund policy?"
Bot:  "According to the document, returns are accepted within 30 days
       with original packaging. Loyalty members get 45 days." [Source: page 12]
```

---

## Architecture

```
PDF
 └─ LangChain PDF loader
     └─ Text splitter (chunking)
         └─ OpenAI Embeddings
             └─ FAISS vector store
                 └─ Retriever
                     └─ LangChain RetrievalQA chain
                         └─ OpenAI GPT-4o
                             └─ Answer + source citations
```

---

## The Stack

| Component | Tool | Why |
|---|---|---|
| Document loading | `LangChain PyPDFLoader` | Handles PDF → text |
| Chunking | `LangChain RecursiveCharacterTextSplitter` | Smart sentence-aware splitting |
| Embeddings | `OpenAI text-embedding-3-small` | Fast, accurate |
| Vector store | `FAISS` | Local, no setup required |
| LLM | `OpenAI gpt-4o-mini` | Fast and cheap for Q&A |
| Chain | `LangChain RetrievalQA` | Wires retriever + LLM together |

---

## Key Code Snippets

### Load and chunk a PDF
```python
from langchain.document_loaders import PyPDFLoader
from langchain.text_splitter import RecursiveCharacterTextSplitter

loader = PyPDFLoader("your_document.pdf")
pages = loader.load()

splitter = RecursiveCharacterTextSplitter(chunk_size=1000, chunk_overlap=100)
chunks = splitter.split_documents(pages)
```

### Create vector store
```python
from langchain.embeddings import OpenAIEmbeddings
from langchain.vectorstores import FAISS

embeddings = OpenAIEmbeddings(model="text-embedding-3-small")
vectorstore = FAISS.from_documents(chunks, embeddings)
vectorstore.save_local("faiss_index")
```

### Build the RAG chain
```python
from langchain.chat_models import ChatOpenAI
from langchain.chains import RetrievalQA

llm = ChatOpenAI(model="gpt-4o-mini", temperature=0)
retriever = vectorstore.as_retriever(search_kwargs={"k": 5})

qa_chain = RetrievalQA.from_chain_type(
    llm=llm,
    retriever=retriever,
    return_source_documents=True
)
```

### Query
```python
result = qa_chain({"query": "What is the refund policy?"})
print(result["result"])
print(result["source_documents"])
```

---

## What Can Go Wrong (and How to Fix It)

| Problem | Likely Cause | Fix |
|---|---|---|
| "I don't know" on a topic that's in the doc | Chunking split the relevant passage | Increase chunk overlap, try semantic chunking |
| Irrelevant answer | Wrong chunks retrieved | Add a reranker (chapter 10 covers this via vector DB internals) |
| Hallucinated facts | LLM ignoring retrieved context | Tighten the system prompt: "Only answer from the provided context" |
| Slow retrieval | FAISS doing brute-force search | Switch to HNSW index (chapter 10) |

---

## Cost Estimate

For a 100-page PDF (~75,000 tokens):
- Embedding: ~$0.001 (one-time, cheap)
- Per query (retrieval + 5 chunks + answer): ~$0.002–0.005

Running 1,000 queries/day ≈ **$2–5/day**

---

## Code

Full end-to-end notebook: [`code/rag_chatbot.ipynb`](code/rag_chatbot.ipynb)

[![Open in Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com)

**Requirements:**
```
pip install langchain langchain-openai faiss-cpu pypdf
```
Set `OPENAI_API_KEY` in your environment or Colab secrets.

---

## Go Deeper
- Next: [Chapter 10 — How Vector Databases Actually Work](../10-vector-databases/)
- [LangChain RAG tutorial](https://python.langchain.com/docs/use_cases/question_answering/)
