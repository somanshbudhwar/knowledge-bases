---
title: "Training Compute-Optimal Large Language Models (Chinchilla)"
type: source
tags: [scaling-laws, chinchilla, compute, training, data]
created: 2026-05-22
updated: 2026-05-22
sources: 0
---

## Bibliographic Info

- **URL**: https://arxiv.org/abs/2203.15556
- **Authors**: Jordan Hoffmann, Sebastian Borgeaud, Arthur Mensch, Elena Buchatskaya, et al. (Google DeepMind, 22 authors)
- **Date**: March 2022
- **Type**: Research paper

## Abstract (paraphrased)

We investigate the optimal model size and number of tokens for training a transformer language model under a given compute budget. By training over 400 language models ranging from 70M to 16B parameters on 5B to 500B tokens, we find that current large language models are significantly undertrained. The key result: model size and training tokens should scale in equal proportions. A compute-matched model trained on 4× more data than Gopher significantly outperforms all larger models.

## Key Contributions

1. **Chinchilla rule**: ~20 tokens per parameter for compute-optimal training.
2. Refutes Kaplan et al. (2020) finding that model size matters more than data.
3. Chinchilla (70B, 1.4T tokens) outperforms Gopher (280B, 300B tokens) at same compute.
4. Chinchilla beats GPT-3 (175B), Jurassic-1 (178B), MT-NLG (530B) on most benchmarks.
5. Practical benefit: smaller, well-trained models are cheaper to fine-tune and serve.

## Key Numbers

- Chinchilla-optimal: 1 compute unit → 0.5 to parameters, 0.5 to tokens.
- 70B model: train on 1.4T tokens.
- 7B model: train on 140B tokens (Chinchilla) or 1-15T tokens (inference-optimal).
- Chinchilla 67.5% on MMLU vs. Gopher 60%.

## Pages Derived From This Source

- [[concepts/scaling-laws]]
