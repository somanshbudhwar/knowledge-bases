---
title: Reinforcement Learning from Human Feedback (RLHF)
type: concept
tags: [alignment, training, reinforcement-learning]
created: 2026-05-22
updated: 2026-05-22
sources: 1
---

## Overview

RLHF is the technique used to align pretrained language models with human preferences. It transforms a next-token predictor into a useful, safe assistant.

## The Classic Recipe

1. **Supervised Fine-Tuning (SFT)**: train on (instruction, response) pairs to teach format and behavior
2. **Reward Model**: train a model on human preference pairs (response A > response B) to score outputs
3. **PPO Optimization**: optimize the LLM policy against the reward model using Proximal Policy Optimization

## Failure Modes

- **Reward hacking**: the model exploits patterns in the reward model rather than genuinely improving
- **Mode collapse**: the model converges to a narrow set of "safe" responses

## Modern Alternatives

- **DPO (Direct Preference Optimization)**: skips the reward model entirely; optimizes preferences directly against the LLM. Simpler pipeline, often comparable results.
- **GRPO**: used in DeepSeek-R1 for reasoning model training
- **KTO, IPO**: other variants in a rapidly evolving space
- **Constitutional AI / RLAIF**: uses AI feedback instead of (or alongside) human feedback (Anthropic's approach)

## Related

- [[transformers]] — the underlying architecture being aligned
- [[../sources/llm-foundations-learning-roadmap|LLM Foundations Roadmap]] (Stage 3)
