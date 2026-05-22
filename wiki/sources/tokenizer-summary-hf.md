---
title: "Tokenization Algorithms — HuggingFace Docs"
type: source
tags: [tokenization, bpe, wordpiece, sentencepiece, unigram]
created: 2026-05-22
updated: 2026-05-22
sources: 0
---

## Bibliographic Info

- **URL**: https://huggingface.co/docs/transformers/en/tokenizer_summary
- **Publisher**: HuggingFace
- **Ingested**: 2026-05-22
- **Type**: Technical documentation

## Summary

A clear, practical explanation of the three main subword tokenization algorithms used in Transformers: BPE, Unigram, and WordPiece — plus word-level and character-level approaches. Uses a running example ("hug", "pug", "pun", "bun", "hugs") to illustrate how each algorithm builds its vocabulary.

## Key Points

- **BPE**: iterative bottom-up merge of most-frequent adjacent pair. Deterministic. Used by GPT, LLaMA, Gemma.
- **Byte-level BPE**: uses 256 byte values as base, eliminating `<unk>`. Used by GPT-2, GPT-4.
- **WordPiece**: like BPE but merges on *likelihood gain* (`freq(AB) / freq(A) × freq(B)`) not raw frequency. Used by BERT.
- **Unigram**: top-down pruning of a large candidate vocabulary using a probabilistic language model. Probabilistic tokenization. Used by T5.
- **SentencePiece**: library that wraps BPE or Unigram with language-agnostic raw-text processing (spaces represented as `▁`). Used by LLaMA, Gemma, T5.

## Pages Derived From This Source

- [[concepts/tokenization]]
