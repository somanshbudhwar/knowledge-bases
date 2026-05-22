---
title: "The Transformer Family Version 2.0 — Lilian Weng"
type: source
tags: [transformers, attention, positional-encoding, rope, efficiency]
created: 2026-05-22
updated: 2026-05-22
sources: 0
---

## Bibliographic Info

- **URL**: https://lilianweng.github.io/posts/2023-01-27-the-transformer-family-v2/
- **Author**: Lilian Weng (OpenAI)
- **Date**: January 2023
- **Type**: Blog post (technical survey)

## Summary

Comprehensive survey of transformer architecture improvements from 2020 onward. Covers efficiency improvements (sparse attention, low-rank approximation, content-based hashing), positional encoding evolution (learned → relative → RoPE), long-context methods (Transformer-XL, external memory), and adaptive computation (early exit, adaptive attention span).

## Key Points

- **RoPE**: applies rotation matrices to Q/K vectors by position; naturally encodes relative position; dominant in modern LLMs
- **ALiBi**: fixed linear bias by token distance; no parameters; good out-of-distribution context length extrapolation
- **Sparse attention**: Longformer (local + global), Big Bird (local + random + global tokens) reduce O(n²) attention
- **Linformer**: projects K/V to lower dimensions, achieving O(n) attention — at cost of some quality
- **Reformer**: LSH-based attention for O(n log n) complexity
- **Transformer-XL**: segment-level recurrence with relative positional encodings; foundation for many long-context models
- **Decision Transformer**: reformulates RL as sequence modeling, conditioning on desired return-to-go

## Pages Derived From This Source

- [[concepts/transformers]]
- [[concepts/positional-encodings]]
