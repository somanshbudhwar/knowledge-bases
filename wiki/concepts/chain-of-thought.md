---
title: Chain-of-Thought Prompting
type: concept
tags: [prompting, reasoning, chain-of-thought, test-time-compute, cot]
created: 2026-05-22
updated: 2026-05-22
sources: 2
---

## Overview

Chain-of-Thought (CoT) prompting elicits step-by-step reasoning from language models before producing a final answer. It dramatically improves performance on multi-step reasoning tasks (math, logic, planning) where direct answer prediction fails. CoT revealed that LLMs contain latent reasoning capabilities that only emerge when given space to "think" — a finding that has driven the entire "test-time compute" paradigm.

Paper: "Chain-of-Thought Prompting Elicits Reasoning in Large Language Models" (Wei et al., 2022, NeurIPS).

## Why CoT Works

Standard prompting asks: `Q: What is 37 × 23? A: 851`. The model must compress multi-step computation into one token prediction.

CoT prompting allows intermediate steps:
```
Q: What is 37 × 23?
A: Let me compute step by step.
   37 × 20 = 740
   37 × 3 = 111
   740 + 111 = 851
   The answer is 851.
```

The model can distribute computation across many tokens, leveraging the recurrent nature of Transformer generation. Each token generation step effectively performs one "computation step."

Key empirical findings from Wei et al.:
- CoT benefits are **emergent**: only appear in models with 50B+ parameters.
- CoT helps most on tasks requiring multiple reasoning steps.
- CoT hurts on simple tasks (adds noise, wastes tokens).

## Zero-Shot CoT

Kojima et al. (2022) showed that the single phrase **"Let's think step by step"** appended to the question elicits CoT reasoning without any examples. This is "zero-shot CoT" — no hand-crafted reasoning chains needed.

Why it works: the phrase activates the model's knowledge about how reasoning is typically presented in text, and the generation proceeds to fill in the expected reasoning pattern.

## Few-Shot CoT

The original Wei et al. approach: provide 4-8 examples of (question, reasoning chain, answer). The reasoning chains are manually written by humans for the target task.

Choices matter:
- **Complexity of examples**: harder examples with more reasoning steps transfer better.
- **Diversity of examples**: diverse question types prevent spurious pattern matching.
- **Position**: later examples in the prompt have more influence (recency bias).

## Self-Consistency

Wang et al. (2022) improved CoT with **self-consistency sampling**:

1. Sample multiple reasoning chains from the model (e.g., temperature 0.7, 20 samples).
2. Extract the final answer from each chain.
3. Return the **majority vote** answer.

This works because different reasoning chains may reach the same correct answer via different paths, while errors are less likely to agree. Self-consistency gives 5-20% improvement over greedy CoT on math benchmarks. The tradeoff: 20x inference cost.

## Tree of Thoughts (ToT)

Yao et al. (2023) generalized CoT from a linear chain to a **tree**:

1. At each reasoning step, generate multiple candidate "thoughts" (next reasoning steps).
2. Evaluate each thought (via model self-evaluation or heuristic).
3. Use BFS or DFS to explore promising paths and backtrack from dead ends.

ToT dramatically improves performance on tasks requiring planning and search (e.g., the Game of 24, creative writing with constraints). The cost: many more model calls per problem.

## Process Reward Models (PRMs) vs. Outcome Reward Models (ORMs)

For training reasoning models, two approaches exist:

**Outcome Reward Model (ORM)**: rewards the final answer correctness only.
- Simple to implement (just check if answer is right).
- Doesn't distinguish correct answers reached via flawed reasoning.

**Process Reward Model (PRM)**: rewards each intermediate reasoning step.
- Requires step-level human annotations or verification.
- "Let's Verify Step by Step" (Lightman et al., OpenAI, 2023): showed PRMs outperform ORMs.
- PRMs enable better search — you can detect early reasoning errors and backtrack.

## Test-Time Compute Scaling (The o1/R1 Paradigm)

OpenAI's o1 model (2024) demonstrated a new paradigm: **scaling test-time compute** rather than just pretraining compute. Key ideas:

- Train the model to think in a hidden "reasoning trace" before answering.
- Use reinforcement learning (outcome reward: correct/incorrect) to train the thinking process.
- At inference: let the model generate longer reasoning traces for harder problems.

This is analogous to how Chinchilla showed that data matters as much as parameters — o1 showed that test-time compute can substitute for or complement pretraining compute.

DeepSeek-R1 (2025) replicated and open-sourced this approach using **GRPO** (Group Relative Policy Optimization):
- Sample 8-16 reasoning chains per problem.
- Score each on correctness (and format).
- Train the policy to prefer chains that lead to correct answers.

Key emergent behavior: models trained this way develop "aha moments" and self-correction — they learn to abandon wrong reasoning paths mid-chain.

## Practical Guidance

- Use zero-shot CoT ("think step by step") first — low cost, often surprisingly effective.
- Add few-shot examples if zero-shot CoT is inconsistent.
- Add self-consistency sampling if you need maximum accuracy and can afford 10-20x cost.
- For math/reasoning tasks, consider fine-tuned reasoning models (o1, R1, QwQ) rather than prompting general models.
- CoT is most valuable for multi-step reasoning; skip it for retrieval, classification, or simple factual questions.

## Related Pages

- [[transformers]] — the architecture generating the reasoning chains
- [[rlhf]] — RL techniques used to train reasoning models
- [[direct-preference-optimization]] — GRPO variant used for R1-style reasoning training
- ([Prompt Engineering Blog — Lilian Weng](../sources/prompt-engineering-lilianweng.md))
