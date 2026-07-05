# 07 — Fine-Tuning LLMs on Your Own Data: LoRA, QLoRA & PEFT ⭐

> **Video:** [Watch on YouTube](https://www.youtube.com/@FromPromptstoLoops)  
> **Code:** [`code/finetune_lora.ipynb`](code/finetune_lora.ipynb)  
> **What this is:** How to adapt an existing LLM to your domain without retraining from scratch — and how to do it on consumer hardware.

---

## What is Fine-Tuning?

Fine-tuning is taking an existing pre-trained LLM (Llama-3, Mistral, GPT) and continuing to train it on **your specific dataset** so it becomes an expert in your domain.

**What happens technically:**
- You start with an already-capable model
- You train it on your company's/domain's data
- It updates *some* internal weights to minimize error on your dataset
- It now understands your domain deeply

**After fine-tuning, your model can:**
- Respond in the right *tone* (polite, formal, branded)
- Answer in the right *format* (bullet points, structured JSON)
- Follow *guardrails* (block NSFW, refuse off-topic queries)
- Use the right *tools* (calculator, internal APIs)

---

## Fine-Tuning vs RAG — When to Use Which

| | Fine-Tuning | RAG |
|---|---|---|
| Use when | You need a specific *style*, *tone*, or *format* | You need access to frequently changing or private *facts* |
| Cost | High (training compute) | Lower (retrieval + inference) |
| Knowledge update | Requires retraining | Update the document store |
| Hallucination risk | Lower for style, can hallucinate facts | Lower for facts (grounded in retrieved docs) |
| Best for | Customer service persona, code style, format adherence | Company knowledge base, document Q&A |

> **Rule of thumb:** Use RAG first. Only fine-tune if RAG can't give you the tone, format, or behavior you need.

---

## The Problem: Full Fine-Tuning is Expensive

A 7B parameter model has ~28GB of weights. Training all of them requires:
- Multiple high-end GPUs
- Days of compute
- Careful management to avoid catastrophic forgetting

Most teams can't afford this. Enter **PEFT**.

---

## PEFT — Parameter-Efficient Fine-Tuning

Train only a **small fraction** of the model's parameters while freezing the rest.  
Result: 95%+ of the quality at 1–5% of the cost.

---

## LoRA — Low-Rank Adaptation

**The idea:** Instead of updating a large weight matrix W directly, learn two small matrices A and B such that the update is:

```
ΔW = A × B
```

Where A is (d × r) and B is (r × d), with r << d (rank is much smaller than dimension).

```
Original weight matrix W:  4096 × 4096 = 16.7M parameters
LoRA matrices (rank=16):   4096×16 + 16×4096 = 131K parameters  ← 127x fewer!
```

During inference, the adapted weights are: `W + ΔW = W + A×B`  
You can even merge LoRA weights back into W for zero-latency inference.

**What you tune:**
- `r` (rank) — higher = more capacity = more expensive. Typically 8–64.
- `alpha` — scaling factor for ΔW. Usually set to r or 2r.
- Which layers to apply LoRA to — usually Q and V projection matrices in attention.

---

## QLoRA — Quantized LoRA

**QLoRA = LoRA + 4-bit quantization of the base model**

Quantization compresses the base model's weights from 16-bit floats to 4-bit integers (~4x smaller).  
This lets you fine-tune a 7B model on a **single 16GB GPU** (e.g., free Google Colab T4).

```
7B model in fp16:  ~14GB VRAM
7B model in 4-bit: ~4GB VRAM    ← fits on a free Colab GPU
```

The LoRA adapters themselves are still trained in 16-bit for precision.

---

## The Fine-Tuning Data Format

Your dataset must be formatted as instruction-response pairs:

```json
{
  "instruction": "What is our return policy?",
  "input": "",
  "output": "Our return policy allows returns within 30 days of purchase with original packaging."
}
```

Or in chat format (recommended for modern models):
```json
{
  "messages": [
    {"role": "system", "content": "You are a helpful customer service agent for Acme Corp."},
    {"role": "user", "content": "What is your return policy?"},
    {"role": "assistant", "content": "Returns are accepted within 30 days with original packaging."}
  ]
}
```

**How much data do you need?**
- Style / tone adaptation: 100–500 examples can be enough
- Domain knowledge: 1,000–10,000 examples
- Complex behaviors: 10,000+ examples

---

## Code

[`code/finetune_lora.ipynb`](code/finetune_lora.ipynb) fine-tunes a small model using QLoRA with:
- `transformers` + `peft` + `trl` (HuggingFace stack)
- A sample instruction dataset
- Training, saving, and inference with the adapted model

[![Open in Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com)

---

## Go Deeper
- [LoRA paper](https://arxiv.org/abs/2106.09685) — original paper, accessible
- [QLoRA paper](https://arxiv.org/abs/2305.14314)
- [HuggingFace PEFT library](https://github.com/huggingface/peft)
- Next: [Chapter 08 — RAG Part 1](../08-rag-part1/)
