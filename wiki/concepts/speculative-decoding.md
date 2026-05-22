---
title: Speculative Decoding
type: concept
tags: [inference, decoding, efficiency, serving, speculative]
created: 2026-05-22
updated: 2026-05-22
sources: 0
---

## Overview

Speculative decoding is an inference acceleration technique that exploits the observation that LLM generation is memory-bandwidth-bound, not compute-bound. By using a small "draft" model to propose multiple tokens at once, and a large "target" model to verify them in a single forward pass, speculative decoding achieves 2-4x inference speedup with zero quality loss (outputs are provably identical in distribution to the target model).

Paper: "Fast Inference from Transformers via Speculative Decoding" (Leviathan et al., 2023) and "Speculative Decoding with Big Little Decoder" (Kim et al., 2023).

## The Core Problem: Decode Is Memory-Bandwidth-Bound

During autoregressive generation, the model generates one token per forward pass. Each forward pass requires:
- Loading all model weights from GPU HBM into compute units (~billions of parameters).
- Doing a small matrix-vector multiply (not matrix-matrix).

This is highly inefficient for GPU hardware designed for large parallel operations. The GPU's compute cores sit mostly idle while waiting for data to arrive from memory. **Result**: generating 1 token or generating 10 tokens in a single forward pass takes nearly the same time — you're bottlenecked on loading weights, not on computation.

Speculative decoding exploits this: if you can verify K tokens in one forward pass, you can generate K tokens for roughly the same latency as 1.

## How Speculative Decoding Works

1. **Draft phase**: use a small, fast draft model (or heuristic) to generate K candidate tokens `t_1, ..., t_K` autoregressively.

2. **Verify phase**: run the large target model on all K draft tokens in a *single parallel forward pass* (like processing a sequence, not token-by-token). The target model computes the probability distribution at each position.

3. **Accept/reject**: for each draft token `t_i`, accept it with probability:
   ```
   P_accept(t_i) = min(1, P_target(t_i | context) / P_draft(t_i | context))
   ```
   If `t_i` is rejected, sample a correction token from the adjusted distribution `P_target - P_draft` and stop.

4. At minimum, one token is always accepted (the first rejection produces one corrected token). At best, all K draft tokens are accepted and one additional target token is sampled.

**Key property**: the distribution of accepted tokens is identical to what the target model would have sampled. No quality loss — speculative decoding is an *exact* method.

## Why This Works

The math works out because of the properties of rejection sampling. The expected number of tokens accepted per verification step is:

```
E[tokens/step] = K · α + 1   (approximate, for acceptance rate α)
```

Where `α` is the average per-token acceptance rate. If the draft model is good (high overlap with target), `α` is high and speedup is large.

The speedup is:
```
Speedup ≈ (K · α + 1) / (cost_draft/cost_target + 1)
```

A small draft model might cost 1/50 of the target model per token but achieve 80% acceptance, giving a speedup of ~2.5x.

## Acceptance Rate and Draft Model Quality

The acceptance rate `α` depends on how aligned the draft model's distribution is with the target:
- **Same family**: a 7B model drafting for a 70B model of the same architecture — `α ≈ 0.7-0.85`.
- **Different family or domain mismatch**: acceptance rate drops.
- **Task-specific draft models**: fine-tuned draft models can be more aligned for specific domains.

Typical speedups in practice: **2-3x** for matched draft/target model pairs.

## Self-Speculative Decoding (No Draft Model Needed)

Variants that avoid needing a separate draft model:

### Medusa Heads (Cai et al., 2024)

Add multiple prediction heads to the target model's final layer, each trained to predict `t+1, t+2, ..., t+K` directly. During inference:
- Each Medusa head proposes a candidate for position `t+K`.
- One forward pass of the base model verifies them using a tree-structured attention.
- No separate draft model; the heads are small (cheap) additions.

Speedup: ~2-3x with minimal quality loss.

### Layer-Skip / Early-Exit

Route easy tokens through only a subset of layers (early exit), using the full model only for hard tokens. Related to [[mixture-of-experts]] adaptive computation ideas.

### EAGLE (Extrapolation Algorithm for Greater Language-model Efficiency)

Drafts using the target model's own internal hidden states (feature-level drafting), achieving higher acceptance rates than using a separate smaller model.

## Speculative Decoding in Production

Modern LLM serving frameworks implement speculative decoding:
- **vLLM**: supports speculative decoding with draft models.
- **TensorRT-LLM**: NVIDIA's serving framework has native speculative decoding.
- **Google's production systems**: reportedly use speculative decoding internally.

Operational considerations:
- Requires matching tokenizer between draft and target (must use same vocabulary).
- Draft model must be kept in GPU memory alongside target (memory overhead).
- Best when draft model is ~10-50x smaller than target.
- Works best for greedy or low-temperature sampling; less gain at high temperatures (acceptance rate drops).

## When to Use Speculative Decoding

| Scenario | Good Fit? |
|---|---|
| Low-latency single-user serving | Yes — 2-3x speedup directly reduces TTFT and latency |
| High-throughput batch serving | Less clear — throughput gains depend on batching |
| Very short sequences (<50 tokens) | Marginal — draft+verify overhead not worth it |
| Constrained decoding (structured output) | Yes — tree verification integrates naturally |
| Edge/CPU inference | Less effective (memory bandwidth less of a bottleneck) |

## Connection to Other Concepts

- **[[kv-cache]]**: both the draft and target model use KV cache during generation. The target's verification pass is parallel (like prefill), so KV cache works naturally.
- **[[flash-attention]]**: efficient attention computation helps both draft and target models.
- **[[mixture-of-experts]]**: MoE models are especially good at speculative decoding because they already have sparse activation — different experts can be seen as soft "draft paths".

## Practical Takeaways

- Speculative decoding is most impactful for single-stream latency-sensitive applications (e.g., chat with one user, code autocompletion).
- Medusa heads are easier to deploy (no separate draft model) but require model modification.
- The quality guarantee (exact same distribution as target) makes it production-safe — no accuracy-latency tradeoff.
- Draft model choice matters most: pick the fastest model with acceptable acceptance rate, not just the smallest.

## Related Pages

- [[kv-cache]] — complementary inference optimization
- [[flash-attention]] — attention computation efficiency
- [[transformers]] — the architecture being accelerated
