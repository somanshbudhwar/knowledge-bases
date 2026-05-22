---
title: "LoRA: Low-Rank Adaptation of Large Language Models"
type: source
tags: [lora, fine-tuning, peft, efficiency, adaptation]
created: 2026-05-22
updated: 2026-05-22
sources: 0
---

## Bibliographic Info

- **URL**: https://arxiv.org/abs/2106.09685
- **Authors**: Edward Hu, Yelong Shen, Phillip Wallis, Zeyuan Allen-Zhu, Yuanzhi Li, Shean Wang, Lu Wang, Weizhu Chen (Microsoft)
- **Date**: 2021
- **Published at**: ICLR 2022
- **Type**: Research paper

## Abstract (paraphrased)

An important paradigm of natural language processing consists of large-scale pre-training on general domain data and adaptation to particular tasks or domains. As we pre-train larger models, full fine-tuning becomes infeasible. Using GPT-3 175B as an example, deploying independent instances of fine-tuned models is prohibitively expensive. The authors propose LoRA, which freezes pretrained model weights and injects trainable rank decomposition matrices into each Transformer layer, greatly reducing trainable parameters for downstream tasks.

## Key Contributions

1. LoRA reduces trainable parameters by 10,000x and GPU memory requirements by 3x vs. full fine-tuning GPT-3.
2. **Zero inference latency**: LoRA weights are merged with base model at inference time.
3. Empirical validation: ranks as low as r=1 or r=2 are often sufficient, supporting the low-rank hypothesis.
4. Outperforms adapters (inference latency), prefix tuning (instability), and fine-tuning a subset of layers.

## Core Formula

```
h = W_0 x + ΔW x = W_0 x + BAx
```

where `B ∈ R^{d×r}`, `A ∈ R^{r×k}`, `r << min(d,k)`. A initialized Gaussian, B initialized zero.

## Pages Derived From This Source

- [[concepts/lora]]
