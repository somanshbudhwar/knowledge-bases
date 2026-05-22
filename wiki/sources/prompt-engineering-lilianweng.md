---
title: "Prompt Engineering — Lilian Weng"
type: source
tags: [prompting, chain-of-thought, few-shot, tool-use, reasoning]
created: 2026-05-22
updated: 2026-05-22
sources: 0
---

## Bibliographic Info

- **URL**: https://lilianweng.github.io/posts/2023-03-15-prompt-engineering/
- **Author**: Lilian Weng (OpenAI)
- **Date**: March 2023
- **Type**: Blog post (technical survey)

## Summary

Comprehensive guide to prompt engineering techniques: zero-shot/few-shot learning, chain-of-thought, tree-of-thought, self-consistency, automatic prompt design (APE), and augmented language models (retrieval, code execution, tool use). Written as an empirical science guide with evidence from key papers.

## Key Points

- **Three few-shot biases**: majority label bias, recency bias, common token bias — affect example selection strategy
- **Chain-of-Thought**: most useful for models 50B+; complex examples transfer better than simple ones; newlines > periods for step separation
- **Zero-shot CoT**: "Let's think step by step" alone elicits reasoning chains without examples
- **Self-consistency**: sample N chains, take majority vote — 5-20% improvement at N× cost
- **Tree of Thoughts**: generates multiple reasoning branches, uses BFS/DFS search over thought trees
- **APE** (Automatic Prompt Engineer): generates and scores instruction candidates via LLM, replaces manual prompt writing
- **Toolformer**: self-supervised learning of when to call external APIs (calculator, search) — improves factuality
- **PAL/PoT**: delegates computation to Python interpreter — decouples reasoning from arithmetic

## Pages Derived From This Source

- [[concepts/chain-of-thought]]
