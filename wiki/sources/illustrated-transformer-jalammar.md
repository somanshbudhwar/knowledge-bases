---
title: "The Illustrated Transformer — Jay Alammar"
type: source
tags: [transformers, attention, visualization, tutorial]
created: 2026-05-22
updated: 2026-05-22
sources: 0
---

## Bibliographic Info

- **URL**: https://jalammar.github.io/illustrated-transformer/
- **Author**: Jay Alammar
- **Date**: 2018 (regularly referenced/updated)
- **Type**: Blog post / visual explainer

## Summary

The canonical visual tutorial on the Transformer architecture. Uses diagrams and step-by-step examples to explain self-attention, multi-head attention, positional encoding, the encoder-decoder structure, and the training process. Widely used as a first introduction to transformers for learners at all levels.

## Key Points

- Explains the QKV mechanism intuitively: Q is "what am I looking for", K is "what do I have to offer", V is "what I actually provide".
- Self-attention score = `Q · K^T / sqrt(d_k)` → softmax → weighted sum of V.
- Multi-head attention: 8 independent attention subspaces, concatenated and projected.
- Positional encoding: sinusoidal (original), allows model to use relative position information.
- Encoder-decoder: encoder runs in parallel over all source tokens; decoder generates one token at a time, attending to encoder output.
- Training: cross-entropy loss, label smoothing (in original paper), Adam optimizer with warmup.

## Best Used For

- First introduction to self-attention mechanics.
- Understanding the encoder-decoder structure visually.
- Explaining to others what transformers do.

## Pages Derived From This Source

- [[concepts/transformers]]
