---
title: Retrieval-Augmented Generation (RAG)
type: concept
tags: [retrieval, architecture-pattern, knowledge]
created: 2026-05-22
updated: 2026-05-22
sources: 1
---

## Overview

RAG is an architecture pattern that augments LLM generation with retrieved external knowledge. The core loop: retrieve relevant chunks from a knowledge store, stuff them into the prompt, then generate.

## Retrieval Methods

- **Dense retrieval (bi-encoders)**: embed queries and documents into vectors; retrieve by cosine similarity. Fast, scalable, but limited expressiveness (sentence-BERT and descendants).
- **Cross-encoders**: concatenate query + document and score jointly. Much more accurate but can't precompute document representations — doesn't scale.
- **Late interaction (ColBERT)**: encode each token separately, defer interaction to retrieval time. Per-query-token max-similarity, then sum. Near cross-encoder quality with bi-encoder scalability. ColPali extends this to vision.
- **Hybrid search**: combine dense retrieval with sparse methods like BM25

## Challenges with Naive RAG

- Chunking strategy matters enormously
- Retrieved context may be irrelevant or contradictory
- Reranking is often necessary to improve precision

## Modern Approaches

- Hierarchical retrieval (coarse then fine)
- GraphRAG: knowledge-graph-augmented retrieval
- Agentic retrieval: the model decides when and what to retrieve
- Query rewriting for better recall

## Related

- [[transformers]] — the generation backbone
- [[vision-language-models]] — ColPali bridges retrieval and vision
- [[../sources/llm-foundations-learning-roadmap|LLM Foundations Roadmap]] (Stage 4)
