---
title: Scaling Laws
type: concept
tags: [scaling, compute, training, chinchilla, kaplan]
created: 2026-05-22
updated: 2026-05-22
sources: 2
---

## Overview

Scaling laws describe how model performance changes as a function of compute budget, model size (parameters), and data (tokens). Understanding them is essential for making rational decisions about how to spend compute during training. The field was revolutionized twice: first by Kaplan et al. (2020), then corrected by Hoffmann et al. (2022, "Chinchilla").

## Kaplan et al. (2020) — The Original Scaling Laws

*"Scaling Laws for Neural Language Models"* (OpenAI) found that LLM loss follows smooth power laws:

```
L(N) ~ N^{-α_N}    (loss as function of parameters, infinite data)
L(D) ~ D^{-α_D}    (loss as function of dataset tokens, large model)
L(C) ~ C^{-α_C}    (loss as function of compute budget)
```

Key findings from Kaplan et al.:
- Loss decreases predictably as you scale N, D, or C.
- **Model size matters more than data**: doubling N gives a bigger loss reduction than doubling D.
- Recommended keeping dataset small and model large given fixed compute.
- Optimal batch size scales as `B ~ B_crit * sqrt(C)`.

This led to models like GPT-3 (175B parameters, ~300B training tokens) — large models trained on relatively little data by today's standards.

## Hoffmann et al. (2022) — Chinchilla and Compute-Optimal Training

*"Training Compute-Optimal Large Language Models"* (DeepMind) trained 400+ models from 70M to 16B parameters on 5B to 500B tokens and found Kaplan was wrong:

**Key insight**: for a fixed compute budget `C`, both `N` (parameters) and `D` (tokens) should scale equally:

```
N_opt ∝ C^{0.5}
D_opt ∝ C^{0.5}
```

The Chinchilla-optimal ratio is approximately:

**~20 tokens per parameter**

Examples:
- 7B model → should train on ~140B tokens
- 70B model → should train on ~1.4T tokens
- 405B model → should train on ~8T tokens

The paper validated this by training Chinchilla (70B params, 1.4T tokens) at the same compute cost as Gopher (280B params, 300B tokens). Chinchilla outperformed Gopher, GPT-3, Jurassic-1, and Megatron-Turing NLG — all much larger models — on most benchmarks.

## Why Kaplan Was Wrong (and Why It Matters)

Kaplan's experiments didn't fully vary both N and D simultaneously, leading to a systematic bias toward large N. They also didn't train models long enough to see diminishing returns from data.

The practical consequence: before Chinchilla, companies spent massive compute on undertrained large models. The "right" answer was to train smaller models longer.

## Post-Chinchilla Reality

After Chinchilla, the field shifted:
- **LLaMA 1** (Meta, 2023): 7B–65B models trained on 1T–1.4T tokens (chinchilla-optimal).
- **LLaMA 2**: extended to 2T tokens ("beyond chinchilla-optimal" for inference efficiency).
- **Mistral 7B**: 7B model trained far past Chinchilla-optimal → better inference efficiency at deployment.

The insight: if you care about *deployment cost* (many inference calls), train smaller models on more data than Chinchilla suggests. The optimal is "chinchilla+" for products that serve many users.

## Inference-Optimal vs. Training-Optimal

A crucial practical distinction:

- **Training-optimal** (Chinchilla): minimize loss for a given training compute budget → 20 tokens/param.
- **Inference-optimal**: for a fixed capability level, which model is cheapest to *serve*? → smaller models trained longer (e.g., LLaMA 3 8B on 15T tokens).

At scale, inference dominates total compute cost. Training a smaller model longer is often the right business decision even if it's "compute-inefficient" by Chinchilla standards.

## The Neural Scaling Hypothesis

Broader implications: these smooth power laws suggest that intelligence itself may scale predictably with compute and data. This is the empirical basis for the "scaling hypothesis" associated with [[ilya-sutskever]] and others at OpenAI — that continuing to scale was the most reliable path to more capable AI.

## Key Numbers to Remember

| Model | Params | Training Tokens | Chinchilla-optimal? |
|---|---|---|---|
| GPT-3 | 175B | ~300B | Severely undertrained |
| Chinchilla | 70B | 1.4T | Exactly optimal |
| LLaMA 1 (7B) | 7B | 1T | ~optimal |
| LLaMA 3 (8B) | 8B | 15T | Heavily overtrained (by design) |
| Gemma 2 (2B) | 2B | 2T | Heavily overtrained |

## Related Pages

- [[pretraining]] — applying scaling law insights to actual training runs
- [[tokenization]] — token counts are the "D" in scaling law formulas
- [[lora]] — efficient fine-tuning; enabled by smaller Chinchilla-optimal base models
- ([Chinchilla Paper](../sources/chinchilla-paper-arxiv.md))
