# 06 — Inside an LLM Training Run: Ablations, Evals, Data Prep & Post-Training

> **Video:** [Watch on YouTube](https://www.youtube.com/@FromPromptstoLoops)  
> **What this is:** What actually happens when a lab trains a frontier model — the data pipeline, training decisions, and how base models become chat models.

---

## The Two Phases of Building an LLM

```
Phase 1: Pre-training      → teaches the model language and world knowledge
Phase 2: Post-training     → teaches the model to be helpful, safe, and follow instructions
```

---

## Phase 1: Pre-Training

### What happens
The model is trained to predict the next token across an enormous text corpus. Gradient descent (chapter 02) updates billions of parameters to minimize prediction error.

### Data Preparation Pipeline

Raw internet data is not ready to train on. It must be cleaned:

**1. Filtering**
- Remove duplicates — the same web page scraped 100 times would over-index that content
- Remove toxic and harmful content
- Remove PII (personally identifiable information)

**2. Sequence Packing & Document Masking**

Models process data in fixed-size chunks called *sequences* (typically 2,048 / 4,096 / 8,192 tokens).

Documents come in all sizes:
```
Tweet:      50 tokens   →  needs 4,046 tokens of padding  →  waste
News article: 50,000 tokens → spans many sequences
```

**Sequence packing:** pack multiple short documents into one sequence.  
**Document masking:** ensure the model doesn't "see across" document boundaries (token from doc A shouldn't attend to token from doc B).

### Ablations
Before committing to a full training run, labs run **ablations** — small-scale experiments that test one variable at a time:
- What happens if we change the data mixture (more code vs. more web text)?
- What happens if we change the learning rate schedule?
- What happens at 1B parameters vs. 7B parameters?

Ablation results guide decisions for the full, expensive run.

### Training Evals
During training, the model is periodically evaluated on benchmark tasks to track capability growth:
- MMLU (general knowledge)
- HumanEval (coding)
- GSM8K (math word problems)

These tell the lab whether the training run is on track or has diverged.

---

## Phase 2: Post-Training

After pre-training, you have a **base model** — it predicts the next token, but it doesn't follow instructions or hold conversations.

Post-training is what turns a base model into ChatGPT.

### Step 1: Supervised Fine-Tuning (SFT)
Train on a dataset of high-quality `(instruction, ideal response)` pairs.  
Teaches the model the *format*: system prompt → user → assistant.

### Step 2: RLHF (Reinforcement Learning from Human Feedback)
Human raters compare model outputs and rank them.  
A **reward model** is trained on these preferences.  
The LLM is then fine-tuned to maximize the reward model's score.

This is what makes the model:
- Prefer helpful responses over unhelpful ones
- Refuse dangerous requests
- Match a particular tone and style

---

## Key Concepts

| Term | Plain English |
|---|---|
| **Tokenization** | Splitting text into subword units |
| **Sequence packing** | Fitting multiple documents into one fixed-length chunk |
| **Ablation** | A controlled experiment: change one thing, measure the effect |
| **Base model** | Raw output of pre-training — predicts next token, no instruction following |
| **SFT** | Fine-tuning on instruction-response pairs to teach the assistant format |
| **RLHF** | Using human preference rankings to further align the model's outputs |
| **Reward model** | A model trained to predict which response humans prefer |

---

## Go Deeper
- [Llama 3 paper](https://arxiv.org/abs/2407.21783) — Meta's detailed pre-training writeup
- [InstructGPT paper](https://arxiv.org/abs/2203.02155) — the original RLHF paper from OpenAI
- Next: [Chapter 07 — Fine-Tuning on Your Own Data](../07-finetuning/)
