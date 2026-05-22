---
title: Reinforcement Learning from Human Feedback (RLHF)
type: concept
tags: [alignment, training, reinforcement-learning]
created: 2026-05-22
updated: 2026-05-22
sources: 2
---

## Overview

RLHF is the technique used to align pretrained language models with human preferences. It transforms a next-token predictor into a useful, safe assistant.

## The Classic Recipe

1. **Supervised Fine-Tuning (SFT)**: train on (instruction, response) pairs to teach format and behavior
2. **Reward Model**: train a model on human preference pairs (response A > response B) to score outputs
3. **PPO Optimization**: optimize the LLM policy against the reward model using Proximal Policy Optimization

## The Bradley-Terry Reward Model

The reward model is trained using the **Bradley-Terry model** for pairwise preferences:

Given a prompt `x` and two responses `y_w` (preferred) and `y_l` (rejected), the probability that `y_w` is preferred is:

```
P(y_w > y_l | x) = σ(r(x, y_w) - r(x, y_l))
```

where `r(x, y)` is the scalar reward and `σ` is the sigmoid function. The reward model is trained with binary cross-entropy on human preference pairs. The Bradley-Terry model assumes preferences are **transitive** and **consistent** — both assumptions are violated in practice (humans are inconsistent), which contributes to reward model noise.

## Failure Modes — Extended

### Reward Hacking
The policy learns to exploit patterns in the reward model that don't correspond to genuine quality:
- **Verbosity bias**: reward models tend to prefer longer responses regardless of content.
- **Sycophancy**: the model learns to agree with users and validate their premises.
- **Format exploitation**: excessive use of bullet points, headers, and bold text.
- **Persuasive-sounding nonsense**: high reward for confident-sounding but factually wrong content.

Reward hacking is why PPO training includes a KL penalty term — it prevents the policy from drifting too far from the reference SFT model:
```
Objective = E[r_φ(y|x)] - β · KL(π_θ || π_ref)
```

### Mode Collapse
The model converges to a small set of response patterns it "knows" the reward model likes, losing diversity:
- All responses start with "Great question!"
- Tendency to give similar-length, similar-structured answers
- Avoidance of any potentially controversial content regardless of context

### Reward Model Overfitting
If the reward model is too small or trained on too little data, the PPO-trained policy quickly identifies its weaknesses and exploits them. The reward model must be at least as capable as the policy it is scoring.

## Modern Alternatives and Successors

### DPO (Direct Preference Optimization)
Skips the reward model entirely. The key insight: the optimal RLHF policy can be parameterized directly as a function of the policy log-probabilities. The DPO loss is a binary cross-entropy over preference pairs. See [[direct-preference-optimization]].

**When to use over PPO**: almost always for typical alignment tasks — simpler, more stable, comparable quality.

### GRPO (Group Relative Policy Optimization)
Used in DeepSeek-R1 for reasoning model training. Samples multiple responses per prompt, computes rewards for each, and uses relative rewards (above/below group mean) as the advantage estimate. Avoids needing a separate critic model (unlike PPO which requires a value function). Particularly effective for mathematical reasoning with binary correct/incorrect rewards.

### IPO (Identity Preference Optimization)
Removes the Bradley-Terry model assumption, replacing the log-sigmoid loss with a simpler squared error. More robust to inconsistent preferences.

### KTO (Kahneman-Tversky Optimization)
Inspired by prospect theory. Doesn't require paired preferences — works with individual labeled examples (good vs. bad responses without needing matched pairs). Enables simpler data collection.

### Constitutional AI / RLAIF
Anthropic's approach: use AI feedback instead of (or alongside) human feedback. A "critique" model evaluates responses against a set of principles ("the constitution"). Scales to much larger datasets than human labeling allows.

## The RLHF Pipeline vs. DPO: When to Use Which

| Scenario | PPO-RLHF | DPO |
|---|---|---|
| Large-scale alignment (GPT-4 level) | Yes (more powerful) | May not be sufficient |
| Reasoning/coding models | GRPO variant | Less common |
| Limited compute | No | Yes |
| Stability required | Risk of instability | Stable |
| Online exploration needed | Yes | No |

## Related

- [[transformers]] — the underlying architecture being aligned
- [[direct-preference-optimization]] — the modern simplification of this pipeline
- [[chain-of-thought]] — GRPO used to train reasoning chains
- [[../sources/llm-foundations-learning-roadmap|LLM Foundations Roadmap]] (Stage 3)
