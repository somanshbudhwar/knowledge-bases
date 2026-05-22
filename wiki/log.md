# Log

Append-only chronological record of all wiki operations.

Parse recent entries: `grep "^## \[" log.md | tail -5`

---

## [2026-05-22] ingest | LLM Foundations: A Structured Learning Roadmap

Ingested `raw/guide.txt` — a progressive 8-stage LLM learning curriculum. Created 1 source page, 6 concept pages (transformers, flash-attention, rlhf, retrieval-augmented-generation, mixture-of-experts, vision-language-models), and 2 entity pages (Andrej Karpathy, Tri Dao).

## [2026-05-22] ingest | LLM Concept Enrichment — Stage 1

Downloaded 7 resources from the web into raw/ (HuggingFace tokenizer docs, Lilian Weng transformer family and prompt engineering posts, LoRA paper, DPO paper, Chinchilla paper, Illustrated Transformer). One URL (interconnects.ai RLHF/DPO) returned 404. Created 9 new concept pages (tokenization, scaling-laws, lora, kv-cache, direct-preference-optimization, chain-of-thought, positional-encodings, pretraining, speculative-decoding). Created 3 new entity pages (ashish-vaswani, ilya-sutskever, yann-lecun). Enriched 2 existing concept pages: transformers (added Training Objectives section covering CLM/MLM/seq2seq and Key Milestones table GPT-1 through LLaMA 3) and rlhf (added Bradley-Terry reward model derivation, extended failure modes with reward hacking/sycophancy/mode collapse, added detailed Modern Alternatives section covering DPO/GRPO/IPO/KTO/Constitutional AI). Created 7 source summary pages.
