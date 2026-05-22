---
title: LoRA (Low-Rank Adaptation)
type: concept
tags: [fine-tuning, peft, lora, adaptation, efficiency]
created: 2026-05-22
updated: 2026-05-22
sources: 1
---

## Overview

LoRA (Low-Rank Adaptation) is the dominant technique for parameter-efficient fine-tuning (PEFT) of large language models. Rather than updating all model weights during fine-tuning, LoRA freezes the pretrained weights and injects small trainable rank-decomposition matrices alongside them. The result: competitive fine-tuning performance with 10,000x fewer trainable parameters.

Paper: "LoRA: Low-Rank Adaptation of Large Language Models" (Hu et al., 2021, arXiv:2106.09685).

## How LoRA Works

For a pretrained weight matrix `W ∈ R^{d×k}`, full fine-tuning learns `ΔW` such that the adapted output is:

```
h = (W + ΔW)x
```

LoRA constrains `ΔW` to be a product of two low-rank matrices:

```
ΔW = B × A
```

where `B ∈ R^{d×r}` and `A ∈ R^{r×k}`, with rank `r << min(d, k)`.

During training:
- `W` is **frozen** (no gradients, no optimizer state).
- `A` is initialized with random Gaussian, `B` is initialized to zero (so ΔW = 0 at init).
- Only `A` and `B` are trained.

During inference, the adapted weight is simply merged: `W' = W + BA`. This means **zero inference latency overhead** compared to full fine-tuning.

The scaling factor `α/r` (where `α` is a hyperparameter) controls the magnitude of the LoRA update, typically set to a constant like `α = 16` or `α = r`.

## Why It Works: The Low-Rank Hypothesis

The key empirical observation: **weight updates during fine-tuning have low intrinsic rank**. The pretrained model already encodes rich representations; task-specific adaptation only needs to adjust a small subspace. Hu et al. showed that even `r = 1` or `r = 2` can be sufficient for many tasks — the effective rank of the adaptation is much lower than the full matrix dimensions.

## Rank Selection

Typical rank values in practice:
- `r = 4` or `r = 8`: lightweight, good for domain adaptation with plenty of data.
- `r = 16` or `r = 32`: more expressive, for complex tasks or limited data.
- `r = 64`+: approaches full fine-tuning, rarely necessary.

A rule of thumb: start with `r = 8` and `α = 16`. Use validation loss to tune if needed.

## Which Layers to Apply LoRA To

Original paper applied LoRA to attention weight matrices (`W_Q`, `W_K`, `W_V`, `W_O`). In practice:
- Applying to all linear layers (including FFN) often helps.
- `W_V` and `W_O` are typically most important.
- Modern recipes (LLaMA fine-tuning) often apply to all projection matrices.

## QLoRA: 4-bit Quantization + LoRA

QLoRA (Dettmers et al., 2023) makes fine-tuning accessible on consumer hardware by combining:
1. **4-bit quantization** of the frozen base model weights (using NF4 data type).
2. **Double quantization** to reduce quantization constant memory.
3. **Paged optimizers** for gradient checkpointing to avoid OOM spikes.
4. LoRA adapters trained in full precision atop the quantized base.

Result: fine-tune a 65B model on a single 48GB GPU. Without QLoRA, this requires ~780GB of GPU memory.

## PEFT Library (HuggingFace)

HuggingFace's `peft` library provides a standard API for LoRA and related methods:

```python
from peft import LoraConfig, get_peft_model

config = LoraConfig(r=8, lora_alpha=16, target_modules=["q_proj", "v_proj"])
model = get_peft_model(base_model, config)
```

Other supported methods: Prefix Tuning, P-Tuning, IA³, AdaLoRA (adaptive rank).

## LoRA Variants

- **AdaLoRA**: dynamically allocates rank budget across layers based on importance scores.
- **LoRA+**: uses different learning rates for A and B matrices.
- **DoRA** (Weight-Decomposed LoRA): decomposes weight into magnitude and direction, adapts direction with LoRA. Often more stable.
- **rsLoRA**: scales LoRA by `1/sqrt(r)` instead of `1/r` for stability at high rank.

## When to Use LoRA vs. Full Fine-Tuning

| Scenario | Recommendation |
|---|---|
| Limited GPU memory | LoRA (or QLoRA) |
| Multiple tasks, one base model | LoRA (swap adapters, not full models) |
| Very small dataset (<1K examples) | LoRA (less overfitting risk) |
| Large dataset (>100K examples) + sufficient compute | Full fine-tuning may give +1-2% |
| Production serving with many specialized models | LoRA (merge adapters on demand) |

LoRA's ability to merge adapters at inference time (zero latency overhead) makes it dominant in production systems. The 10-100x parameter reduction also matters for storage and serving cost.

## Practical Takeaways

- Always freeze the base model before applying LoRA — modifying base weights defeats the purpose.
- LoRA adapters are small enough to share openly (a few hundred MB vs. tens of GB for full models).
- The HuggingFace Hub has thousands of LoRA adapters for LLaMA, Mistral, and other base models.
- `r` and `α` are the critical hyperparameters; the ratio `α/r` determines the effective learning rate scaling.

## Related Pages

- [[transformers]] — the architecture LoRA is applied to
- [[scaling-laws]] — why smaller Chinchilla-optimal models made LoRA more practical
- [[pretraining]] — what LoRA is adapting from
- ([LoRA Paper](../sources/lora-paper-arxiv.md))
