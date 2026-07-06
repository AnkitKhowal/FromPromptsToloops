# Code — Fine-Tuning LLMs

This folder contains all code, notebooks, and scripts for this chapter.

## How to run

All notebooks are designed to run on [Google Colab](https://colab.research.google.com) with no local setup.
Look for the **Open in Colab** badge at the top of each notebook.

To run locally:
```bash
pip install -r requirements.txt  # if present
jupyter notebook
```

## Files
<!-- List notebooks and scripts added to this folder -->
| File | Description |
|------|-------------|
| *https://colab.research.google.com/drive/1xrGdM6qrKmKKY1PywZb1HOMelz8RrRFi?usp=sharing* |This notebook demonstrates how to fine-tune a small Language Model (LLM) using your company's knowledge base to create an AI chatbot that provides accurate, company-specific answers. The scenario involves 'TechNova Solutions' wanting a chatbot to answer employee questions from their internal HR handbook, preventing hallucinations common with generic LLMs. The solution involves fine-tuning a small Llama 3.2 1B model with about 40-50 company-specific Q&A pairs. The notebook proves that while a base model hallucinates or fails to answer company-specific questions, a fine-tuned model can accurately respond to both direct and rephrased queries, demonstrating significant generalization. This process is made efficient and cost-effective using QLoRA quantization, allowing it to run on a free Colab T4 GPU.

 |

---
> Running into issues? Open an issue and tag it with the chapter number.
