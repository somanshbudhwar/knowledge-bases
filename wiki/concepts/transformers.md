---
title: Transformers
type: concept
tags: [architecture, attention, deep-learning]
created: 2026-05-22
updated: 2026-05-22
sources: 1
---

## Overview

The transformer is the foundational architecture behind modern LLMs. Introduced in "Attention Is All You Need" (Vaswani et al., 2017), it replaced recurrence with self-attention, enabling massive parallelization during training.

## Core Components

- **Self-attention**: each token computes Query, Key, and Value vectors. Attention scores are computed as softmax(QK^T / sqrt(d_k)) * V. The sqrt(d_k) scaling prevents softmax saturation in high dimensions.
- **Multi-head attention**: multiple parallel attention computations with smaller dimensions; each head can specialize (syntactic, semantic, positional patterns).
- **Positional encodings**: attention is permutation-invariant, so position must be injected. Evolution: sinusoidal → learned absolute → relative → RoPE (dominant in modern LLMs). RoPE generalizes to longer contexts and enables extensions like YaRN.
- **Feedforward network**: applied per-position after attention. Modern models use SwiGLU activation instead of ReLU.
- **Residual connections + normalization**: LayerNorm (original) or RMSNorm (modern).

## Variants

- **Decoder-only** (GPT family): causal masking, next-token prediction. Dominant for generative LLMs.
- **Encoder-only** (BERT): bidirectional attention, masked language modeling. Used for embeddings and classification.
- **Encoder-decoder** (T5): full architecture for seq2seq tasks.

## Related

- [[flash-attention]] — efficient attention computation
- [[mixture-of-experts]] — scaling the feedforward layers
- [[../sources/llm-foundations-learning-roadmap|LLM Foundations Roadmap]] (Stage 1)
