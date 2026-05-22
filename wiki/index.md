---
title: Wiki Index
type: overview
updated: 2026-05-22
---

# Wiki Index

Content catalog for this knowledge base. Updated on every ingest.

## Sources

- [[sources/llm-foundations-learning-roadmap]] — Progressive 8-stage curriculum for learning modern LLMs (2026-05-22)
- [[sources/tokenizer-summary-hf]] — HuggingFace docs: BPE, WordPiece, Unigram, SentencePiece algorithms explained (2026-05-22)
- [[sources/transformer-family-v2-lilianweng]] — Lilian Weng survey of Transformer variants and efficiency improvements (2026-05-22)
- [[sources/prompt-engineering-lilianweng]] — Lilian Weng guide to CoT, few-shot, self-consistency, tool use (2026-05-22)
- [[sources/lora-paper-arxiv]] — LoRA: Low-Rank Adaptation of Large Language Models (Hu et al. 2021) (2026-05-22)
- [[sources/dpo-paper-arxiv]] — Direct Preference Optimization paper (Rafailov et al. 2023) (2026-05-22)
- [[sources/chinchilla-paper-arxiv]] — Chinchilla compute-optimal training scaling laws (Hoffmann et al. 2022) (2026-05-22)
- [[sources/illustrated-transformer-jalammar]] — Illustrated Transformer visual explainer by Jay Alammar (2026-05-22)

## Entities

- [[entities/andrej-karpathy]] — AI researcher and educator; "Zero to Hero" course, nanoGPT
- [[entities/tri-dao]] — Researcher; creator of Flash Attention
- [[entities/ashish-vaswani]] — Lead author of "Attention Is All You Need"; co-founded Adept AI
- [[entities/ilya-sutskever]] — Co-founder OpenAI; scaling hypothesis proponent; founded SSI
- [[entities/yann-lecun]] — Meta Chief AI Scientist; CNN pioneer; critic of LLM-centric AI path

## Concepts

- [[concepts/transformers]] — The foundational architecture behind modern LLMs
- [[concepts/flash-attention]] — IO-aware exact attention algorithm for GPU efficiency
- [[concepts/rlhf]] — Reinforcement Learning from Human Feedback for alignment
- [[concepts/retrieval-augmented-generation]] — Architecture pattern augmenting LLMs with retrieved knowledge
- [[concepts/mixture-of-experts]] — Sparse architecture scaling parameters without scaling per-token compute
- [[concepts/vision-language-models]] — Extending LLMs to process images, video, and audio
- [[concepts/tokenization]] — BPE, WordPiece, SentencePiece, tiktoken; vocabulary tradeoffs; token fertility
- [[concepts/scaling-laws]] — Kaplan 2020 power laws; Chinchilla compute-optimal training (~20 tokens/param)
- [[concepts/lora]] — Low-Rank Adaptation; ΔW=BA; QLoRA; PEFT; when to use vs full fine-tuning
- [[concepts/kv-cache]] — KV cache for autoregressive generation; GQA/MQA; PagedAttention; memory costs
- [[concepts/direct-preference-optimization]] — DPO as RLHF without RL; the DPO loss; PPO comparison; variants
- [[concepts/chain-of-thought]] — CoT prompting; zero-shot CoT; tree-of-thought; test-time compute scaling
- [[concepts/positional-encodings]] — Sinusoidal, learned, ALiBi, RoPE; context length extrapolation (YaRN)
- [[concepts/pretraining]] — Next-token prediction; data curation; FineWeb/DCLM; instabilities; optimizer
- [[concepts/speculative-decoding]] — Draft model + target verification; 2-4x speedup; Medusa; zero quality loss

## Syntheses

*(none yet)*
