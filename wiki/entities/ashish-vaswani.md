---
title: Ashish Vaswani
type: entity
tags: [researcher, transformer, attention, google-brain]
created: 2026-05-22
updated: 2026-05-22
sources: 0
---

## Overview

Ashish Vaswani is the lead author of "Attention Is All You Need" (Vaswani et al., 2017) — the paper that introduced the Transformer architecture and triggered a decade-long revolution in AI. He was a senior research scientist at Google Brain when the paper was published.

## Key Contribution: "Attention Is All You Need" (2017)

The paper proposed replacing recurrent and convolutional architectures entirely with a pure attention-based architecture for sequence-to-sequence tasks (translation). The core insight: self-attention allows any two tokens to interact directly regardless of distance, enabling full parallelization during training (unlike RNNs, which process sequentially).

The paper introduced:
- **Scaled dot-product attention**: `Attention(Q,K,V) = softmax(QK^T / sqrt(d_k)) V`
- **Multi-head attention**: running attention in parallel across multiple projection subspaces
- **Positional encoding** (sinusoidal) to compensate for attention's permutation invariance
- The encoder-decoder Transformer architecture for neural machine translation

The paper had 8 authors (Vaswani, Shazeer, Parmar, Uszkoreit, Jones, Gomez, Kaiser, Polosukhin), making it a notable team effort rather than a solo discovery.

## Career

- Research Scientist, Google Brain — developed the Transformer architecture
- Co-founded **Adept AI** (2022) — working on general-purpose AI agents that can use computers to complete tasks, in the paradigm of "action transformers"
- Later moved on from Adept to other ventures

## The Paper's Impact

"Attention Is All You Need" is one of the most cited papers in computer science history (100,000+ citations). The Transformer immediately displaced LSTMs for NLP and within a few years became the foundation of:
- GPT-1, GPT-2, GPT-3, GPT-4 (decoder-only Transformers)
- BERT, RoBERTa (encoder-only Transformers)
- T5, BART (encoder-decoder Transformers)
- Vision Transformers (ViT) — Transformers for images
- Essentially all modern large-scale AI models

## Related Pages

- [[transformers]] — the architecture Vaswani co-invented
- [[positional-encodings]] — the sinusoidal approach originates from this paper
- [[andrej-karpathy]] — researcher who has extensively taught the Transformer's internals
