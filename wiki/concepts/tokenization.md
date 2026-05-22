---
title: Tokenization
type: concept
tags: [tokenization, preprocessing, vocabulary, bpe, sentencepiece]
created: 2026-05-22
updated: 2026-05-22
sources: 2
---

## Overview

Tokenization is the process of converting raw text into the discrete integer IDs that a language model actually processes. It sits between human-readable text and the model — every design decision here affects what the model can and cannot learn. Tokenization is not just a preprocessing step; it fundamentally shapes model behavior, context window utilization, and multilingual capability.

## Why Tokenization Matters

- **Vocabulary coverage vs. sequence length tradeoff**: larger vocabulary → shorter sequences, but more embedding parameters and less data per token during training.
- **Token fertility**: the number of tokens a string requires. English is "fertile" (~1 token/word for common words); code and non-Latin languages can be 3-5x more fertile, meaning the model "sees" fewer characters per context window.
- **Arithmetic and spelling failures**: the model never sees individual characters or digits directly. "1000000" might be one token or several, making arithmetic unreliable unless careful.
- **Language bias**: vocabularies built on English text under-represent other languages, making non-English models spend more tokens (and context) on the same content.

## Tokenization Algorithms

### Byte Pair Encoding (BPE)

BPE is the most widely used algorithm (GPT family, LLaMA, Gemma, Qwen). Originally a compression algorithm (Sennrich et al., 2016):

1. Start with individual characters as the base vocabulary.
2. Repeatedly merge the most frequent adjacent token pair.
3. Stop when vocabulary reaches target size (e.g., 50,257 for GPT-2; 128,000 for LLaMA 3).

Result: common words are single tokens; rare words decompose into known subwords.

**Byte-level BPE** (used by GPT-2, GPT-4, LLaMA): treats the base vocabulary as 256 raw byte values rather than Unicode characters. This guarantees no `<unk>` tokens — any string can be tokenized. GPT-2 has vocab size 50,257 = 256 bytes + 50,000 merges + 1 special token.

### WordPiece

Used by BERT-family models. Similar to BPE but merges pairs that maximize likelihood of the training corpus, not raw frequency:

```
score(u, g) = freq("ug") / (freq("u") × freq("g"))
```

This means it prefers merging tokens that are *informative* (appear together more than chance predicts), producing slightly different vocabularies than BPE for the same data.

### Unigram Language Model

Used by T5, ALBERT, many multilingual models. Works top-down rather than bottom-up:

1. Start with a large candidate vocabulary.
2. Assign probabilities to all subwords via a unigram language model.
3. Iteratively prune tokens whose removal increases total loss least (bottom 10-20%).
4. Repeat until target vocabulary size is reached.

During inference, tokenization is probabilistic (picks highest-probability segmentation). Can also sample different tokenizations during training for regularization.

### SentencePiece

SentencePiece (Kudo & Richardson, 2018) is a *library* that implements BPE or Unigram, not a separate algorithm. Its key innovation: it treats the entire input as a raw byte stream, representing spaces as the special character `▁`. This makes it language-agnostic — no need for language-specific pre-tokenization rules. Used by T5, LLaMA, Gemma, and most multilingual models.

### tiktoken

OpenAI's fast BPE tokenizer library used for GPT-3.5, GPT-4, and related models. Vocabularies (called "encodings"): `cl100k_base` (100,277 tokens for GPT-4), `o200k_base` (200,019 tokens for GPT-4o). Much faster than HuggingFace tokenizers in practice.

## Vocabulary Size Tradeoffs

| Vocab Size | Pros | Cons |
|---|---|---|
| Small (~8K) | Fewer embedding params, more data per token | Long sequences, poor rare-word coverage |
| Medium (~32-50K) | Classic sweet spot (BERT, GPT-2) | Reasonable balance |
| Large (~100-200K) | Short sequences, good multilingual coverage | Large embedding tables, sparse training |

Modern frontier models trend toward 100K+ vocabularies to improve token efficiency across languages and code.

## Token Fertility and Multilingual Fairness

**Token fertility** = average tokens per word for a given language. A model trained with an English-centric vocabulary might tokenize:
- English: "hello" → 1 token
- Chinese: "你好" → 2-4 tokens (each character may be its own token)
- Turkish (agglutinative): words can be 5-10 tokens

This means non-English users get far less information per context window. Correcting this is an active research area.

## Special Tokens

Tokenizers add special tokens for control flow:
- `<|endoftext|>` / `</s>` / `[EOS]` — end of sequence
- `[CLS]`, `[SEP]` — BERT-style classification / separator
- `<|im_start|>`, `<|im_end|>` — OpenAI chat format markers
- `[PAD]` — padding for batch processing

## Practical Takeaways

- **Never assume 1 token = 1 word**: budget ~1.3 tokens/word for English prose, more for code and non-English.
- **Longer prompts cost more**: context window limits are in tokens, not words.
- **Tokenizer and model must match**: using the wrong tokenizer produces garbage input IDs.
- **Vocabulary size ≠ capability**: a 128K vocabulary model isn't necessarily smarter, just more efficient at representing diverse text.

## Related Pages

- [[transformers]] — the architecture that consumes token IDs
- [[pretraining]] — decisions about data, tokenizers, and vocab during pretraining
- [[scaling-laws]] — how token count affects compute-optimal training
- ([HuggingFace Tokenizer Summary](../sources/tokenizer-summary-hf.md))
