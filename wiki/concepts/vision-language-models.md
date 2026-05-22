---
title: Vision-Language Models (VLMs)
type: concept
tags: [multimodal, vision, architecture]
created: 2026-05-22
updated: 2026-05-22
sources: 1
---

## Overview

Vision-Language Models extend LLMs to process images (and video/audio) alongside text. One of the most active research areas in modern AI.

## Building Blocks

- **Vision Transformer (ViT)**: splits images into patches, embeds each, processes through a transformer. Images become sequences of patch tokens.
- **CLIP**: contrastive pretraining of paired image and text encoders. Creates a shared embedding space but doesn't generate text. Underlies most VLM architectures.

## Dominant Architecture Pattern

**Vision encoder → Projector → LLM**

The projector maps visual features into the LLM's token space so images become "tokens" the LLM attends over.

- **LLaVA**: canonical simple example using an MLP projector. Understand this deeply first.
- **BLIP-2**: uses a Q-Former as projector
- **Flamingo**: cross-attention layers injected into the LLM (more expressive, more complex)
- **Qwen-VL**: modern production VLM

## Tradeoffs

- MLP projector: simpler, trains faster
- Cross-attention: more expressive but more complex integration
- Q-Former: learnable queries bridge vision and language

## Extensions

- **ColPali**: late interaction applied to document page images — retrieve PDFs without OCR (bridges [[retrieval-augmented-generation|RAG]] and vision)
- **Video**: temporal modeling over frame sequences
- **Audio**: spectrograms or audio tokenizers; Whisper for ASR; native audio-in/audio-out models

## Related

- [[transformers]] — the backbone architecture
- [[retrieval-augmented-generation]] — ColPali connects vision and retrieval
- [[../sources/llm-foundations-learning-roadmap|LLM Foundations Roadmap]] (Stage 5)
