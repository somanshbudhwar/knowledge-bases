---
title: "Direct Preference Optimization: Your Language Model is Secretly a Reward Model"
type: source
tags: [dpo, alignment, rlhf, preference-learning, fine-tuning]
created: 2026-05-22
updated: 2026-05-22
sources: 0
---

## Bibliographic Info

- **URL**: https://arxiv.org/abs/2305.18290
- **Authors**: Rafael Rafailov, Archit Sharma, Eric Mitchell, Stefano Ermon, Christopher D. Manning, Chelsea Finn (Stanford)
- **Date**: May 2023
- **Published at**: NeurIPS 2023
- **Type**: Research paper

## Abstract (paraphrased)

Large unsupervised LMs acquire broad world knowledge and reasoning capabilities, but precise behavioral control remains difficult due to the unsupervised nature of training. Existing RLHF methods are complex and unstable, as they use a separate reward model trained on human preferences and use RL (typically PPO) to optimize the LM policy. The authors introduce DPO, a simpler approach that directly optimizes the policy using a classification loss equivalent to the implicit reward model in RLHF.

## Key Contributions

1. Shows the reward model in RLHF is implicitly parameterized by the policy — no separate model needed.
2. Derives the DPO loss from the RLHF objective analytically.
3. DPO is stable (classification loss), efficient (no sampling), and matches or beats PPO on sentiment, summarization, and dialogue.
4. Spawned an entire research program of "offline preference optimization" methods.

## Core Formula

```
L_DPO(π_θ; π_ref) = -E[log σ(β log(π_θ(y_w|x)/π_ref(y_w|x)) - β log(π_θ(y_l|x)/π_ref(y_l|x)))]
```

## Pages Derived From This Source

- [[concepts/direct-preference-optimization]]
- [[concepts/rlhf]] (enrichment)
