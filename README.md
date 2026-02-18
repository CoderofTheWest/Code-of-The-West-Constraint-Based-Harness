# Code of the West: Constraint-Based Agent Architecture

Architecture documentation and research for **Clint** — a relational AI system grounded in the Code of the West (Courage, Word, Brand), and **Piper** — a portable agent built on the same principles using the [OpenClaw](https://github.com/openclaw) plugin framework.

## What This Is

This repo documents two working systems that give LLM agents persistent behavioral learning — the ability to remember their mistakes, learn from corrections, and improve over time — without fine-tuning or model modification.

**Clint** is the reference implementation: a DeepSeek v3.1:671b agent with 9-source entropy decomposition, a metabolism system that converts lessons into permanent character traits, and cross-vector arbitration governed by core principles. In production since October 2025.

**Piper** is the portable version: three OpenClaw plugins reverse-engineered from Clint's architecture, including a closed-loop feedback mechanism that measures whether injected lessons actually helped.

## Contents

| File | Description |
|------|-------------|
| [growth-vector-systems-paper.md](growth-vector-systems-paper.md) | Research paper: *Growth Vector Systems: Entropy-Modulated Context Engineering for Persistent LLM Behavioral Adaptation*. Consolidates 20+ papers on attractor dynamics, steering vectors, in-context learning, and context equilibria. Documents both systems and identifies four novel contributions. |
| [CLINT-ARCHITECTURE-DIAGRAMS.md](CLINT-ARCHITECTURE-DIAGRAMS.md) | Mermaid diagrams of Clint's full architecture — server, prompt construction, agent loop, memory systems, identity evolution, tool system, and skill system. |

## Related Repositories

| Repo | Description |
|------|-------------|
| [openclaw-plugin-stability](https://github.com/CoderofTheWest/openclaw-plugin-stability) | Agent stability, entropy monitoring, growth vector injection with closed-loop feedback. The "nervous system" + "learning" plugins. |
| [openclaw-plugin-continuity](https://github.com/CoderofTheWest/openclaw-plugin-continuity) | Persistent conversational memory across sessions. The "recall" plugin. |

## Key Concepts

**Growth Vectors** — Structured records of lessons learned (corrections, capability gaps, proprioceptive signals) that are scored for relevance and injected into the LLM's context window before each generation. The research paper explains why this works through the lens of in-context learning as implicit optimization, attractor dynamics, and context equilibria.

**Entropy Monitoring** — Real-time measurement of conversational entropy from multiple sources (corrections, novel concepts, metacognitive load, temporal confabulation, quality decay). Used to modulate which growth vectors get injected and when.

**Metabolism** (Clint only) — A 30-day maturation pipeline that transforms episodic growth vectors into permanent character traits. Three-gate conjunction: time-mature AND principle-aligned AND reviewed. No published equivalent in the LLM literature.

**Closed-Loop Feedback** (Piper) — Tracks which vectors were injected, measures post-turn entropy delta, and automatically adjusts vector relevance weights. Vectors that consistently reduce entropy get boosted; destabilizing vectors get penalized.

## Novel Contributions (vs. Published Research)

The paper identifies four mechanisms not present in the existing literature:

1. **Metabolism** — Episodic vectors transform into permanent behavioral traits (no published equivalent)
2. **Entropy-aware vector selection** — Context injection modulated by agent internal state, not just query similarity
3. **Principle-filtered growth** — Normative filter on what gets learned (only value-aligned resolutions persist)
4. **Closed-loop effectiveness tracking** — Empirical measurement of injection outcomes with automatic weight adjustment

See the [full paper](growth-vector-systems-paper.md) for detailed comparison against 24 cited works.

## License

This work is shared for research and educational purposes. The code referenced in the paper is available in the linked repositories above.
