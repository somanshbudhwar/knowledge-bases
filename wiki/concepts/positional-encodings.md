---
title: Positional Encodings
type: concept
tags: [attention, position, rope, alibi, transformers, context-length]
created: 2026-05-22
updated: 2026-05-22
sources: 2
---

## Overview

Self-attention is permutation-invariant: if you shuffle the input tokens, the attention mechanism produces the same output (just reordered). This is a problem — language has order, and a model that can't distinguish "dog bites man" from "man bites dog" is useless. Positional encodings inject order information into the model. The field has seen rapid evolution: sinusoidal → learned absolute → relative → ALiBi → RoPE. RoPE is now dominant.

## The Problem: Permutation Invariance

For an input sequence `x_1, ..., x_n`, self-attention computes:

```
Attention(Q, K, V) = softmax(QK^T / sqrt(d_k)) V
```

This is set-theoretic: it doesn't care about order. Without positional information, a Transformer is just a "bag of tokens" processor.

## Approach 1: Sinusoidal Encodings (Original Transformer, 2017)

Vaswani et al. added fixed sinusoidal positional encodings to token embeddings before the first layer:

```
PE(pos, 2i)   = sin(pos / 10000^(2i/d_model))
PE(pos, 2i+1) = cos(pos / 10000^(2i/d_model))
```

Properties:
- Each position gets a unique fixed vector.
- The model can attend to relative positions because `PE(pos+k)` is a linear function of `PE(pos)`.
- Generalizes to lengths longer than seen during training (in theory).
- **Problem**: the additive encoding approach doesn't interact cleanly with the attention mechanism; the model struggles to use position information deep in the network.

## Approach 2: Learned Absolute Position Embeddings

Instead of fixed sinusoids, learn an embedding `e_1, ..., e_T` (one per position, up to maximum sequence length `T`).

Used by BERT, GPT-2, GPT-3. Simple and effective within training range. **Problems**: cannot generalize to sequences longer than `T` (hard cutoff), and positions at the end of training range are less well-represented.

## Approach 3: Relative Position Encodings

Rather than encoding absolute position, encode *relative distance* between tokens. T5 (Raffel et al.) adds learned relative biases to attention logits. Transformer-XL uses recurrent memory with relative encodings.

Key insight: "how far apart are these two tokens?" is often more useful than "what position is this token?". But these methods are more complex to implement.

## Approach 4: ALiBi (Attention with Linear Biases)

ALiBi (Press et al., 2022) adds a *fixed, non-learned* bias to attention scores based on distance:

```
Attention score = QK^T / sqrt(d_k) - m · |i - j|
```

Where `m` is a per-head slope (geometrically spaced across heads) and `|i - j|` is the token distance. Tokens far apart get penalized, creating a "soft" local attention bias.

ALiBi extrapolates well beyond training length — a model trained on 2K context can generalize to 4K+ at inference. No additional parameters needed.

**Limitation**: the linear decay assumption may not always match the data; RoPE generally performs better at the same training length.

## Approach 5: RoPE (Rotary Position Embedding)

RoPE (Su et al., 2021) is now the dominant positional encoding in LLMs (LLaMA, Mistral, Gemma, Qwen, GPT-NeoX, and more).

**Core idea**: instead of *adding* position information to embeddings, *rotate* the query and key vectors by a position-dependent angle before computing attention.

For a 2D subspace, rotate by angle `m·θ` for position `m`:

```
R(m) = [[cos(mθ), -sin(mθ)],
         [sin(mθ),  cos(mθ)]]
```

Applied to d-dimensional vectors, RoPE applies these 2D rotations to pairs of dimensions independently. The rotation angle `θ` decays geometrically across dimension pairs: `θ_i = 10000^(-2i/d)`.

**Why this is elegant**: the attention dot product `q_m · k_n` (after rotation) depends only on `m - n` (relative position), not absolute positions. This naturally gives relative position encoding while maintaining the transformer's parallelism.

**Benefits over alternatives**:
- Better performance than sinusoidal and learned absolute.
- Naturally encodes relative position.
- Compatible with Flash Attention.
- Scales to long contexts.

## Context Length Extrapolation with RoPE

RoPE doesn't generalize well *out of the box* to contexts longer than the training length (the rotation angles for distant positions are poorly calibrated). Several techniques address this:

**NTK-aware Scaling** (a.k.a. "NTK scaling"): scale the base frequency `θ` by `(L_new/L_train)^(d/(d-2))` to stretch the frequency spectrum. Surprisingly effective at zero extra training cost.

**YaRN** (Yet another RoPE extensioN, Peng et al. 2023): combines NTK scaling with "dynamic scaling" that adjusts based on actual sequence length at inference. Used to extend LLaMA context from 4K to 128K+. LLaMA 3 uses a variant.

**Position Interpolation** (Chen et al. 2023): scale position indices by `L_train/L_new` before applying RoPE — compress longer sequences into the trained range. Requires fine-tuning to adapt.

**RoPE base scaling**: simply change `θ_base` from 10000 to 500000 (LLaMA 3) — longer base means slower frequency decay, better for long contexts.

## Summary: Which Encoding for Which Model

| Encoding | Used By | Key Strength |
|---|---|---|
| Sinusoidal | Original Transformer | Theoretical, rarely used now |
| Learned absolute | BERT, GPT-2, GPT-3 | Simple, effective within training range |
| Relative T5 | T5, mT5 | Relative distances |
| ALiBi | MPT, BLOOM | Zero extra params, good extrapolation |
| RoPE | LLaMA, Mistral, Gemma, Qwen | Best performance, current standard |

## Practical Takeaways

- If using a pretrained model, the positional encoding is fixed — don't change it.
- For long-context inference beyond training length, try NTK scaling first (often works with no fine-tuning).
- YaRN or RoPE base changes require fine-tuning on long-context data to fully work.
- Models trained with RoPE can be efficiently served with [[kv-cache]] because the rotation is applied during attention, not to stored embeddings.

## Related Pages

- [[transformers]] — the architecture these encodings are applied within
- [[kv-cache]] — positional encoding choice affects context length generalization
- [[flash-attention]] — attention computation that works with all position encoding types
- ([Transformer Family v2 — Lilian Weng](../sources/transformer-family-v2-lilianweng.md))
