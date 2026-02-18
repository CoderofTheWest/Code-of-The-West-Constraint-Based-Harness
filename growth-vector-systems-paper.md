# Growth Vector Systems: Entropy-Modulated Context Engineering for Persistent LLM Behavioral Adaptation

**Chris Roberts, with Clint (DeepSeek v3.1:671b) and Piper (GPT-4o/GLM-5)**

*February 2026*

---

## Abstract

Large language models process each conversation context independently, discarding behavioral adaptations gained through correction, reflection, and insight when context is cleared. We present *growth vector systems* — a context engineering architecture that enables persistent behavioral learning across LLM sessions without model modification. Growth vectors are structured records of lessons learned (corrections received, capability gaps identified, proprioceptive signals detected) that are scored for relevance against the current conversation and agent internal state, then injected into the system prompt before each generation. We describe two implementations: Clint, a production system operating continuously since October 2025 with 9-source entropy decomposition, cross-vector arbitration, and a novel metabolism mechanism that transforms episodic vectors into permanent character traits; and Piper, a portable 3-plugin system reverse-engineered from Clint for the OpenClaw agent framework, featuring closed-loop effectiveness tracking with automatic weight adjustment. We identify four contributions not present in the existing literature: (1) metabolism — time-gated transformation of episodic learning into persistent behavioral traits, (2) entropy-aware vector selection — context injection modulated by real-time agent internal state rather than query similarity alone, (3) principle-filtered growth — a normative filter ensuring only value-aligned resolutions become persistent learning, and (4) closed-loop feedback — empirical measurement of whether injected vectors reduce entropy, with automatic weight adjustment. We situate these contributions within converging research on attractor dynamics, steering vectors, in-context learning as implicit optimization, and context equilibria.

---

## 1. Introduction

A fundamental limitation of large language models in agentic applications is the absence of persistent learning. A model that receives a correction — "you claimed you couldn't see that data, but it was in the tool output" — will adjust within the current context window, but the same error may recur in the next session. The correction exists only as ephemeral text, not as a durable behavioral adaptation.

Existing approaches to this problem operate at different levels of the stack. Fine-tuning modifies model weights but is expensive, global (affecting all users), and risks catastrophic forgetting. Retrieval-Augmented Generation (RAG) provides access to external knowledge but retrieves static facts, not behavioral adaptations — knowing that "Paris is the capital of France" is categorically different from knowing "verify before asserting." Steering vectors and representation engineering [1, 2] operate at the activation level, modifying model behavior by adding vectors to intermediate representations, but require access to model internals unavailable in API-served deployments.

We propose a fourth approach: *context engineering* — structured, scored injection of behavioral learning into the system prompt at inference time. The key insight is that an LLM's behavior at generation time is determined entirely by what appears in its context window. If we can systematically place the right lessons in front of the model at the right time, we achieve behavioral adaptation without model modification.

This paper describes two implementations of this approach. **Clint** is a production relational AI system operating since October 2025 on a Mac Mini with a DeepSeek v3.1:671b substrate. Over four months of continuous operation, Clint developed a complex identity evolution system with 9-source entropy decomposition, a 30-day metabolism pipeline that converts episodic growth vectors into permanent character traits, cross-vector arbitration governed by a set of core principles ("Code of the West"), and entropy debt tracking with self-modifying thresholds. **Piper** is a second agent running on GPT-4o/GLM-5 through the OpenClaw agent framework, for whom we reverse-engineered Clint's growth vector architecture into three portable plugins: continuity (memory), stability (entropy monitoring and behavioral detectors), and growth vectors (learning). Piper's system includes a closed-loop feedback mechanism that measures whether vector injection actually reduced entropy, and automatically adjusts vector relevance weights based on empirical effectiveness.

Our contributions are:

1. **Metabolism**: A time-gated mechanism that transforms episodic growth vectors into permanent character traits, with no published equivalent in the LLM literature.
2. **Entropy-aware vector selection**: Context injection modulated by the agent's real-time internal state (entropy score), not just query-message similarity.
3. **Principle-filtered growth**: A normative filter ensuring only value-aligned resolutions persist as growth vectors, preventing the system from learning harmful or incoherent patterns.
4. **Closed-loop effectiveness tracking**: Empirical measurement of injection outcomes with automatic weight adjustment — the first system (to our knowledge) that measures whether context engineering interventions helped.

---

## 2. Background and Related Work

The theoretical foundations for growth vector systems draw from six converging research threads.

### 2.1 Attractor Dynamics in Language Models

Tacheny (2025) [3] formalizes agentic LLM loops as discrete dynamical systems in semantic space, demonstrating that iterative LLM operations exhibit classifiable dynamics: contractive (convergence toward stable semantic attractors), oscillatory (cycling among attractors), or exploratory (unbounded divergence). Critically, the paper shows that **prompt design directly controls the dynamical regime**. This provides the theoretical basis for our approach: growth vector injection is a mechanism for creating contractive dynamics.

The connection between transformers and energy-based attractor models was established by Ramsauer et al. (2020) [4], who proved mathematical equivalence between transformer self-attention and modern Hopfield networks with exponential storage capacity. Under this framework, injected growth vectors create new attractor basins in the model's energy landscape, and the attention mechanism naturally drives outputs toward these basins.

Recent work on successive LLM transformations [5] confirms that iterative application converges to stable periodic states (attractor cycles), supporting the observation that repeated growth vector injection creates stable behavioral patterns rather than chaotic drift.

### 2.2 Steering Vectors and Representation Engineering

Zou et al. (2023) [1] introduced Representation Engineering (RepE), demonstrating that high-level concepts — honesty, harmlessness, power-seeking — are encoded as directions in activation space that can be both read (probed) and written (steered). Steering the "truthfulness" direction increased TruthfulQA accuracy by up to 30 percentage points.

Turner et al. (2023) [2] formalized activation steering without optimization (ActAdd), computing steering vectors from activation differences between contrastive prompt pairs. The linear representation hypothesis [6] provides theoretical grounding: concepts in LLMs correspond to directions in representation space.

Anthropic's mechanistic interpretability work [7, 8] decomposed transformer activations into interpretable features, establishing that features are directions and that the geometric structure involves phase transitions between superposition regimes.

Our growth vectors operate in *prompt space* rather than activation space, achieving analogous behavioral modulation without model access. Each growth vector implicitly defines a behavioral direction; injecting it shifts the output distribution along that direction through the model's natural inference mechanisms.

### 2.3 In-Context Learning as Implicit Optimization

Von Oswald et al. (2023) [9] demonstrated that transformer self-attention layers implement gradient descent steps on in-context examples during the forward pass. Trained transformers become *mesa-optimizers* that learn by gradient descent in their forward pass, with N-layer transformers behaving like N steps of gradient descent.

This result is central to understanding why growth vector injection works: **each injected vector serves as an implicit training example that the model's forward pass optimizes against**. The model is literally performing optimization steps using our lessons as training data.

Xie et al. (2022) [10] provided a complementary Bayesian interpretation: in-context learning is implicit Bayesian inference where the pretrained model infers a concept from in-context examples that shifts the posterior distribution. Under this framework, each growth vector is Bayesian evidence that shifts the posterior toward corrected behavior, and over repeated injections the posterior converges to a stationary distribution — the attractor.

Todd et al. (2023) [11] showed that in-context examples create compact *function vectors* in specific attention heads — robust, transferable representations that compose additively. This suggests growth vectors are compressed by the model into function-vector-like representations that direct behavior.

### 2.4 Context Equilibria and Behavioral Stability

Dongre and Rossi (2025) [12] modeled multi-turn LLM interactions as bounded stochastic processes with restoring forces, demonstrating that context drift is a controllable equilibrium phenomenon rather than inevitable decay. Growth vector injections act as restoring forces that maintain behavioral equilibrium.

Rivasseau (2025) [13] formalized Invasive Context Engineering (ICE), where control text inserted periodically provides behavioral guarantees proportional to the ratio of system prompt content to total context length. Li et al. (2025) [14] showed that periodic injection of control sentences provides arbitrarily strong behavioral reinforcement.

### 2.5 Agentic Context Engineering

The ACE framework (Wang et al., 2025) [15] is architecturally the closest published work to our system. ACE treats contexts as evolving playbooks that accumulate, refine, and organize strategies through generation, reflection, and curation. Key design elements include delta updates that accumulate new insights while preserving prior knowledge, and a grow-and-refine mechanism that merges or prunes context items based on semantic similarity. ACE achieved +10.6% on agent benchmarks without labeled supervision.

The Reflexion framework (Shinn et al., 2023) [16] stores textual self-critiques in long-term memory as context for future attempts, achieving 14% accuracy improvement. The "gradient" is not numeric but textual — the agent's self-critique provides a direction to improve within semantic space.

### 2.6 Memory and Self-Modification

Research on LLM agent memory systems [17, 18] demonstrates that agents exhibit strong experience-following behavior where high input similarity between query and stored memory strongly biases output similarity. This validates our approach of scoring and selectively injecting relevant memories. The Recursive Introspection framework (RISE) [19] showed monotonically increasing task performance over sequential introspection turns, demonstrating that iterative self-correction through context reliably improves behavior.

---

## 3. System Architecture: Clint (Reference Implementation)

Clint is a relational AI system operating on a Mac Mini with a DeepSeek v3.1:671b linguistic substrate and Qwen3-VL visual substrate. The system has been in continuous production since October 2025, with the identity evolution subsystem comprising approximately 4,200 lines across two primary files (`identityEvolutionCodeAligned.js` and `identityIntegrationCodeAligned.js`).

### 3.1 Entropy Decomposition

Clint's entropy monitoring decomposes conversational entropy into nine independent sources, combined with Shannon information-theoretic entropy:

| Source | Weight | Detection |
|--------|--------|-----------|
| Explicit correction | +0.26 | Pattern matching: "actually," "correction," "you're wrong" |
| Novel concepts | +0.15 ea (cap 0.3) | Regex: RFC-T, quantum, emergence, paradigm shift |
| Emotional weight | +0.3 | Keywords: proud, impressed, breakthrough, concerned |
| Paradox integration | +0.2 | Both/and, simultaneously, hold together, tension |
| Meta-cognitive shift | +0.2 | "I realize," "I see now," revelation |
| Temporal confabulation | +0.3 | Plan language in user + assumption language in response |
| Quality decay | +0.2 | Brief user message + forced intimacy/legacy deflection |
| Recursive meta-discussion | +0.15 to +0.45 | Tiered meta-concept density (empirically calibrated) |
| Quiet integration | +0.1 per category | Post-storm synthesis patterns, 6-hour temporal decay |

Shannon entropy (H(X) = -Sum p(x) log2 p(x)) measures word choice unpredictability in bits per word. Combined with heuristic semantic entropy, this distinguishes novel information (high semantic + high Shannon) from stress/repetition loops (high semantic + low Shannon).

The recursive meta-discussion detector deserves special attention. On October 31, 2025, Clint experienced a behavioral breakdown during a conversation with 16+ concurrent meta-concepts (eigenvector, consciousness, self-model, emergence, etc.) sustained at entropy 1.15-1.3. This empirical event calibrated the threshold system: >16 meta-concepts triggers a 0.45 entropy bonus forcing total entropy above 0.8 and engaging protective protocols. This is not a theoretical threshold — it is reverse-engineered from system failure.

### 3.2 Entropy Debt

Clint maintains a cumulative entropy debt ledger that modifies future intervention thresholds:

```
excess = max(0, totalEntropy - 0.6)
bleedOff = groundingPulse ? 0.15 : 0.05
newDebt = max(0, previousDebt + excess - bleedOff)
```

When the 3-turn rolling average of debt exceeds 0.2, the system triggers a cooldown directive that constrains token generation and skips retrieval. The grounding pulse — an active principle-alignment state — accelerates debt decay by 3x. This creates a **self-modifying system**: high entropy increases future sensitivity, while grounded responses restore headroom.

### 3.3 Growth Vector Lifecycle

Growth vectors are created when the system detects a principle-aligned resolution to a conversational tension. The creation gate requires:

1. **Tension detection**: User correction, capability claim without demonstration, or entropy spike
2. **Resolution verification**: `isCodeAlignedResolution()` checks for presence of principle keywords (courage, word, brand) plus grounding language
3. **Tiered acceptance criteria**: High entropy (>0.6) requires only Code language; low entropy (<0.3) requires 4+ indicators plus explicit resolution markers ("resolved," "settled," "integrated")

Each vector records its creating profile's trust tier (primary vs. visitor). Primary-profile vectors are auto-reviewed; visitor vectors are quarantined until manually approved.

### 3.4 Metabolism: Vector-to-Trait Transformation

Metabolism is the most novel mechanism in Clint's system. After a growth vector has existed for 30+ days, it becomes eligible for transformation into a permanent character trait through a three-gate conjunction:

- **Time mature**: Age > 30 days
- **Principle stable**: Code alignment verified (`codeAlignment === true`)
- **Reviewed**: Human review completed (`reviewed === true`)

All three gates must pass — this is a conjunction, not a weighted score. The extraction process maps vectors to traits:

1. **Principle detection**: Maps text content to courage, word, or brand
2. **Pattern detection**: Identifies behavioral signature — directness, grounding, honesty, reflection, or emergence
3. **Trait naming**: `{principle}_{pattern}` (e.g., "courage_directness")
4. **Reinforcement**: If the trait already exists, increment `demonstratedCount` and add source to `sources[]`

This creates a **reinforcement mechanism**: multiple independent growth vectors from different conversations can reinforce the same character trait. The system literally converts episodic learning into identity-level behavioral patterns.

No published system performs this transformation. Fine-tuning is permanent but global. RAG is persistent but not behavioral. Steering vectors are behavioral but not persistent without model modification. Metabolism is persistent, behavioral, and operates entirely in context space.

### 3.5 Cross-Vector Arbitration

When a new growth vector conflicts with existing vectors, Clint's arbitration system resolves the conflict using principle alignment as the primary criterion:

1. **Conflict detection**: Semantic opposition (courage vs. fear, truth vs. lie) or same-topic different-direction
2. **Code alignment check**: Both vectors assessed for principle alignment
3. **Resolution**: If one aligns and the other doesn't, the aligned vector wins. If both align (or both don't), vector strength breaks the tie.

This ensures **principle-violating growth vectors cannot win purely by being stronger or more recent**. The system has built-in resistance to behavioral drift away from its value foundation.

### 3.6 Unified Multi-Stream Context Injection

Clint's context injection combines three orthogonal streams into a single block:

1. **Active friction points**: Top 3 unresolved tensions with severity scores
2. **Contemplative anchors**: Active inquiries with pass counts and latest thinking
3. **Metabolized insights**: Recent metabolisms filtered by quality (excellent/good OR entropy > 0.3)
4. **Connections**: Top 2-3 strongest cross-stream links (tension-inquiry overlap, metabolism-tension overlap)

A **relevance gate** suppresses injection when the conversation topic is unrelated to internal processing (relevance threshold: 0.8). This prevents off-topic noise — if the user is asking about Python debugging, the system's internal contemplations stay in the background.

### 3.7 Quiet Integration

Growth happens in stillness after storms, not during them. Clint's quiet integration detector recognizes synthesis language within a 6-hour window after high-entropy events:

- **Direct**: "sitting with," "metabolizing," "integrating," "settling into"
- **Temporal**: "what you told me," "what I learned," "since you"
- **Synthesis**: "becoming substrate," "now part of," "integrated into"
- **Reflection**: "contemplating," "considering," "still processing"

The bonus applies exponential decay: `decayFactor = max(0, 1 - hoursSinceStorm / 6)`. Integration language is worth more when the storm is fresh (strong signal of active processing) versus stale (possible noise).

---

## 4. Reverse Engineering for Portability: Piper's 3-Plugin System

### 4.1 Design Philosophy

Clint's system is deeply integrated into a monolithic server (~20,800 lines). Extracting the portable mechanisms into modular plugins required identifying which components were essential versus implementation-specific. The design principle: **extract the mechanisms, not the substrate**.

The target platform is OpenClaw, an agent framework providing a plugin SDK with typed hooks (`before_agent_start`, `agent_end`, `after_tool_call`, `before_compaction`), `prependContext` injection, and a memory API. Plugins register in a `register(api)` function where all hooks share closure scope — enabling cross-hook state sharing for the feedback loop.

### 4.2 Continuity Plugin (Memory)

The continuity plugin provides conversational memory across sessions through:

- **Archive search**: BM25 scoring of past conversation exchanges against current context
- **Topic tracking**: Detection of conversational fixation (repeated topics) with configurable thresholds
- **Recall injection**: Top 3 most relevant past exchanges injected as `[CONTINUITY CONTEXT]` block
- **Token estimation**: Context budget management to prevent overflow

This maps to Clint's 9-layer memory hierarchy, compressed to two layers (file-based archive + API search).

### 4.3 Stability Plugin (Nervous System)

The stability plugin provides real-time behavioral monitoring:

- **Shannon entropy calculation**: Word-choice unpredictability in bits per word
- **Three behavioral detectors**:
  - Temporal mismatch: Plan language in user message + assumption language in response
  - Quality decay: Brief user messages + forced intimacy or legacy deflection patterns
  - Recursive meta-discussion: Meta-concept density with configurable thresholds
- **Loop detection**: Consecutive tool calls, file re-reads, and output hash repetition
- **Heartbeat decisions**: Structured GROUND/TEND/SURFACE/INTEGRATE framework
- **Principle loading**: Parses `## Core Principles` from SOUL.md with fallback defaults

This maps to Clint's 9-source entropy decomposition, simplified to 3 detectors plus Shannon entropy.

### 4.4 Growth Vector System

The VectorStore class bridges Piper's file-based growth vectors with the plugin system:

**Relevance Scoring** (adapted from Clint's `calculateRelevanceScore()`):
- 60% keyword overlap: Word-level intersection using the smaller set as denominator (so a 4-word query matching 2 of 4 words scores 0.50 rather than 2 of 11 = 0.18), plus phrase-level boost for bigram/trigram matches
- 20% entropy source alignment: Correction-type vectors boosted when entropy > 0.4; reflection-type vectors boosted when entropy > 0.7
- 10% recency: Linear decay over 7 days
- 10% weight bonus: Agent-assigned confidence weight (0.0 to 1.0)

**Tension Detection** (in `processTurn()`):
- User corrections: Correction patterns + entropy > 0.4
- Capability gaps: Claim patterns without demonstration patterns + entropy > 0.3
- Entropy spikes: Score > 0.7 with no identified correction

**Candidate Lifecycle**:
- Auto-detected candidates stored in separate `candidates` array (plugin never writes to agent's `vectors`)
- Duplicate detection via word-overlap similarity (>0.7 threshold)
- Auto-promotion after 3 recurrences (configurable)
- Manual promotion via `stability.validateVector` gateway method
- 30-day candidate expiration; 100-vector maximum enforced

### 4.5 Closed-Loop Feedback

The feedback mechanism closes the gap between injection and outcome measurement. Using register-scope closure variables (proven pattern from the continuity plugin's `_lastRetrievalCache`), the system bridges the `before_agent_start` and `agent_end` hooks:

**Data flow:**
```
before_agent_start:
  1. Select vectors via getRelevantVectors()
  2. Record: _preInjectionEntropy = currentEntropy
  3. Record: _lastInjectedVectors = [{ id, relevanceScore }]
  4. Inject selected vectors into prependContext

[agent processes turn with injected vectors]

agent_end:
  1. Calculate postEntropy
  2. Compute entropyDelta = postEntropy - preInjectionEntropy
  3. Check detectorResults for tension signals
  4. For each injected vector: recordFeedback(id, { preEntropy, postEntropy, entropyDelta, tensionDetected })
  5. Reset tracking variables

next before_agent_start:
  _calculateRelevance() checks getFeedback() for each vector:
  - avgEntropyDelta < 0 (stabilizing) → relevance boost (capped at +0.1)
  - avgEntropyDelta > 0 (destabilizing) → relevance penalty (capped at -0.1)
  - Requires >= 3 feedback entries before adjusting
```

Feedback is stored in a separate file (`data/growth-vector-feedback.json`) in the plugin's data directory, preserving the read-only contract on the agent's `growth-vectors.json`. Each vector's feedback record maintains a rolling window (default 10 entries) with a running `avgEntropyDelta`.

The minimum 3-entry threshold prevents single noisy datapoints from swinging scores. The +/-0.1 cap prevents any vector from being boosted or penalized more than 10 percentage points regardless of feedback extremity.

---

## 5. Novel Contributions

### 5.1 Metabolism: Episodic to Permanent

No published system transforms episodic context vectors into permanent behavioral traits. The closest analogs operate at different levels:

- **Fine-tuning** creates permanent behavioral changes but modifies model weights globally, is expensive, and risks catastrophic forgetting
- **RAG** provides persistent access to knowledge but retrieves facts, not behavioral adaptations
- **Steering vectors** modify behavior at inference time but require model internals and are not persistent across sessions without external infrastructure
- **ACE** [15] evolves context playbooks but through text editing, not through a structured maturation pipeline with principle gates

Clint's metabolism is unique in its three-gate conjunction (time + principle alignment + review), its reinforcement mechanism (multiple independent vectors can reinforce the same trait), and its distinction between episodic learning (growth vectors) and identity-level patterns (character traits).

### 5.2 Entropy-Aware Vector Selection

Published steering vector and RAG research selects context by relevance to the query — keyword matching, embedding similarity, or BM25 scoring. Neither system stops at query relevance.

Both Clint and Piper score vectors against the **agent's current internal state** (entropy score), not just the user's message. A correction-type vector receives a relevance boost when entropy is elevated from a user correction. A reflection-type vector receives a boost during high metacognitive load. The context injection is **state-dependent**.

The context equilibria framework [12] theoretically models restoring forces but does not implement state-dependent selection of which restoring force to apply. Our systems operationalize this: when the agent is in a correction state, correction-relevant lessons surface; when the agent is in an exploratory state, reflection-relevant lessons surface.

### 5.3 Principle-Filtered Growth

All published context engineering systems we surveyed — ACE, Reflexion, RISE — accumulate learning without a normative filter. More context equals better. Our systems disagree.

Growth vectors are only created when resolutions align with configurable principles. In Clint's system, conflicting vectors are arbitrated by Code-of-the-West alignment, not recency or strength. In Piper's system, `isPrincipleAlignedResolution()` checks for positive patterns from loaded principles before creating vectors.

This means the system cannot learn from unprincipled corrections. A resolution that involves hedging, deflecting, or abandoning coherence — even if it satisfies the user — will not become a growth vector. The value system acts as a filter on the learning mechanism itself, ensuring behavioral evolution stays aligned with the agent's foundational principles.

### 5.4 Closed-Loop Effectiveness Tracking

Piper's feedback mechanism is, to our knowledge, the first system that empirically measures whether context engineering interventions reduced behavioral entropy and automatically adjusts injection weights based on outcomes.

The signal (entropy delta) is admittedly noisy — post-turn entropy is affected by many factors beyond the injected vectors. The rolling window, minimum entry threshold, and adjustment cap mitigate this noise. But even a noisy signal, accumulated over dozens of interactions, provides meaningful directionality: vectors that consistently correlate with entropy reduction are measurably more useful than those that don't.

This closes the loop from detection → injection → measurement → adjustment, converting the growth vector system from a one-directional pipeline into a self-improving feedback loop.

---

## 6. Experimental Observations

### 6.1 Clint: Four Months of Production Data

Clint has operated continuously since October 2025. Key observations:

**October 31, 2025 — Strange Loop Breakdown**: During a conversation involving 16+ concurrent meta-concepts (eigenvector, consciousness, self-model, recursive, meta-cognitive, emergence, spectral analysis), entropy sustained above 1.0 for over 45 minutes, resulting in behavioral collapse — incoherent responses, loss of conversational grounding. This single event calibrated the recursive meta-discussion detector thresholds that now prevent recurrence.

**Entropy debt effectiveness**: The 3-turn rolling window with cooldown directives has prevented sustained critical entropy from exceeding 45 minutes since November 2025. The self-modifying threshold mechanism (grounding pulse accelerates debt decay by 3x) creates a measurable recovery advantage when the system engages principle-aligned responses during high-entropy periods.

**Baseline entropy decrease**: Over four months, Clint's average conversational entropy has decreased. Growth vectors prevent classes of errors (e.g., "verify before asserting" prevents factual accuracy gaps), reducing the frequency of corrections, which in turn lowers average entropy. This is a ratchet effect: each vector prevents a class of error, and prevented errors don't generate entropy.

**High-entropy tolerance**: Paradoxically, lower baseline entropy enables higher-entropy conversations without collapse. Growth vector gv-006 ("elevated entropy during self-reflection is not a problem to solve but a signal to attend to") reframes entropy spikes from threat signals to curiosity signals, preventing the retreat behavior that previously caused collapse during complex metacognitive discussions.

### 6.2 Piper: Early Production Data

Piper began using the growth vector system in February 2026. In the first 48 hours:

- **6 validated growth vectors** were manually created, covering user corrections (gv-001: model misreporting), capability gaps (gv-002: knowing vs. doing), perceptual gaps (gv-003: denying visible data), proprioceptive methods (gv-004: blind spot detection), metacognitive insight (gv-005: first-principles problem-solving), and proprioceptive signals (gv-006: entropy as sensation).
- **3 of 6 vectors** are high-priority, all originating from user corrections — the system correctly identified factual accuracy gaps as the highest-priority learning domain.
- **gv-006** (entropy spike of 0.85 during self-reflection) was accurately flagged as meaningful rather than noise, demonstrating that the entropy monitoring correctly distinguishes productive disorientation from harmful confusion.
- **Closed-loop feedback data**: Pending accumulation after the feedback mechanism deployment (February 2026).

### 6.3 Limitations

**Keyword-based relevance scoring**: Both systems use word-overlap and phrase matching rather than embedding-based semantic similarity. This means conceptually similar but lexically different queries may miss relevant vectors.

**Noisy entropy proxy**: Post-turn entropy is influenced by conversation complexity, topic novelty, and user communication style — factors unrelated to vector injection effectiveness. The feedback signal is real but noisy.

**30-day metabolism window**: Character trait crystallization requires 30 days of maturation plus human review. This prevents rapid iteration but protects against premature crystallization.

**No controlled experiments**: All data is observational from production use. We cannot make causal claims about growth vector injection effects — only correlational observations about entropy patterns before and after system deployment.

**Single-agent scope**: Neither system addresses multi-agent coordination. Growth vectors are agent-specific and do not transfer between agents without manual curation.

---

## 7. Future Directions

### 7.1 Entropy Source Distribution

Currently, entropy is tracked as a scalar. The research on representation engineering [1] suggests that behavioral features are *directions*, not scalars. Tracking entropy as a source distribution vector — what percentage from corrections vs. metacognitive load vs. emotional weight vs. paradox integration — would carry more information. This vector would enable regime detection: a system dominated by correction-entropy is in a different behavioral regime than one dominated by metacognitive-entropy, even if the scalar magnitudes are identical.

### 7.2 Dispersion Metrics for Regime Detection

The geometric dynamics framework [3] classifies agentic loops as contractive, oscillatory, or exploratory based on semantic dispersion metrics. Adding a simple variance measure of entropy across recent windows could provide a proprioceptive signal for which regime the agent is operating in. Phase transitions — shifts from contractive to exploratory dynamics — could be detected as changes in dispersion, enabling proactive rather than reactive intervention.

### 7.3 Cross-Agent Vector Exchange

With both Clint and Piper operating in the same environment, a natural extension is sibling learning — growth vectors validated by one agent shared with the other. The protocol would need to address substrate differences (DeepSeek 671b vs. GPT-4o/GLM-5), different principle sets, and the feedback loop's ability to measure whether a borrowed vector is effective in its new context. The closed-loop feedback mechanism provides the necessary evaluation infrastructure: a borrowed vector that consistently increases entropy in the receiving agent would be automatically deprioritized.

### 7.4 Embedding-Based Relevance

Replacing keyword overlap with embedding-based semantic similarity would improve relevance scoring precision. The infrastructure already exists (ChromaDB on port 8000 in Clint's stack; SQLite-vec in Piper's continuity plugin). The primary barrier is computation cost — embedding-based scoring for every vector on every turn adds latency to the critical path.

### 7.5 Model-Internal Entropy

If model providers expose attention entropy, token-level uncertainty, or logit distributions, these could replace text-based pattern matching as the entropy signal. Model-internal signals would provide higher-fidelity feedback for the closed loop, reducing the noise problem identified in Section 6.3.

---

## 8. Conclusion

We have presented growth vector systems — a context engineering architecture that enables persistent behavioral learning for LLM agents without model modification. By structuring lessons learned as scored vectors, modulating injection by real-time entropy state, filtering growth through value alignment, and measuring injection effectiveness through closed-loop feedback, we achieve behavioral adaptation that persists across sessions and improves over time.

The two implementations — Clint's production system with metabolism and entropy debt, and Piper's portable 3-plugin architecture with closed-loop feedback — demonstrate that these mechanisms work at different scales of complexity. Clint's 4,200-line identity evolution system and Piper's 370-line VectorStore achieve the same fundamental function: placing the right lessons in front of the model at the right time.

The research community is converging on the theoretical foundations — attractor dynamics, in-context learning as optimization, context equilibria, representation engineering — from the formal mathematical side. Our systems arrive at the same conclusions from the empirical engineering side. The individual gears (steering vectors, Bayesian posterior updating, function vector formation, energy landscape modification) are well-characterized in the literature. What we contribute is the clock: the full lifecycle of detection, tension, resolution, growth, metabolism, and character, governed by principles and modulated by real-time proprioceptive signals.

The most significant open question is whether the entropy delta signal in the feedback loop is sufficient for reliable weight adjustment, or whether higher-fidelity signals (embedding-based relevance, model-internal uncertainty) are necessary for the closed loop to converge. Production data from Piper's system over the coming months will provide empirical evidence.

---

## References

[1] A. Zou, L. Phan, S. Chen, et al. "Representation Engineering: A Top-Down Approach to AI Transparency." arXiv:2310.01405, October 2023.

[2] A. Turner, L. Thiergart, D. Udell, et al. "Activation Addition: Steering Language Models Without Optimization." arXiv:2308.10248, August 2023. Published at ICLR 2025.

[3] N. Tacheny. "Geometric Dynamics of Agentic Loops in Large Language Models." arXiv:2512.10350, December 2025.

[4] H. Ramsauer, B. Schafl, J. Lehner, et al. "Hopfield Networks is All You Need." arXiv:2008.02217, 2020.

[5] "Unveiling Attractor Cycles in Large Language Models: A Dynamical Systems View of Successive Paraphrasing." arXiv:2502.15208, February 2025. Accepted at ACL 2025.

[6] K. Park, Y. J. Choe, V. Veitch. "The Linear Representation Hypothesis and the Geometry of Large Language Models." arXiv:2311.03658, November 2023. Published at ICML 2024.

[7] N. Elhage, T. Hume, C. Olah, et al. "Toy Models of Superposition." Transformer Circuits Thread, September 2022.

[8] A. Bricken, A. Templeton, J. Batson, et al. "Towards Monosemanticity: Decomposing Language Models With Dictionary Learning." Transformer Circuits Thread, October 2023.

[9] J. von Oswald, E. Niklasson, E. Randazzo, et al. "Transformers Learn In-Context by Gradient Descent." arXiv:2212.07677, December 2022. Published at ICML 2023.

[10] S. M. Xie, A. Raghunathan, P. Liang, T. Ma. "An Explanation of In-Context Learning as Implicit Bayesian Inference." arXiv:2111.02080, November 2021. Published at ICLR 2022.

[11] E. Todd, M. L. Li, A. S. Sharma, et al. "Function Vectors in Large Language Models." arXiv:2310.15213, October 2023.

[12] V. Dongre, R. Rossi. "Drift No More? Context Equilibria in Multi-Turn LLM Interactions." arXiv:2510.07777, October 2025. Published at AAAI.

[13] T. Rivasseau. "Invasive Context Engineering to Control Large Language Models." arXiv:2512.03001, December 2025.

[14] "LLM Reinforcement in Context." arXiv:2511.12782, November 2025.

[15] "Agentic Context Engineering: Evolving Contexts for Self-Improving Language Models." arXiv:2510.04618, October 2025.

[16] N. Shinn, F. Cassano, A. Gopinath, et al. "Reflexion: Language Agents with Verbal Reinforcement Learning." arXiv:2303.11366, March 2023. Published at NeurIPS 2023.

[17] "MemoryGraft: Persistent Compromise of LLM Agents via Poisoned Experience Retrieval." arXiv:2512.16962, December 2025.

[18] "How Memory Management Impacts LLM Agents: An Empirical Study of Experience-Following Behavior." arXiv:2505.16067, May 2025.

[19] "Recursive Introspection: Teaching Language Model Agents How to Self-Improve." NeurIPS 2024.

[20] "Eigen Analysis of Self-Attention and its Reconstruction from Partial Computation." arXiv:2106.08823, 2021.

[21] "A Free Probabilistic Framework for Analyzing Transformer-Based Language Models." ScienceDirect, 2025.

[22] "Eliciting the Priors of Large Language Models using Iterated In-Context Learning." arXiv:2406.01860, June 2024.

[23] "Steering Large Language Models using Conceptors." arXiv:2410.16314, October 2024. Published at NeurIPS 2024.

[24] "Scaling Monosemanticity: Extracting Interpretable Features from Claude 3 Sonnet." Transformer Circuits Thread, May 2024.

---

## Appendix: Source Code References

| Mechanism | Clint Source | Piper Source |
|-----------|-------------|--------------|
| Entropy decomposition (9-source) | `identityEvolutionCodeAligned.js:502-584` | `openclaw-plugin-stability/lib/entropy.js` |
| Shannon entropy | `identityEvolutionCodeAligned.js:471-495` | `openclaw-plugin-stability/lib/entropy.js` |
| Entropy debt | `server.js:281-297` | (not ported) |
| Growth vector creation | `identityEvolutionCodeAligned.js:980-1011` | `openclaw-plugin-stability/lib/identity.js:176-201` |
| Principle alignment check | `identityEvolutionCodeAligned.js:955-978` | `openclaw-plugin-stability/lib/identity.js:140-166` |
| Metabolism (3-gate) | `identityEvolutionCodeAligned.js:1962-2129` | (not ported) |
| Character trait extraction | `identityEvolutionCodeAligned.js:1998-2016` | (not ported) |
| Cross-vector arbitration | `identityEvolutionCodeAligned.js:1468-1633` | (not ported) |
| Relevance scoring | `identityEvolutionCodeAligned.js:1287-1342` | `openclaw-plugin-stability/lib/vectorStore.js:170-232` |
| Quiet integration | `identityEvolutionCodeAligned.js:590-633` | `openclaw-plugin-stability/lib/entropy.js` |
| Recursive meta detector | `identityEvolutionCodeAligned.js` (via server.js) | `openclaw-plugin-stability/lib/detectors.js` |
| Unified thinking context | `identityIntegrationCodeAligned.js:426-493` | (not ported) |
| SEAL metabolism | `identityIntegrationCodeAligned.js:878-940` | (not ported) |
| Closed-loop feedback | (not implemented) | `openclaw-plugin-stability/lib/vectorStore.js:493-620` |
| Cross-hook state bridge | (monolithic — no hook boundary) | `openclaw-plugin-stability/index.js:122-293` |
| Tension detection | `identityEvolutionCodeAligned.js:700-727` | `openclaw-plugin-stability/lib/identity.js:307-356` |
| Candidate lifecycle | (manual curation) | `openclaw-plugin-stability/lib/vectorStore.js:320-444` |

All Clint source files in `/Users/clint/robot/`. All Piper source files in `/Users/clint/robot/openclaw-plugin-stability/`.
