---
title: Transformers
type: concept
tags: [architecture, attention, deep-learning]
created: 2026-05-22
updated: 2026-05-22
sources: 3
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

## Training Objectives

The architecture supports multiple training paradigms, each producing different model behaviors:

### Causal Language Modeling (CLM) — Decoder-only
- **Objective**: predict each token from all *preceding* tokens only (left-to-right, causal mask).
- **Used by**: GPT family, LLaMA, Mistral, all modern chat models.
- **Why**: enables autoregressive generation; model "writes" one token at a time at inference.
- **Data efficiency**: every position contributes a training signal simultaneously.

### Masked Language Modeling (MLM) — Encoder-only
- **Objective**: randomly mask 15% of tokens and predict the masked tokens using *bidirectional* context.
- **Used by**: BERT, RoBERTa, DeBERTa, ELECTRA.
- **Why**: bidirectional context produces richer representations for classification and embedding tasks.
- **Limitation**: cannot generate text autoregressively; produces a fixed-length representation.

### Encoder-Decoder (Seq2Seq)
- **Objective**: encoder processes full source sequence (bidirectional attention); decoder generates output token by token attending to encoder states + previous outputs.
- **Used by**: T5, BART, mT5, for translation, summarization, question answering.
- **Why**: natural fit for tasks with explicit input→output structure.

## Key Milestones

The Transformer architecture evolved rapidly after its 2017 introduction:

| Year | Model | Key Contribution |
|---|---|---|
| 2017 | **Transformer** (Vaswani et al.) | Invented attention-only architecture; encoder-decoder for MT |
| 2018 | **GPT-1** (OpenAI) | Decoder-only pretraining + fine-tuning; 117M params |
| 2018 | **BERT** (Google) | Bidirectional masked LM; became dominant for NLU tasks; 340M params |
| 2019 | **GPT-2** (OpenAI) | Scaled to 1.5B; demonstrated surprisingly good zero-shot generation |
| 2019 | **T5** (Google) | Encoder-decoder; "text-to-text" unified format for all NLP tasks |
| 2020 | **GPT-3** (OpenAI) | 175B params; few-shot learning emerged; prompted the LLM era |
| 2022 | **ChatGPT / InstructGPT** | RLHF applied to GPT-3; unlocked assistant behavior |
| 2023 | **LLaMA** (Meta) | Open-weights Chinchilla-optimal family; democratized research |
| 2023 | **GPT-4** (OpenAI) | Frontier multimodal model; specific architecture unknown |
| 2024 | **LLaMA 3** (Meta) | 405B open model; GQA, 128K context, 15T training tokens |

## Related

- [[flash-attention]] — efficient attention computation
- [[mixture-of-experts]] — scaling the feedforward layers
- [[positional-encodings]] — how position information is injected
- [[kv-cache]] — critical inference optimization for autoregressive generation
- [[pretraining]] — how these models are trained from scratch
- [[ashish-vaswani]] — lead author of "Attention Is All You Need"
- [[../sources/llm-foundations-learning-roadmap|LLM Foundations Roadmap]] (Stage 1)
- ([Illustrated Transformer — Alammar](../sources/illustrated-transformer-jalammar.md))
- ([Transformer Family v2 — Lilian Weng](../sources/transformer-family-v2-lilianweng.md))
