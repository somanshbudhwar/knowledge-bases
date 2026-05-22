---
title: Mixture of Experts (MoE)
type: concept
tags: [architecture, scaling, efficiency]
created: 2026-05-22
updated: 2026-05-22
sources: 1
---

## Overview

Mixture of Experts is an architecture pattern that scales total model parameters without proportionally scaling per-token compute. Instead of every token passing through every parameter, a learned router selects a small subset of "expert" feedforward networks for each token.

## How It Works

- The feedforward layer in each transformer block is replaced by multiple parallel expert FFNs
- A gating/router network assigns each token to a small number of experts (typically 2)
- Only the selected experts are activated, keeping per-token FLOPs constant even as total parameters grow

## Notable Models

- **Mixtral** (Mistral AI)
- **DeepSeek-V3**
- Many frontier models use MoE architectures

## Tradeoffs

- Scales parameters cheaply — more capacity without more compute per token
- Requires careful load balancing across experts
- Memory footprint is larger (all expert weights must be stored), though compute per token is lower

## Related

- [[transformers]] — MoE modifies the feedforward component
- [[flash-attention]] — complementary efficiency technique
- [[../sources/llm-foundations-learning-roadmap|LLM Foundations Roadmap]] (Stage 2)
