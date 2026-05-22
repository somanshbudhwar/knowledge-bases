---
title: "LLM Foundations: A Structured Learning Roadmap"
type: source
tags: [llm, curriculum, transformers, deep-learning, roadmap]
created: 2026-05-22
updated: 2026-05-22
sources: 1
---

## Summary

A progressive 8-stage curriculum for building a solid foundation in modern LLMs, from mathematical prerequisites through frontier research topics. Each stage builds on the last; the guide emphasizes implementation over passive reading.

## Stage Breakdown

### Stage 0 — Prerequisites
- Linear algebra, probability, calculus, neural network basics
- [[../concepts/transformers|Tokenization]] (BPE, WordPiece, SentencePiece) and embeddings (word2vec, GloVe)
- Recommended: 3Blue1Brown's "Neural Networks" series; [[../entities/andrej-karpathy|Andrej Karpathy]]'s "Zero to Hero" course

### Stage 1 — The Transformer and Attention
- [[../concepts/transformers|Self-attention]]: Q, K, V vectors; softmax(QK^T / sqrt(d_k)) * V; scaling factor prevents softmax saturation
- Multi-head attention: parallel heads specializing in different patterns
- Positional encodings: sinusoidal → learned → relative → RoPE (used in nearly all modern LLMs)
- Full transformer block: attention + residual + norm + feedforward (SwiGLU) + residual + norm
- Training objectives: causal LM (GPT), masked LM (BERT), encoder-decoder (T5)
- Key paper: "Attention Is All You Need" (Vaswani et al., 2017)

### Stage 2 — Scaling and Efficiency
- Quadratic O(n^2) cost of attention in sequence length
- [[../concepts/flash-attention|Flash Attention]]: tiling + online softmax to keep computation in fast SRAM ([[../entities/tri-dao|Tri Dao]])
- KV caching for tractable autoregressive inference; memory grows linearly with context
- GQA/MQA: shared K/V heads to shrink KV cache (Llama 2/3, Mistral)
- [[../concepts/mixture-of-experts|Mixture of Experts (MoE)]]: route tokens to expert subsets; scale params without scaling per-token compute
- Quantization: INT8/INT4 inference (GPTQ, AWQ, GGUF)
- Long-context: sliding window, Ring Attention, YaRN

### Stage 3 — Training, Alignment, and Post-Training
- Pretraining: data curation, compute scaling, Chinchilla scaling laws
- SFT on (instruction, response) pairs
- [[../concepts/rlhf|RLHF]]: reward model + PPO; reward hacking and mode collapse risks
- Modern alternatives: DPO, GRPO (DeepSeek-R1), KTO, IPO
- Constitutional AI / RLAIF (Anthropic's approach)
- Reasoning / test-time compute: o1/R1 paradigm — scale inference, not just training

### Stage 4 — Retrieval and Late Interaction
- Dense retrieval with bi-encoders (sentence-BERT)
- Cross-encoders: accurate but don't scale
- Late interaction (ColBERT): per-token encoding with deferred interaction; ColPali extends to vision
- [[../concepts/retrieval-augmented-generation|RAG]]: retrieve → stuff into prompt → generate; chunking, hybrid search, reranking
- Modern: hierarchical retrieval, GraphRAG, agentic retrieval

### Stage 5 — Vision-Language Models
- [[../concepts/vision-language-models|VLMs]]: ViT for image patches → CLIP for shared image-text space
- Dominant pattern: vision encoder → projector → LLM (LLaVA as canonical example)
- Frontier: Flamingo (cross-attention), BLIP-2 (Q-Former), Qwen-VL
- ColPali: late interaction applied to document images (no OCR needed)
- Video/audio: temporal modeling, Whisper for ASR

### Stage 6 — Agents, Tools, and Inference Systems
- Tool use / function calling; ReAct pattern
- Agentic loops: plan → act → observe → reflect
- Inference serving: vLLM, PagedAttention, continuous batching, speculative decoding
- Evaluation: MMLU, HumanEval, SWE-bench; contamination and gaming challenges

### Stage 7 — Frontier Topics
- Interpretability: sparse autoencoders, mechanistic interpretability (Anthropic, Neel Nanda)
- Alignment/safety: scalable oversight, red-teaming, deceptive alignment
- State-space models: Mamba, hybrid architectures
- Test-time scaling: chain-of-thought, tree search
- Synthetic data and self-improvement

## Methodology Advice

- **Implement, don't just read** — build a tiny transformer, implement attention from scratch, read nanoGPT line by line
- **Read primary sources** — papers over blog posts
- **Stay current carefully** — AlphaSignal, Sebastian Raschka's newsletter, Nathan Lambert's Interconnects
- **Build end-to-end** — a RAG system, a fine-tuned model, a VLM tool
- Realistic timeline: 2-3 months for Stages 1-2, another 2-3 months for 3-5, then ongoing
