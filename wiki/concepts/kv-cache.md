---
title: KV Cache
type: concept
tags: [inference, attention, memory, serving, efficiency]
created: 2026-05-22
updated: 2026-05-22
sources: 0
---

## Overview

The KV cache (Key-Value cache) is the central data structure enabling efficient autoregressive text generation. Without it, generating each new token would require recomputing attention over the entire context from scratch — an O(n²) operation per token. With the KV cache, generation becomes O(n) per step. Understanding the KV cache is essential for understanding why LLM inference is expensive and how serving systems optimize it.

## Why It Exists: Autoregressive Generation

Transformer language models generate text one token at a time. At step `t`, the model attends to all previous tokens `1..t-1` to predict token `t`. Naively:

1. Re-run all attention layers over the full sequence `1..t`.
2. Take the last position's output to predict the next token.
3. Repeat.

This means re-computing `K = W_K * x` and `V = W_V * x` for every token at every step — even though previous tokens' K and V vectors never change. The KV cache stores these computed vectors so they only need to be computed once.

## How the KV Cache Works

During the **prefill** phase (processing the prompt):
- All K and V vectors for every layer are computed and stored in GPU memory.

During the **decode** phase (token generation):
- For each new token `t`, only compute `K_t` and `V_t` (vectors for the new token).
- Append them to the cache.
- Attention for the new token attends to cached K,V pairs — no recomputation needed.

This reduces per-token generation from O(n * d * layers) recomputes to O(d * layers) recomputes, where n is context length and d is model dimension.

## Memory Cost

The KV cache is a major memory consumer at inference time:

```
KV cache size = 2 × layers × heads × head_dim × context_length × bytes_per_element
```

For a typical 7B model (32 layers, 32 heads, head_dim=128, float16):
- Per token: 2 × 32 × 32 × 128 × 2 bytes = 524,288 bytes ≈ 0.5 MB/token
- For 4K context: ~2 GB just for the KV cache
- For 128K context: ~64 GB — often exceeding GPU memory!

This is why long-context inference is expensive and why context length limits exist beyond just attention compute.

## Grouped Query Attention (GQA) and Multi-Query Attention (MQA)

To reduce KV cache memory, modern models use fewer KV heads than query heads:

- **Multi-Head Attention (MHA)**: `n_heads` Q heads, `n_heads` K heads, `n_heads` V heads. Full KV cache.
- **Multi-Query Attention (MQA)**: `n_heads` Q heads, but only **1** K head and **1** V head (shared). KV cache reduced by `n_heads` factor. Slight quality loss.
- **Grouped Query Attention (GQA)**: `n_heads` Q heads, `n_groups` K/V heads (where `n_groups < n_heads`). Compromise between MHA and MQA.

GQA (Ainslie et al., 2023) is now the standard:
- LLaMA 2 70B: 64 Q heads, 8 KV heads (8x cache reduction)
- LLaMA 3: GQA by default
- Mistral 7B: 32 Q heads, 8 KV heads

## Connection to Flash Attention

[[flash-attention]] optimizes the *compute* of the attention operation (reducing HBM memory bandwidth). The KV cache is a separate optimization for *inference* — it avoids recomputing attention from scratch each step. They complement each other:
- Flash Attention: fast computation during training and prefill.
- KV Cache: efficient decode by avoiding redundant computation.

## PagedAttention (vLLM)

In serving systems, efficient KV cache management is critical. **PagedAttention** (Kwon et al., 2023), implemented in vLLM, applies operating system virtual memory concepts:

- KV cache is divided into fixed-size **blocks** (pages) of tokens.
- Blocks are allocated dynamically and can be non-contiguous in memory.
- Enables sharing prefixes across multiple requests (e.g., a system prompt shared by 1000 concurrent users → only stored once).
- Reduces memory waste from reserving worst-case context length upfront.

PagedAttention is why vLLM achieves 2-4x higher throughput than naive serving.

## KV Cache Quantization

Since KV cache memory grows linearly with context and batch size, quantizing it is attractive:
- Store K,V in int8 or int4 instead of float16 → 2-4x memory reduction.
- Small accuracy loss, often acceptable for production.
- Supported by frameworks like TensorRT-LLM and vLLM.

## Practical Implications

- **Context window cost scales linearly** with context length in memory, quadratically in compute (attention).
- **Batch size is limited by KV cache memory**: serving large batches with long contexts quickly exhausts GPU memory.
- **Prefill vs. decode are different compute regimes**: prefill is compute-bound (parallel), decode is memory-bandwidth-bound (sequential).
- **Prefix caching**: reusing the KV cache for shared prefixes (system prompts) is a major serving optimization.

## Related Pages

- [[flash-attention]] — efficient attention computation
- [[transformers]] — the attention mechanism the KV cache is caching
- [[speculative-decoding]] — another inference optimization that works alongside KV cache
- [[positional-encodings]] — positional encoding choices affect how KV cache generalizes to longer contexts
