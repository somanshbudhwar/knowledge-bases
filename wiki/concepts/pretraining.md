---
title: Pretraining
type: concept
tags: [pretraining, training, data, compute, llm, next-token-prediction]
created: 2026-05-22
updated: 2026-05-22
sources: 1
---

## Overview

Pretraining is the phase where a language model learns general knowledge about language, facts, reasoning, and code by predicting the next token across vast amounts of text. It produces a "base model" — a distribution over next tokens — that can subsequently be fine-tuned and aligned for specific uses. Understanding pretraining is understanding where almost all of a model's capability comes from.

## The Objective: Next-Token Prediction

Modern LLMs are trained with the **causal language modeling** (CLM) objective, also called next-token prediction or autoregressive language modeling:

```
L = -Σ_{t=1}^{T} log P(x_t | x_1, ..., x_{t-1}; θ)
```

Given a sequence of tokens `x_1, ..., x_T`, predict each token from all preceding tokens. Minimize cross-entropy loss averaged over all positions.

This deceptively simple objective requires the model to learn:
- Grammar and syntax (to predict grammatical next words)
- World knowledge (to complete factual sentences)
- Reasoning patterns (to complete logical arguments)
- Code structure (if code is in the data)
- Style and register (to maintain consistent writing)

**Why this works**: Predicting text well is equivalent to compressing text well. Compression requires understanding. A model that can accurately predict the next token in a biology textbook must have internalized biology.

## Data Curation: The Most Underrated Part

The training dataset determines what the model knows. Raw internet text is noisy — deduplication, quality filtering, and source mixing are critical.

### Major Public Pretraining Datasets

| Dataset | Tokens | Key Property |
|---|---|---|
| The Pile (EleutherAI) | ~825B | Diverse: books, code, papers, web |
| RedPajama v1 | ~1.2T | LLaMA-like mix, fully open |
| RedPajama v2 | ~30T | Raw, requires filtering |
| FineWeb (HuggingFace) | ~15T | High-quality Common Crawl subset |
| DCLM (Apple) | ~4T | Strong quality filtering, state-of-art open baseline |
| Dolma (AI2) | ~3T | Open, permissive license |

Common Crawl (raw internet) is the backbone of most datasets, but must be heavily filtered.

### Data Quality Filtering Steps

1. **Language filtering**: remove non-target-language documents.
2. **Deduplication**: remove near-duplicate documents (exact and MinHash fuzzy dedup). Duplicates hurt — the model memorizes them instead of learning general patterns.
3. **Quality filtering**: remove low-quality text. Heuristics: minimum length, max symbol/word ratio, perplexity filtering against a small reference LM (low-perplexity = natural text).
4. **Content filtering**: remove harmful content, PII, etc.
5. **Domain mixing**: carefully weight different sources (web, books, code, papers).

Domain mixing is a major research area — the optimal mix is task-dependent. Code improves reasoning even for text-only tasks.

### Data Mix Examples

- **LLaMA 1**: 67% Common Crawl, 15% C4, 4.5% GitHub, 4.5% Wikipedia, 4.5% Books, 2.5% ArXiv, 2% StackExchange
- **GPT-3**: ~60% filtered Common Crawl, 16% WebText2, 8% Books1, 8% Books2, 3% Wikipedia

## Tokenization Decisions at Pretraining

Choices made during pretraining are locked in permanently:
- **Vocabulary**: size (32K, 64K, 128K), algorithm (BPE, SentencePiece), special tokens.
- **Sequence length**: training context window (affects positional encoding capacity).
- **Packing**: to maximize GPU utilization, short documents are concatenated ("packed") into full-length sequences with separator tokens.

See [[tokenization]] for detailed discussion.

## Compute Budget and Scaling

The [[scaling-laws]] literature tells us how to allocate compute:
- Chinchilla-optimal: ~20 tokens per parameter.
- For inference efficiency: train smaller models on many more tokens than Chinchilla suggests.

Modern pretraining runs:
- **Hardware**: thousands of H100s or TPU v4 pods, weeks to months.
- **Precision**: bfloat16 for weights, float32 for optimizer states, with gradient scaling.
- **Parallelism**: data parallel + tensor parallel + pipeline parallel ("3D parallelism").

## Training Instabilities

Large training runs face characteristic instabilities:

- **Loss spikes**: sudden large increases in loss, often caused by bad data batches or numerical issues. Common treatment: roll back a few hundred steps and skip the offending data.
- **Gradient explosion**: addressed by gradient clipping (clip to max norm ~1.0).
- **Attention entropy collapse**: attention weights collapse to near-one-hot, causing training divergence. Related to learning rate and initialization choices.
- **Rank collapse**: model's hidden states lose diversity across positions.

Engineering pretraining stability is a significant and underappreciated part of frontier model development.

## Optimizer and Hyperparameters

Standard setup:
- **Optimizer**: AdamW with `β1=0.9, β2=0.95, ε=1e-8, weight_decay=0.1`.
- **Learning rate schedule**: linear warmup (2000 steps) + cosine decay to 10% of peak LR.
- **Peak LR**: ~3e-4 for 7B models, scales down as models get larger.
- **Batch size**: large (millions of tokens per batch), scaled following Chinchilla batch scaling laws.
- **Gradient clipping**: max norm 1.0.

## Checkpoint Averaging and Evaluation

- **Checkpoint averaging**: averaging the last several checkpoints (Polyak averaging) gives a small but consistent improvement in final model quality.
- **Evaluation**: tracked continuously during training on held-out sets and standard benchmarks (HellaSwag, ARC, MMLU).
- **Loss curves**: smooth, predictable power-law curves. Unexpected behavior signals a data or training problem.

## What Pretraining Doesn't Do

Pretraining produces a next-token predictor, not a helpful assistant:
- **No instruction following**: the model predicts the next token, not "answers" questions.
- **No alignment**: the model freely completes harmful content if that's the likely next token.
- **No conversation**: the model treats conversation the same as any other text format.

Post-pretraining (SFT + RLHF/DPO) is required to make the model useful. See [[rlhf]] and [[direct-preference-optimization]].

## Related Pages

- [[tokenization]] — how text becomes tokens for training
- [[scaling-laws]] — guiding compute/data allocation decisions
- [[transformers]] — the architecture being pretrained
- [[rlhf]] — post-pretraining alignment
- [[direct-preference-optimization]] — modern alignment technique
