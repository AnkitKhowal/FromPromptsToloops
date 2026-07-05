# 02 — ML & Neural Networks Part 1: How Machines Actually Learn

> **Video:** [Watch on YouTube](https://www.youtube.com/@FromPromptstoLoops)  
> **What this is:** The math behind how any ML model — from a simple regressor to a billion-parameter LLM — actually learns from data.

---

## What Does an ML Algorithm Actually Do?

Every ML algorithm does one thing:

> It looks at past data, learns patterns, so it can predict the answer for new, unseen data.

Whether it's a 5-line linear regression or a trillion-parameter transformer — **the goal is always prediction based on patterns in data.**

---

## Classification vs Regression

| Type | Output | Example |
|---|---|---|
| **Classification** | A category | Is this email SPAM or NOT SPAM? |
| **Regression** | A number | What is the delivery ETA? |

---

## A Concrete Example: Spam Detection

The model sees emails humans have labelled as SPAM / NOT SPAM.

**Patterns it learns:**
- Words like "lottery", "congratulations", "click your prize" → SPAM
- Words like "meeting", "lunch", "project" → NOT SPAM

Given a new email, it applies the same pattern: probability of SPAM based on word frequencies.

---

## A Concrete Example: Delivery ETA (Regression)

Data the model sees:
```
Restaurant distance: 3km | Time of day: 1PM | Rating: Yes  → Delivery: 35 min
Restaurant distance: 4km | Time of day: 7PM | Rating: No   → Delivery: 50 min
...
```

The model learns weights for each feature (distance, time, etc.) that best predict the delivery time.

---

## How a Model Finds the Pattern — Step by Step

### The Setup
You have data:
```
x: 1  →  y: 3
x: 2  →  y: 5
x: 3  →  y: 7
x: 4  →  y: 9
```
You suspect the pattern is: `y = a*x + b`

### Step 1: Start with a Random Guess
`a = 0.5, b = 0.2`  → so `y' = 0.5x + 0.2`

### Step 2: Make Predictions and Measure How Wrong You Are

| x | y (real) | y' (predicted) | Error (y' - y) |
|---|---|---|---|
| 1 | 3 | 0.7 | -2.3 |
| 2 | 5 | 1.2 | -3.8 |
| 3 | 7 | 1.7 | -5.3 |
| 4 | 9 | 2.2 | -6.8 |

### Step 3: Compute the Loss (Mean Squared Error)
```
Loss = average of (error²)
     = (5.29 + 14.44 + 28.09 + 46.24) / 4
     = 23.5
```
We want the loss as close to **0** as possible.

### Step 4: Gradient Descent — Adjust `a` and `b`
Rather than brute-force trying every combination of `a` and `b`, we use the **derivative** (rate of change) to know which direction to nudge each parameter to *reduce* the loss.

```
d(loss)/d(a) = 2 * Error * x     → average across all points = -26.5
d(loss)/d(b) = 2 * Error         → average across all points = -9.1
```

**The update formula:**
```
new_value = old_value - learning_rate * derivative
```

A negative derivative means increasing the parameter reduces the loss → so we increase `a` and `b`.

### Step 5: Repeat
Go back to step 2 with updated `a` and `b`. Repeat until loss is near zero.

This loop — **predict → measure loss → compute gradient → update parameters** — is the core of how every ML model learns, including LLMs.

---

## Key Terms

| Term | Plain English |
|---|---|
| **Parameters / Weights** | The numbers the model learns (`a` and `b` in the example above) |
| **Loss function** | How wrong the model is right now |
| **Gradient** | Which direction to nudge each parameter to reduce the loss |
| **Gradient Descent** | The update rule: move parameters in the direction that reduces loss |
| **Learning Rate** | How big a step to take on each update — too big overshoots, too small is slow |
| **Epoch** | One full pass through the training data |

---

## Where Neural Networks Come In

Simple ML works great for **simple patterns** — where inputs map directly to outputs in a straightforward way (e.g., more distance = more delivery time).

Problems it can't solve alone:
- What does a word *mean*? (context-dependent)
- Is this image a cat or dog?
- What is the sentiment of this sentence?

These require looking at **relationships between inputs** — which is what neural networks are designed for.

→ Covered in [Chapter 03](../03-ml-neural-networks-part2/)

---

## Go Deeper
- [3Blue1Brown: Neural Networks series](https://www.youtube.com/playlist?list=PLZHQObOWTQDNU6R1_67000Dx_ZCJB-3pi) — the best visual introduction to this material
- Next: [Chapter 03 — Inside a Neural Network (with code)](../03-ml-neural-networks-part2/)
