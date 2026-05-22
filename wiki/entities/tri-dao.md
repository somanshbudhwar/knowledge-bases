---
title: Tri Dao
type: entity
tags: [person, researcher, systems]
created: 2026-05-22
updated: 2026-05-22
sources: 1
---

## Overview

Researcher known for systems-level innovations in deep learning, particularly GPU-efficient algorithms for transformer training and inference.

## Mentioned In

- [[../sources/llm-foundations-learning-roadmap|LLM Foundations Roadmap]]: credited as the author of the [[../concepts/flash-attention|Flash Attention]] papers (FlashAttention, FlashAttention-2, FlashAttention-3).

## Key Contributions

- **Flash Attention**: IO-aware exact attention algorithm that exploits GPU memory hierarchy (tiling + online softmax) to avoid materializing the full n x n attention matrix. Now the default attention implementation in most LLM frameworks.
