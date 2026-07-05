# 03 — ML & Neural Networks Part 2: Inside a Neural Network ⭐

> **Video:** [Watch on YouTube](https://www.youtube.com/watch?v=ImAYVvkWIWA)  
> **Code:** [`code/neural_network.ipynb`](https://colab.research.google.com/drive/1priJD88sFGuAM58-ycs9Q6mZoPvisM6X?authuser=1#scrollTo=7vmXIV8YnPQt)  
> **What this is:** How neural networks work internally, why they can solve problems simple ML can't, and a live code walkthrough.

---

## Why Neural Networks?

Simple ML maps inputs directly to outputs in one step.  
Neural networks stack multiple layers of transformations — each layer learning a progressively more abstract representation of the data.

**The problem simple ML can't solve:**

> "I deposited money at the *bank*."  
> "I sat by the river *bank*."

Simple ML sees the word "bank" as one feature. It cannot know which meaning applies.  
A neural network learns *context* — the words around "bank" change its representation.

---

## Translation as an Analogy

Translating "I have a dog" from English to Hindi is not a one-step lookup.

1. First, understand the grammar — subject, verb, object
2. Then, understand the meaning of each word
3. Then, express THEN in the target language's grammar

This requires *layered understanding* — exactly what neural networks are built for.

---

## Neural Network Structure

```
Input Layer → Hidden Layer(s) → Output Layer
```

Each layer is a set of **neurons**. Each neuron:
1. Takes weighted inputs from the previous layer
2. Sums them up
3. Passes the result through an **activation function** (which adds non-linearity)
4. Sends the output to the next layer

The weights are the parameters the network learns via gradient descent (same as chapter 02, just many more of them).

---

## Embeddings — Turning Words Into Numbers

Before text can enter a neural network, it must become numbers.

An **embedding** maps a token (word/subword) to a vector — a list of numbers — in a high-dimensional space.

```
"king"   → [0.2, -0.4, 0.8, ...]
"queen"  → [0.3, -0.3, 0.7, ...]   ← similar vector, similar meaning
"dog"    → [-0.9, 0.1, -0.2, ...]  ← very different
```

Key property: **semantic meaning is encoded in distance**.  
Words with similar meanings cluster together in vector space.

### What does a vector look like in practice?

```python
# A word vector in a small embedding space
prices = [1, 3, 4]   # a vector with 3 dimensions

# In real models: 768, 1536, or 4096 dimensions
```

### The accumulation insight
In a loop like:
```python
total = 0
for price in prices:
    total = price   # BUG: this overwrites, not accumulates
```
vs.
```python
total = 0
for price in prices:
    total = total + price   # correct: accumulates all values
```
The difference between overwriting and accumulating is the core of how attention works — each token's final vector is an *accumulation* of influences from all other tokens in the sequence.

---

## How a Neural Network Learns (LLM Edition)

Same loop as chapter 02, but now with billions of parameters:

```
1. Feed in training sentence: "The cat sat on the ___"
2. Model predicts: "table"     (wrong — answer is "mat")
3. Calculate loss
4. Backpropagate the error through every layer
5. Update weights slightly to make "mat" more likely
6. Repeat on the next sentence
```

After seeing enough examples, the model's weights encode statistical patterns about language that generalize to new sentences.

---

## Key Terms

| Term | Plain English |
|---|---|
| **Neuron** | One computational unit — weighted sum + activation |
| **Activation function** | Adds non-linearity so the network can learn complex patterns |
| **Backpropagation** | The algorithm for computing gradients through all layers |
| **Embedding** | A vector representation of a token |
| **Hidden layer** | Any layer between input and output |
| **Depth** | Number of layers — deeper = more abstract features |

---

## Code

The notebook [`code/neural_network.ipynb`](code/neural_network.ipynb) builds a small neural network from scratch and shows:
- Forward pass (prediction)
- Loss computation
- Backward pass (gradient calculation)
- Parameter update

[![Open in Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com)

---

## Go Deeper
- [Andrej Karpathy: micrograd](https://github.com/karpathy/micrograd) — build backprop from scratch in 100 lines
- Next: [Chapter 04 — Transformers Part 1](../04-transformers-part1/)
