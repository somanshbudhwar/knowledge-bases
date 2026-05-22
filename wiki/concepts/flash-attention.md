---
title: Flash Attention
type: concept
tags: [efficiency, attention, gpu, systems]
created: 2026-05-22
updated: 2026-05-22
sources: 1
---

## Overview

Flash Attention is an algorithm by [[../entities/tri-dao|Tri Dao]] that computes exact attention without materializing the full n x n attention matrix. It achieves significant speedups and memory savings by exploiting GPU memory hierarchy.

## Key Insight

The bottleneck on modern GPUs isn't FLOPs — it's memory bandwidth between HBM (high bandwidth memory, slow) and SRAM (on-chip, fast). Standard attention writes large intermediate matrices to HBM repeatedly.

## How It Works

- **Tiling**: breaks the Q, K, V matrices into blocks that fit in SRAM
- **Online softmax**: computes softmax incrementally across tiles without needing the full row of attention scores at once
- Never materializes the full n x n attention matrix in HBM
- Produces the **exact same result** as standard attention — no approximation

## Versions

- **FlashAttention** (2022): initial algorithm
- **FlashAttention-2**: improved parallelism and work partitioning
- **FlashAttention-3**: further optimizations for newer GPU architectures

## Impact

Flash Attention is now the default attention implementation in most LLM training and inference frameworks. It makes long-context training practical by reducing attention's memory footprint from O(n^2) to O(n).

## Related

- [[transformers]] — the architecture Flash Attention optimizes
- [[../entities/tri-dao|Tri Dao]] — creator
- [[../sources/llm-foundations-learning-roadmap|LLM Foundations Roadmap]] (Stage 2)
