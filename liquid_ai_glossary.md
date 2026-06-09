# Liquid AI Glossary — LLM-Parseable Reference

## How to read this file

Every entry follows the same structure so an LLM can chunk and traverse it reliably:

```
## TERM_ID: Canonical Name
- aliases: [other names this term is called]
- category: [foundations | ai-core | efficiency | liquid-tech | liquid-products | market | gtm | people | numbers | taglines]
- definition: one-sentence definition
- detail: 1–3 sentence elaboration
- connects_to:
  - TERM_ID: relationship phrase
  - TERM_ID: relationship phrase
```

- **TERM_ID** is a stable slug (snake_case). Every cross-reference uses the slug, so an LLM can follow connections without name ambiguity.
- **connects_to** is directional but the file is dense enough that reverse links are always reachable within 1–2 hops.
- **category** tags let an LLM filter (e.g. "give me only liquid-products" or "only gtm terms").

---

# FOUNDATIONS

## cpu: Central Processing Unit
- aliases: [CPU]
- category: foundations
- definition: A general-purpose processor that executes a few instructions at a time with high flexibility.
- detail: The "main brain" of any computer; handles sequential work well but is not optimized for the massively parallel math that neural networks need.
- connects_to:
  - gpu: contrasted with; CPU is flexible-but-serial, GPU is rigid-but-parallel
  - npu: complemented by; CPU + NPU is the typical on-device AI pairing
  - on_device: enables; on-device AI runs primarily on CPU + NPU
  - inference: hosts; CPU-only inference is a key Liquid claim ("200% faster decode on CPU")

## gpu: Graphics Processing Unit
- aliases: [GPU]
- category: foundations
- definition: A processor with thousands of small cores that perform the same simple math on huge amounts of data in parallel.
- detail: Originally built for graphics; now the dominant chip for AI training and large-scale cloud inference. NVIDIA dominates this market.
- connects_to:
  - cpu: complements; CPU orchestrates, GPU computes
  - npu: contrasted with; NPUs are purpose-built for inference, GPUs are general parallel compute
  - training: dominates; almost all model training happens on GPU clusters
  - cloud: lives in; the cloud's economic model is GPU-hours
  - amd_partnership: alternative to; AMD GPUs are Liquid's anti-NVIDIA hardware path

## npu: Neural Processing Unit
- aliases: [NPU]
- category: foundations
- definition: A chip purpose-built for the matrix math of neural network inference, designed for low-power devices.
- detail: Lives inside modern phones, laptops, cars, and wearables. Optimized for energy efficiency, not raw throughput.
- connects_to:
  - cpu: complements; CPU + NPU pairing is standard for on-device AI
  - gpu: differs from; GPUs train, NPUs infer
  - on_device: enables; NPUs are the silicon premise of the on-device thesis
  - hardware_aware_design: targets; LFM2's block design maps onto NPU memory patterns

## latency
- aliases: [response time, time-to-first-token, TTFT]
- category: foundations
- definition: How long a single request takes from input to first response, measured in milliseconds.
- detail: Distinct from throughput. Cloud AI has unavoidable network-roundtrip latency; on-device AI does not.
- connects_to:
  - throughput: contrasted with; throughput measures volume per second, latency measures per-request delay
  - on_device: minimized by; eliminating the cloud roundtrip cuts latency to milliseconds
  - value_prop_latency: drives; "millisecond TTFT" is Liquid's #1 value prop
  - automotive_vertical: critical for; safety features cannot tolerate cloud latency
  - decode_speed: component of; tok/s decode is a latency metric

## throughput
- aliases: [requests per second]
- category: foundations
- definition: The number of requests or tokens a system can process per unit time.
- detail: Higher throughput means lower per-unit cost at scale; it's the efficiency metric for the cloud-economics conversation.
- connects_to:
  - latency: contrasted with
  - inference: measured at; inference throughput drives the cloud cost equation
  - cost_equation: drives; throughput × hardware utilization = unit economics

## cloud
- aliases: [the cloud, data center AI]
- category: foundations
- definition: Computing performed on remote servers in data centers, accessed over a network.
- detail: The default home of frontier LLMs (GPT, Claude, Gemini). Has three persistent costs: network latency, per-request compute bill, and data leaving the user's device.
- connects_to:
  - on_device: contrasted with; the central architectural choice in the Liquid story
  - hybrid_future: half of; Liquid believes in cloud + on-device, not anti-cloud
  - cloud_hangover: critiqued in; the "data-center capex unsustainable" debate
  - frontier_labs: live in; OpenAI / Anthropic / Google operate cloud-first

## on_device
- aliases: [edge AI, on-device AI, local AI]
- category: foundations
- definition: AI inference performed on the end-user's hardware (phone, laptop, car, wearable, embedded chip) instead of in the cloud.
- detail: The central market position of Liquid AI. Flips four things at once vs. cloud: latency, cost, privacy, reliability.
- connects_to:
  - cloud: contrasted with
  - cpu: runs on
  - npu: runs on
  - value_prop_latency: enables
  - value_prop_privacy: enables
  - value_prop_reliability: enables
  - value_prop_memory: requires; on-device only works if the model is small enough
  - leap: deployment SDK for; Liquid's tool for shipping on-device
  - apollo: consumer demo of
  - slm: requires; on-device AI needs small models

## parameters
- aliases: [weights, model size]
- category: foundations
- definition: The tunable numbers inside a neural network whose values are learned during training.
- detail: Model size is conventionally quoted in parameters (350M, 1.2B, 8.3B). Bytes-on-disk = parameters × bits-per-parameter ÷ 8.
- connects_to:
  - quantization: reduces precision of
  - moe: distinguishes total from active
  - neural_network: defines; parameters ARE the network
  - lfm2_family: catalogued by; Liquid's product line is indexed by parameter count

---

# AI CORE CONCEPTS

## machine_learning
- aliases: [ML]
- category: ai-core
- definition: A class of systems that learn patterns from examples instead of being explicitly programmed with rules.
- detail: The umbrella under which neural networks, transformers, and foundation models all sit.
- connects_to:
  - neural_network: instance of
  - training: how ML systems are built
  - inference: how ML systems are used

## neural_network
- aliases: [NN, neural net]
- category: ai-core
- definition: A stack of layers of simple math units ("neurons") connected by weighted links, trained to map inputs to outputs.
- detail: Foundation models are very large neural networks. Liquid Neural Networks are a specific, unusual variant.
- connects_to:
  - parameters: composed of
  - training: shapes
  - inference: runs
  - transformer: a type of
  - lnn: a type of
  - convolution: an operation inside

## training
- aliases: []
- category: ai-core
- definition: The process of adjusting a model's parameters by showing it large amounts of data.
- detail: Expensive, performed once (or periodically), almost always on large GPU clusters in the cloud.
- connects_to:
  - inference: contrasted with; the central economic split in AI
  - gpu: runs on
  - cloud: lives in
  - fine_tuning: a smaller training pass
  - pretraining: the largest training pass

## inference
- aliases: [serving, prediction]
- category: ai-core
- definition: The process of running a trained model to produce an output for a new input.
- detail: Done millions of times per deployed model; aggregate inference cost dominates AI economics at scale.
- connects_to:
  - training: contrasted with
  - cost_equation: dominant term in
  - on_device: target location for Liquid's inference
  - cpu: can run on (Liquid's claim)
  - decode_speed: measured by
  - prefill_speed: measured by

## token
- aliases: []
- category: ai-core
- definition: A small chunk of text (roughly a word-piece or 3–4 characters) that language models read and produce as their atomic unit.
- detail: Models do not see letters; they see tokens. All speed and cost metrics ("tok/s," "cost per million tokens") are token-denominated.
- connects_to:
  - context_window: measured in tokens
  - decode_speed: produces tokens
  - prefill_speed: consumes tokens
  - autoregressive_generation: produces one at a time

## context_window
- aliases: [context length]
- category: ai-core
- definition: The maximum number of tokens a model can attend to at once.
- detail: LFM2 = 32K tokens; LFM2.5 = 128K. Larger windows = more memory of the conversation but more compute cost in transformers.
- connects_to:
  - token: measured in
  - attention: cost grows with the square of this
  - lfm2_family: 32K
  - lfm2_5: 128K

## autoregressive_generation
- aliases: [autoregressive, AR]
- category: ai-core
- definition: A generation method where the model produces one token at a time, each new token conditioned on all previous tokens.
- detail: How all chat-style LLMs work. The reason decode speed (tok/s) is the meaningful throughput metric.
- connects_to:
  - decode_speed: measured by
  - token: the atomic output unit
  - transformer: standard AR architecture

## decode_speed
- aliases: [decode, tokens per second, tok/s]
- category: ai-core
- definition: The rate at which a model generates output tokens, measured in tokens per second.
- detail: Headline Liquid metric: LFM2-1.2B-Thinking hits 235 tok/s on CPU; LFM2-24B-A2B hits 112 tok/s on AMD CPU, 293 tok/s on H100.
- connects_to:
  - prefill_speed: paired with
  - latency: a component of
  - inference: measures
  - performance_claims: source of headline numbers

## prefill_speed
- aliases: [prefill]
- category: ai-core
- definition: The rate at which a model processes the input prompt before beginning to generate.
- detail: The other half of the speed story alongside decode. Liquid quotes 200% faster prefill vs. Qwen3/Gemma 3 on CPU.
- connects_to:
  - decode_speed: paired with
  - latency: a component of (TTFT depends on prefill)
  - performance_claims: source of

## transformer
- aliases: []
- category: ai-core
- definition: A neural network architecture, introduced in 2017's "Attention Is All You Need," whose central mechanism is attention over all input tokens.
- detail: The dominant architecture for LLMs (GPT, Claude, Gemini, Llama, Qwen). Liquid AI is explicitly NOT a transformer-derived architecture, which is the foundation of its category-creation pitch.
- connects_to:
  - attention: defined by
  - lfm: contrasted with; LFMs are non-transformer
  - gqa: efficiency variant inside
  - category_creation: enables; "non-transformer" is the category Liquid creates

## attention
- aliases: [self-attention]
- category: ai-core
- definition: A mechanism by which every token in a sequence looks at every other token to compute weighted relevance, producing context-aware representations.
- detail: The reason transformers are powerful (global context) and expensive (cost grows with the square of context length, memory grows linearly).
- connects_to:
  - transformer: core mechanism of
  - mha: original form
  - mqa: efficiency variant
  - gqa: practical compromise variant
  - kv_cache: stores state for
  - lfm2_hybrid_arch: minimized in; only ~20% of LFM2 blocks use attention

## mha: Multi-Head Attention
- aliases: [MHA]
- category: ai-core
- definition: The original attention recipe, in which each "head" maintains its own keys, queries, and values, and multiple heads run in parallel.
- detail: Maximum expressiveness but heaviest memory bandwidth. Largely replaced by GQA in modern LLMs.
- connects_to:
  - attention: instance of
  - mqa: simplified to
  - gqa: generalized to

## mqa: Multi-Query Attention
- aliases: [MQA]
- category: ai-core
- definition: An attention variant where all heads share a single set of keys and values.
- detail: Fast and memory-light but loses quality. The aggressive endpoint that GQA sits between.
- connects_to:
  - attention: instance of
  - mha: simplification of
  - gqa: generalized to

## gqa: Grouped-Query Attention
- aliases: [GQA]
- category: ai-core
- definition: An attention variant where query heads are grouped, and each group shares a single set of keys and values.
- detail: The dominant practical attention design in modern LLMs (Llama, Qwen, Gemma). LFM2 uses GQA in its 6 attention blocks.
- connects_to:
  - mha: middle ground from
  - mqa: middle ground from
  - attention: variant of
  - kv_cache: shrinks
  - lfm2_hybrid_arch: used in the 6 attention blocks

## kv_cache: Key-Value Cache
- aliases: [KV cache]
- category: ai-core
- definition: The stored per-token "keys" and "values" that attention reuses on every subsequent token generation step.
- detail: A major memory cost in transformer inference. GQA's purpose is to shrink it.
- connects_to:
  - attention: state for
  - gqa: shrinks
  - inference: memory bottleneck during

## convolution
- aliases: [conv, short convolution]
- category: ai-core
- definition: A neural operation in which a small "window" slides across the input and applies the same learned transformation at each position.
- detail: Local rather than global, cheap to compute, and hardware-friendly. The workhorse of LFM2 (10 of 16 blocks).
- connects_to:
  - attention: contrasted with; conv is local, attention is global
  - lfm2_hybrid_arch: dominant operation in
  - liv_convolution: Liquid's specific variant
  - hardware_aware_design: enabled by; convs map cleanly onto CPU/NPU

## multiplicative_gate
- aliases: [gating]
- category: ai-core
- definition: A small learned mechanism that selectively scales signals as they pass through a network block.
- detail: Lets the model dynamically decide which features matter. Used in LFM2's "double-gated" convolution blocks.
- connects_to:
  - lfm2_hybrid_arch: used in
  - convolution: applied around

## foundation_model
- aliases: [FM]
- category: ai-core
- definition: A large pretrained model that can be adapted to many downstream tasks; intentionally architecture-agnostic.
- detail: The "F" in LFM. Spans text, vision, audio, retrieval — not just language.
- connects_to:
  - llm: subset of
  - slm: subset of
  - lfm: instance of (non-transformer)
  - pretraining: produces

## llm: Large Language Model
- aliases: [LLM]
- category: ai-core
- definition: A foundation model focused on text/language, typically large (tens to hundreds of billions of parameters).
- detail: The category Liquid does NOT primarily compete in for benchmark supremacy.
- connects_to:
  - foundation_model: type of
  - slm: contrasted with
  - frontier_labs: build
  - transformer: standard architecture

## slm: Small Language Model
- aliases: [SLM]
- category: ai-core
- definition: A foundation model with relatively few parameters (typically <10B) designed for cheap inference, often on-device.
- detail: The category Liquid competes in. Backed by NVIDIA Research's own paper on agentic AI.
- connects_to:
  - llm: contrasted with
  - on_device: enables
  - slm_thesis: thesis around
  - nvidia_slm_paper: validated by
  - liquid_nanos: productization of

## pretraining
- aliases: []
- category: ai-core
- definition: The largest training pass, on broad data, that creates a general-purpose foundation model.
- detail: Expensive, done once. LFM2.5 was pretrained on 38T tokens.
- connects_to:
  - training: type of
  - fine_tuning: precedes
  - foundation_model: produces

## fine_tuning
- aliases: []
- category: ai-core
- definition: A smaller, task- or domain-targeted training pass that adapts a pretrained model to a specific use case.
- detail: The mechanism behind both Liquid Nanos and enterprise custom-model deals.
- connects_to:
  - pretraining: follows
  - liquid_nanos: built via
  - custom_enterprise_models: built via
  - star: paired with in Liquid's enterprise motion

## rlhf: Reinforcement Learning from Human Feedback
- aliases: [RLHF]
- category: ai-core
- definition: A training technique where humans rank model outputs and the model is nudged toward preferred outputs.
- detail: How polite, helpful chatbots are made. Standard across the industry.
- connects_to:
  - fine_tuning: form of
  - training: form of

## distillation
- aliases: [knowledge distillation]
- category: ai-core
- definition: A training method in which a small "student" model is trained to imitate a larger "teacher" model.
- detail: One standard way to compress big models; contrasts with Liquid's "efficiency by design" approach.
- connects_to:
  - quantization: alternative to
  - efficiency_by_design: contrasted with
  - slm: a way to produce

## rag: Retrieval-Augmented Generation
- aliases: [RAG]
- category: ai-core
- definition: A technique where the model is given relevant retrieved documents at query time and uses them to answer.
- detail: Lets small models punch above their weight on knowledge tasks. Productized as LFM2-1.2B-RAG.
- connects_to:
  - tool_calling: paired with
  - liquid_nanos: instance of
  - lfm2_colbert: retrieval backbone

## tool_calling
- aliases: [function calling]
- category: ai-core
- definition: A pattern in which an AI model decides to invoke external tools (search, calculator, APIs) to accomplish a task.
- detail: A core agentic primitive. Productized as LFM2-1.2B-Tool.
- connects_to:
  - rag: paired with
  - agentic_ai: building block of
  - liquid_nanos: instance of

## agentic_ai
- aliases: [agents, agentic systems]
- category: ai-core
- definition: AI systems composed of chained model calls and tool invocations that pursue a goal across multiple steps.
- detail: The forward-looking thesis behind small specialized models. Hasani frames it as "~100 agents per person."
- connects_to:
  - tool_calling: building block
  - rag: building block
  - slm_thesis: motivates
  - liquid_nanos: ideal substrate
  - nvidia_slm_paper: argues for SLMs in this context

---

# EFFICIENCY CONCEPTS

## cost_equation
- aliases: [unit economics of AI]
- category: efficiency
- definition: AI total cost = amortized training + (inference cost × number of inferences) + energy + hardware capex + network.
- detail: At scale, inference dominates. The single most important framing for the economic buyer.
- connects_to:
  - inference: dominant term
  - throughput: drives
  - on_device: changes; on-device shifts inference cost off the cloud bill
  - cloud_hangover: motivates concern
  - economic_buyer: cares about

## quantization
- aliases: []
- category: efficiency
- definition: Storing each model parameter in fewer bits (e.g., FP16 → INT4) to shrink memory footprint and speed inference, usually with mild quality loss.
- detail: The industry's default shrink-the-cloud-model approach. Liquid's "efficiency by design, not compression" tagline is a direct critique of it.
- connects_to:
  - efficiency_by_design: contrasted with
  - distillation: alternative to
  - parameters: changes precision of
  - on_device: enables (but Liquid's argument is to design lean instead)

## moe: Mixture-of-Experts
- aliases: [MoE]
- category: efficiency
- definition: An architecture where many "expert" sub-networks exist but a router selects only a small subset to use per token, so total parameters >> active parameters per token.
- detail: Lets a model have huge capacity but small per-token compute. LFM2-24B-A2B (24B total / 2B active) and LFM2.5-8B-A1B (8.3B total / 1.5B active) are MoE.
- connects_to:
  - parameters: distinguishes total vs. active
  - lfm2_24b_a2b: example
  - lfm2_5_8b_a1b: example
  - efficiency_by_design: variant of

## efficiency_by_design
- aliases: ["efficiency by design, not compression"]
- category: efficiency
- definition: Liquid AI's philosophy of architecting models for target constraints (hardware, latency, memory) from scratch, rather than scaling a cloud model down.
- detail: The most important single tagline. The intellectual foundation of every Liquid product.
- connects_to:
  - quantization: opposed to
  - distillation: opposed to
  - first_principles: synonym for
  - hardware_aware_design: expression of
  - star: tool that operationalizes
  - taglines: anchor of

## slm_thesis
- aliases: [small-is-better thesis]
- category: efficiency
- definition: The argument that small, task-specialized models are sufficient (and often superior) for most agentic workloads and dramatically cheaper to run.
- detail: Productized as Liquid Nanos. Backed by NVIDIA Research's June 2025 arXiv paper.
- connects_to:
  - slm: thesis about
  - nvidia_slm_paper: canonical citation
  - liquid_nanos: productization
  - agentic_ai: the workload it serves
  - cost_equation: the economic case

## nvidia_slm_paper
- aliases: [Belcak et al., arXiv:2506.02153]
- category: efficiency
- definition: A June 2025 paper from NVIDIA Research and Georgia Tech arguing "Small Language Models are the Future of Agentic AI."
- detail: Headline claim: serving a 7B SLM is 10–30× cheaper (latency, energy, FLOPs) than a 70–175B LLM. Indispensable PMM citation because NVIDIA is making Liquid's argument for them.
- connects_to:
  - slm_thesis: canonical evidence for
  - agentic_ai: subject of
  - cost_equation: quantifies
  - scale_vs_efficiency_debate: anchors

---

# LIQUID LINEAGE & ARCHITECTURE

## lnn: Liquid Neural Network
- aliases: [LNN, liquid network, liquid time-constant network]
- category: liquid-tech
- definition: A neural network where each neuron is governed by a differential equation, allowing the network's dynamics to change continuously over time and adapt after training.
- detail: Inspired by the 302-neuron nervous system of the C. elegans roundworm. Original work presented at AAAI Feb 2021; "Closed-form continuous-time neural networks" in Nature Machine Intelligence Nov 2022.
- connects_to:
  - c_elegans: inspiration
  - lfm: lineage and philosophy of, but not literal architecture of
  - ramin_hasani: co-author of
  - mathias_lechner: co-author of
  - daniela_rus: co-author of
  - first_principles: philosophical root of
  - lineage_vs_production: the honesty distinction

## c_elegans
- aliases: [C. elegans, nematode worm]
- category: liquid-tech
- definition: A roundworm with exactly 302 neurons whose complete connectome is mapped; the biological inspiration for liquid neural networks.
- detail: The brand-story anchor: biological efficiency, not biological size, is the right thing to copy.
- connects_to:
  - lnn: inspired
  - first_principles: motif for
  - lineage_vs_production: brand-story element

## first_principles
- aliases: [first-principles design]
- category: liquid-tech
- definition: Designing an architecture from the constraints you actually have (cost, hardware, latency, memory) rather than scaling up what is already popular.
- detail: Liquid's core philosophical claim. The bridge between LNNs (the lineage) and LFM2 (the product).
- connects_to:
  - efficiency_by_design: synonym for
  - lnn: philosophical root
  - lfm2_hybrid_arch: expression of
  - star: tool that operationalizes
  - category_creation: rationale for

## lineage_vs_production
- aliases: [LNN vs LFM2 honesty]
- category: liquid-tech
- definition: The disciplined distinction between LNNs (Liquid's intellectual lineage and brand story) and LFM2 (the actual production architecture, which is a hybrid conv + GQA design).
- detail: Crucial credibility tool: claim the lineage, not literal worm-brain math, in production.
- connects_to:
  - lnn: brand-story side
  - lfm2_hybrid_arch: production side
  - gotchas: anti-overclaim trap

## lfm: Liquid Foundation Model
- aliases: [LFM]
- category: liquid-tech
- definition: A family of non-transformer-derived foundation models built by Liquid AI to run efficiently across hardware tiers, from data-center accelerators down to phones.
- detail: Spans text, vision-language, audio, and retrieval modalities. Branded as "foundation models" (not "SLMs") to claim the broader category.
- connects_to:
  - foundation_model: type of
  - transformer: contrasted with
  - lfm2_family: current generation
  - lfm2_hybrid_arch: defines
  - efficiency_by_design: built on

## lfm2_hybrid_arch
- aliases: [LFM2 architecture]
- category: liquid-tech
- definition: A 16-block hybrid architecture with 10 double-gated short-range LIV convolution blocks and 6 grouped-query attention (GQA) blocks; only ~20% of compute is attention.
- detail: The single most important technical fact about Liquid. Designed via STAR; co-optimized with AMD hardware.
- connects_to:
  - convolution: dominant operation
  - liv_convolution: specific conv variant
  - gqa: 6 of 16 blocks
  - multiplicative_gate: in conv blocks
  - star: discovered by
  - hardware_aware_design: produced by
  - lfm2_family: instantiates
  - performance_claims: source of

## liv_convolution
- aliases: [LIV conv]
- category: liquid-tech
- definition: Liquid's specific short-convolution variant used in LFM2's convolution blocks.
- detail: Detail-level term; knowing it exists signals you've read the model card.
- connects_to:
  - convolution: instance of
  - lfm2_hybrid_arch: building block

## star: Synthesis of Tailored Architectures (NAS engine)
- aliases: [STAR]
- category: liquid-tech
- definition: Liquid's hardware-in-the-loop neural architecture search (NAS) engine that explores model architectures and optimizes them for a specific chip, latency, and memory target.
- detail: The defensible moat behind custom enterprise model development. Lets Liquid tailor an architecture per customer's silicon.
- connects_to:
  - nas: instance of
  - lfm2_hybrid_arch: discovered
  - hardware_aware_design: tool for
  - custom_enterprise_models: enables
  - amd_partnership: co-optimizes with

## nas: Neural Architecture Search
- aliases: [NAS]
- category: ai-core
- definition: An automated process for discovering optimal neural network architectures rather than designing them by hand.
- detail: STAR is Liquid's NAS engine, specialized for hardware-in-the-loop search.
- connects_to:
  - star: Liquid's instance
  - hardware_aware_design: target of

## hardware_aware_design
- aliases: []
- category: liquid-tech
- definition: Co-designing model architecture with the target hardware (CPU/GPU/NPU) so block shapes and memory patterns map cleanly onto the silicon.
- detail: Implemented via STAR. Strategically aligned with AMD as both investor and hardware partner.
- connects_to:
  - star: tool for
  - npu: targets
  - cpu: targets
  - gpu: targets
  - amd_partnership: aligned with
  - efficiency_by_design: implementation of
  - lfm2_hybrid_arch: produced by

---

# LIQUID PRODUCTS

## lfm_first_gen: First-Generation LFMs (Oct 2024)
- aliases: [LFM-1.3B, LFM-3.1B, LFM-40.3B MoE]
- category: liquid-products
- definition: The first commercial LFM family: LFM-1.3B (on-device), LFM-3.1B (edge), LFM-40.3B MoE (complex tasks).
- detail: Not open-source. Distributed via Liquid Playground, Lambda, Perplexity, Cerebras.
- connects_to:
  - lfm: instance of
  - lfm2_family: superseded by
  - moe: includes one

## lfm2_family
- aliases: [LFM2]
- category: liquid-products
- definition: Liquid's second-generation, open-weight model family released July 2025; dense models at 350M, 700M, 1.2B, 2.6B, plus an 8.3B/1.5B-active MoE.
- detail: 32K context. Apache-2.0-based license, free commercial use under $10M revenue. Over 10M Hugging Face downloads by Feb 2026.
- connects_to:
  - lfm: instance of
  - lfm2_hybrid_arch: built on
  - lfm2_vl: sibling
  - lfm2_audio: sibling
  - lfm2_colbert: sibling
  - liquid_nanos: variants of
  - context_window: 32K
  - apache_license_cap: licensed under
  - hugging_face: distributed via

## lfm2_vl: LFM2-Vision-Language
- aliases: [LFM2-VL, LFM2-VL-450M]
- category: liquid-products
- definition: The vision-language member of the LFM2 family.
- detail: Powers Brilliant Labs Halo AI glasses (LFM2-VL-450M).
- connects_to:
  - lfm2_family: sibling of
  - brilliant_labs: deployed in
  - wearables_vertical: use case

## lfm2_audio: LFM2-Audio
- aliases: [LFM2-Audio]
- category: liquid-products
- definition: A 1.5B end-to-end speech model (no separate ASR/TTS pipeline) in the LFM2 family.
- detail: Expands LFM2 beyond text to voice interfaces.
- connects_to:
  - lfm2_family: sibling of

## lfm2_colbert: LFM2-ColBERT
- aliases: []
- category: liquid-products
- definition: A multilingual retrieval model in the LFM2 family used as the retrieval backbone for RAG.
- detail: Connects on-device generation to on-device search.
- connects_to:
  - lfm2_family: sibling of
  - rag: backbone for

## liquid_nanos
- aliases: [Nanos]
- category: liquid-products
- definition: Task-specific models released Sept 25, 2025, 350M–2.6B parameters, claiming GPT-4o-class performance on a single narrow task.
- detail: Initial seven include LFM2-350M-ENJP-MT (translation), LFM2-350M-Extract, LFM2-1.2B-RAG, LFM2-1.2B-Tool, LFM2-350M-Math. Productization of the SLM thesis.
- connects_to:
  - slm_thesis: productization of
  - slm: instance of
  - rag: includes a variant
  - tool_calling: includes a variant
  - fine_tuning: built via
  - shopify: endorsing customer
  - agentic_ai: substrate for

## lfm2_24b_a2b
- aliases: [LFM2-24B-A2B]
- category: liquid-products
- definition: A Feb 2026 MoE model with 24B total parameters and 2B active per token, fitting in 32GB RAM.
- detail: 112 tok/s decode on AMD CPU; 293 tok/s on H100. The "higher-capability edge" tier.
- connects_to:
  - moe: instance of
  - lfm2_family: extension of
  - amd_partnership: benchmarked on
  - performance_claims: source of

## lfm2_5: LFM2.5
- aliases: []
- category: liquid-products
- definition: A generation introduced at CES Jan 2026, featuring a 128K context window and reasoning tuning across 9 languages.
- detail: Includes LFM2.5-8B-A1B and LFM2.5-1.2B-Thinking.
- connects_to:
  - context_window: 128K
  - lfm2_family: successor to
  - lfm2_5_8b_a1b: includes
  - lfm2_5_thinking: includes

## lfm2_5_8b_a1b
- aliases: [LFM2.5-8B-A1B]
- category: liquid-products
- definition: An MoE model released May 28, 2026, with 8.3B total parameters / 1.5B active, 128K context, 38T training tokens.
- detail: Reasoning-tuned, multilingual (9 languages).
- connects_to:
  - lfm2_5: instance of
  - moe: instance of
  - context_window: 128K

## lfm2_5_thinking
- aliases: [LFM2.5-1.2B-Thinking]
- category: liquid-products
- definition: An on-device reasoning model released Jan 2026 that runs at 235 tok/s on CPU in under 900 MB.
- detail: Demonstrates that reasoning-class behavior can fit on a phone.
- connects_to:
  - lfm2_5: instance of
  - decode_speed: 235 tok/s
  - on_device: runs on
  - performance_claims: source of

## leap: Liquid Edge AI Platform
- aliases: [LEAP]
- category: liquid-products
- definition: A developer-first SDK to deploy SLMs in iOS/Android apps in ~10 lines of code, with a model library starting at 300 MB and 4 GB+ RAM minimum.
- detail: The product-led-growth motion. Bottoms-up; devs adopt then their company buys.
- connects_to:
  - on_device: deployment platform for
  - technical_buyer: targets
  - self_serve_motion: anchors
  - lfm2_family: distributes

## apollo
- aliases: []
- category: liquid-products
- definition: A free on-device chat app (acquired by Liquid, originally designed by Aaron Ng) that lets non-developers run LFMs locally.
- detail: The most direct way to experience an LFM as an end user.
- connects_to:
  - on_device: demonstrates
  - leap: complements
  - aaron_ng: original designer

## hugging_face
- aliases: [HF]
- category: liquid-products
- definition: The public model-distribution platform where Liquid's open-weight LFM2 family is hosted.
- detail: Over 10M LFM2 downloads as of Feb 2026.
- connects_to:
  - lfm2_family: hosts
  - self_serve_motion: channel for
  - open_weights_vs_proprietary: distribution proof

## runtime_ecosystem
- aliases: [runtimes]
- category: liquid-products
- definition: The community inference runtimes that support LFM models: llama.cpp, ExecuTorch, vLLM, MLX, Ollama, ONNX.
- detail: Plus AMD Ryzen acceleration paths. Launch ecosystem partners: Qualcomm, Ollama, FastFlowLM, Cactus Compute, Nexa AI.
- connects_to:
  - on_device: enables
  - amd_partnership: includes
  - leap: complements

## apache_license_cap
- aliases: ["Apache-2.0–based with $10M cap"]
- category: liquid-products
- definition: The LFM2 license: Apache-2.0-based, free commercial use under $10M revenue; above that threshold, contact sales.
- detail: The bifurcation behind Liquid's "community floor + enterprise ceiling" model.
- connects_to:
  - lfm2_family: licensed under
  - open_weights_vs_proprietary: enacts
  - custom_enterprise_models: revenue motion above the cap

---

# MARKET & DEBATES

## frontier_labs
- aliases: [OpenAI, Anthropic, Google DeepMind, Meta]
- category: market
- definition: The companies building the largest cloud-hosted general-purpose LLMs and competing on benchmark supremacy.
- detail: Liquid explicitly does NOT compete head-on with them; positions in a different category.
- connects_to:
  - cloud: live in
  - llm: build
  - category_creation: contrasted with
  - hybrid_future: still useful in

## big_tech_on_device
- aliases: [Gemini Nano, Apple Intelligence, Phi, Llama 3.2 1B/3B, Gemma]
- category: market
- definition: Big-tech on-device model efforts: Google Gemini Nano (Android), Apple on-device models, Microsoft Phi (Phi-4-mini ~3.8B), Meta Llama 3.2 1B/3B, Google Gemma 3/3n.
- detail: Direct competitive set for Liquid in the on-device market.
- connects_to:
  - on_device: compete in
  - slm: are
  - liquid_competitive_set: members of

## open_small_models
- aliases: [Qwen, SmolLM, Granite, Nemotron, Mistral]
- category: market
- definition: Open small-model families: Alibaba Qwen, SmolLM, IBM Granite, NVIDIA Nemotron, Mistral.
- detail: Liquid's open-weight peer set. LFM2's "200% faster than Qwen3" claim positions against these.
- connects_to:
  - slm: are
  - open_weights_vs_proprietary: live in
  - performance_claims: benchmark against

## edge_specialists
- aliases: [webAI, Baseten, Qualcomm AI, Arcee AI, Nexa AI, Cactus Compute]
- category: market
- definition: Edge/efficient-AI specialist companies adjacent to Liquid: webAI, Baseten, Qualcomm AI, Arcee AI, Nexa AI, Cactus Compute. Plus Hugging Face as the distribution layer.
- detail: The "bottom floor" of the AI market — Liquid's closest direct peer set.
- connects_to:
  - on_device: focus
  - liquid_competitive_set: members
  - hugging_face: distribution

## liquid_competitive_set
- aliases: []
- category: market
- definition: The aggregate set of competitors Liquid touches: frontier labs, big-tech on-device, open small models, edge specialists.
- detail: Liquid differentiates on first-principles architecture, efficiency-by-design, STAR, full deployment stack, and AMD alignment.
- connects_to:
  - frontier_labs: avoids competing
  - big_tech_on_device: competes with
  - open_small_models: competes with
  - edge_specialists: competes with
  - liquid_differentiators: how Liquid wins

## liquid_differentiators
- aliases: []
- category: market
- definition: The five things Liquid claims uniquely: novel first-principles architecture, efficiency by design (not compression), STAR per-customer architecture search, full deployment stack (LEAP + custom), AMD strategic alignment.
- detail: The battlecard summary against the competitive set.
- connects_to:
  - first_principles: includes
  - efficiency_by_design: includes
  - star: includes
  - leap: includes
  - amd_partnership: includes
  - category_creation: enables

## scale_vs_efficiency_debate
- aliases: []
- category: market
- definition: The 2025–2026 industry shift from "bigger is better" to "right-sized, efficient, hardware-aware models win the deployable market."
- detail: Anchored by the NVIDIA SLM paper. The macro tailwind for Liquid.
- connects_to:
  - nvidia_slm_paper: anchors
  - slm_thesis: companion of
  - cost_equation: motivates
  - cloud_hangover: companion debate

## cloud_hangover
- aliases: [data-center capex debate]
- category: market
- definition: The concern that data-center capex (>$1T projected by 2027 per Hasani) is rising faster than monetizable AI revenue.
- detail: Motivates the move of inference to the edge. A recurring Hasani talking point.
- connects_to:
  - cost_equation: instance of
  - cloud: critiques
  - on_device: motivates
  - ramin_hasani: invokes

## privacy_sovereignty_debate
- aliases: [data sovereignty]
- category: market
- definition: The argument that regulated industries and sovereign nations need AI that keeps data local.
- detail: Behind the G42 deal and Mercedes deal; the emotional moat of on-device.
- connects_to:
  - on_device: motivates
  - g42: customer expression of
  - value_prop_privacy: anchors

## heterogeneous_agentic_debate
- aliases: [swarms of agents]
- category: market
- definition: The thesis that the future of AI is many small specialized agents orchestrated together, with occasional frontier LLM calls.
- detail: Hasani's "~100 agents per person" framing. Connects SLMs, on-device, and Nanos into one story.
- connects_to:
  - agentic_ai: structural form
  - slm_thesis: implies
  - liquid_nanos: substrate
  - hybrid_future: companion

## open_weights_vs_proprietary
- aliases: []
- category: market
- definition: The tension between releasing model weights publicly (community goodwill, distribution) and keeping them proprietary (monetization).
- detail: Liquid open-sources LFM2 but keeps larger / STAR-derived / custom models commercial.
- connects_to:
  - apache_license_cap: enacts
  - hugging_face: distribution side
  - custom_enterprise_models: monetization side
  - self_serve_motion: open side
  - enterprise_motion: proprietary side

## hybrid_future
- aliases: ["I believe in a hybrid future"]
- category: market
- definition: Liquid's strategic position that on-device + cloud is the right architecture, not "on-device replaces cloud."
- detail: Hasani's direct quote. Avoids religious wars; keeps partnerships open; aligns Liquid with SLM-as-agentic-substrate, not anti-cloud.
- connects_to:
  - cloud: half of
  - on_device: half of
  - heterogeneous_agentic_debate: enables
  - frontier_labs: still useful in
  - taglines: anchor of

## category_creation
- aliases: []
- category: market
- definition: The PMM job of defining and educating the market on a new category ("on-device foundation models") rather than competing inside an existing one.
- detail: The core mandate of the first Liquid PMM.
- connects_to:
  - transformer: contrasted with
  - first_principles: rationale
  - efficiency_by_design: rationale
  - pmm_role: central job of
  - sarah_mulligan: hiring manager for

---

# GTM, BUYERS, USE CASES

## pmm_role
- aliases: [Product Marketing Manager, first PMM]
- category: gtm
- definition: The first product-marketing hire at Liquid AI, tasked with category creation, dual-buyer messaging, and launching the marketing function from scratch.
- detail: Reports to a VP of Marketing (not publicly named). Hiring manager: Sarah Mulligan.
- connects_to:
  - category_creation: core mandate
  - economic_buyer: serves
  - technical_buyer: serves
  - sarah_mulligan: hires for
  - positioning_truths: north star

## economic_buyer
- aliases: [exec buyer, business buyer]
- category: gtm
- definition: The enterprise decision-maker (VP/SVP/CIO/CTO/CFO/Head of Product) who buys based on cost, ROI, time-to-market, privacy/compliance, and brand risk.
- detail: First touch is typically sales- or partner-led. Convinced by case studies and named references.
- connects_to:
  - technical_buyer: paired with
  - cost_equation: cares about
  - value_prop_privacy: cares about
  - proof_points: convinced by
  - enterprise_motion: targeted by
  - dual_buyer_message: addressed by

## technical_buyer
- aliases: [developer, ML engineer, embedded engineer]
- category: gtm
- definition: The hands-on decision-maker (developer, ML/embedded engineer, infra architect) who evaluates based on latency, memory, tok/s, integration, runtime support, license, and benchmarks.
- detail: First touch is typically self-serve via Hugging Face + LEAP. Convinced by hands-on PoC.
- connects_to:
  - economic_buyer: paired with
  - leap: targeted by
  - hugging_face: discovers via
  - decode_speed: evaluates on
  - prefill_speed: evaluates on
  - self_serve_motion: targeted by
  - dual_buyer_message: addressed by

## dual_buyer_message
- aliases: [unifying message]
- category: gtm
- definition: The single message that has to land for both buyers: "Frontier-quality, deployed where you need it, at a fraction of the cost, energy, and latency."
- detail: The PMM's central translation problem.
- connects_to:
  - economic_buyer: lands for
  - technical_buyer: lands for
  - value_prop_latency: encodes
  - value_prop_privacy: encodes
  - taglines: source of

## value_prop_latency
- aliases: []
- category: gtm
- definition: The value proposition that LFMs deliver millisecond time-to-first-token with no cloud roundtrip.
- detail: Liquid's #1 stated value prop.
- connects_to:
  - latency: encodes
  - on_device: enables
  - automotive_vertical: critical for
  - technical_buyer: speaks to

## value_prop_memory
- aliases: [small footprint]
- category: gtm
- definition: The value proposition that LFMs fit on phones, cars, wearables, and embedded chips.
- detail: LFM2.5-1.2B-Thinking fits in <900 MB on a phone is the canonical proof.
- connects_to:
  - on_device: enables
  - lfm2_5_thinking: proof
  - technical_buyer: speaks to

## value_prop_privacy
- aliases: []
- category: gtm
- definition: The value proposition that user data never leaves the device.
- detail: The emotional moat of on-device; central for healthcare, finance, automotive, sovereign customers.
- connects_to:
  - on_device: enables
  - privacy_sovereignty_debate: anchors
  - g42: customer proof
  - insilico_medicine: customer proof
  - economic_buyer: speaks to

## value_prop_reliability
- aliases: [offline, no-connectivity]
- category: gtm
- definition: The value proposition that LFMs work without cloud or network connectivity.
- detail: Cars, planes, factories, remote field deployments.
- connects_to:
  - on_device: enables
  - automotive_vertical: critical for

## value_prop_deployability
- aliases: [CPU/GPU/NPU same codebase]
- category: gtm
- definition: The value proposition that the same LFM codebase runs across CPU, GPU, and NPU from data-center accelerators to embedded chips.
- detail: Simplifies BOM and dev experience.
- connects_to:
  - cpu: targets
  - gpu: targets
  - npu: targets
  - runtime_ecosystem: enacts
  - technical_buyer: speaks to

## value_prop_cost_energy
- aliases: [cost/energy efficiency]
- category: gtm
- definition: The value proposition that LFMs deliver inference at dramatically lower cost and energy than cloud LLMs.
- detail: Liquid sometimes claims "50× cheaper, 100× lower energy" (company claim).
- connects_to:
  - cost_equation: instance of
  - cloud_hangover: addresses
  - economic_buyer: speaks to

## automotive_vertical
- aliases: []
- category: gtm
- definition: In-car voice assistants, conversational AI, and safety-adjacent features that cannot rely on cloud connectivity.
- detail: Hasani's framing: "you cannot rely on the cloud to power a critical safety feature inside a car."
- connects_to:
  - mercedes_benz: flagship customer
  - value_prop_latency: drives
  - value_prop_reliability: drives
  - value_prop_privacy: drives

## wearables_vertical
- aliases: [consumer electronics]
- category: gtm
- definition: Smart glasses, on-device assistants, and meeting summarization on AI PCs.
- detail: Brilliant Labs Halo and AMD Ryzen AI PCs are the proof.
- connects_to:
  - brilliant_labs: flagship customer
  - amd_partnership: flagship customer
  - lfm2_vl: powers

## life_sciences_vertical
- aliases: []
- category: gtm
- definition: Private, on-prem drug-discovery and clinical AI workloads that cannot send data off-premises.
- detail: Insilico Medicine LFM2-2.6B-MMAI is the proof: outperformed 10× larger TxGemma-27B on 13 of 22 tasks.
- connects_to:
  - insilico_medicine: flagship customer
  - value_prop_privacy: drives

## financial_ecommerce_vertical
- aliases: [financial services, e-commerce]
- category: gtm
- definition: Ultra-low-latency recommendations, high-frequency decisioning, private document extraction, on-device personalization.
- detail: Shopify CTO Mikhail Parakhin's public Nanos endorsement is the proof.
- connects_to:
  - shopify: flagship customer
  - liquid_nanos: deployed
  - value_prop_latency: drives

## sovereign_vertical
- aliases: [sovereign AI, regional AI]
- category: gtm
- definition: Government and regional AI deployments requiring data sovereignty and regulatory alignment.
- detail: G42 deal (MENA/Global South) and Alef Education (UAE) are the proofs.
- connects_to:
  - g42: flagship customer
  - alef_education: customer
  - privacy_sovereignty_debate: anchors

## proof_points
- aliases: [reference customers, case studies]
- category: gtm
- definition: The named customer deployments that turn architectural claims into believable outcomes.
- detail: AMD, Mercedes-Benz, G42, Insilico, Brilliant Labs, Shopify, Alef Education, ecosystem partners.
- connects_to:
  - amd_partnership: includes
  - mercedes_benz: includes
  - g42: includes
  - insilico_medicine: includes
  - brilliant_labs: includes
  - shopify: includes
  - alef_education: includes
  - positioning_truths: drives "proof over claims"

## enterprise_motion
- aliases: [direct sales]
- category: gtm
- definition: Direct enterprise sales with custom model development driven by STAR architecture search.
- detail: High-ticket. Buyers: Mercedes, G42, Insilico.
- connects_to:
  - custom_enterprise_models: vehicle
  - star: weapon of
  - economic_buyer: targets
  - mercedes_benz: customer
  - g42: customer

## licensing_motion
- aliases: []
- category: gtm
- definition: Liquid licenses LFM models to product builders (e.g., Brilliant Labs licenses LFM2-VL for the Halo).
- detail: Middle-ground monetization for productized customers.
- connects_to:
  - brilliant_labs: customer
  - lfm2_vl: licensed
  - open_weights_vs_proprietary: paid side

## startup_program
- aliases: []
- category: gtm
- definition: A discounted or free tier for early-stage developers building on LEAP.
- detail: Funnel-builder for future enterprise customers.
- connects_to:
  - leap: built on
  - self_serve_motion: complements

## self_serve_motion
- aliases: [PLG, product-led growth]
- category: gtm
- definition: The developer self-serve motion via LEAP and open weights on Hugging Face.
- detail: Bottoms-up demand generation; leads roll up into enterprise.
- connects_to:
  - leap: anchor
  - hugging_face: anchor
  - technical_buyer: targets
  - enterprise_motion: feeds
  - open_weights_vs_proprietary: free side

## custom_enterprise_models
- aliases: [custom model development]
- category: gtm
- definition: Bespoke model engagements where Liquid uses STAR + fine-tuning + synthetic data + hardware-aware optimization to build a model for a specific customer's chip and task.
- detail: The most defensible and highest-value Liquid revenue stream.
- connects_to:
  - star: built via
  - fine_tuning: built via
  - hardware_aware_design: applied in
  - enterprise_motion: vehicle of
  - mercedes_benz: example
  - amd_partnership: example

## gtm_funnel
- aliases: []
- category: gtm
- definition: The end-to-end Liquid go-to-market funnel: awareness → education → tech eval (LEAP/HF) → econ eval (cases/battlecards) → decision (STAR proposal) → expansion.
- detail: Different audiences fire at different stages; PMM messaging maps to each.
- connects_to:
  - economic_buyer: tracks through
  - technical_buyer: tracks through
  - self_serve_motion: feeds
  - enterprise_motion: closes in
  - proof_points: used at decision

## positioning_truths
- aliases: [five positioning truths]
- category: gtm
- definition: The five north-star truths for the PMM: (1) category creation is the core challenge; (2) two buyers, one story; (3) differentiate on efficiency by design + deployability, not benchmarks; (4) proof over claims; (5) hybrid, not anti-cloud.
- detail: Anchors every messaging decision.
- connects_to:
  - category_creation: #1
  - dual_buyer_message: #2
  - efficiency_by_design: #3
  - proof_points: #4
  - hybrid_future: #5

---

# PEOPLE

## ramin_hasani
- aliases: [Ramin Hasani]
- category: people
- definition: Co-founder and CEO of Liquid AI; co-author of the original liquid neural network papers; public spokesperson for Liquid's thesis.
- detail: Sources of memorable quotes: "I believe in a hybrid future," ">$1T data-center capex by 2027," "you cannot rely on the cloud for safety features."
- connects_to:
  - lnn: co-author
  - liquid_ai: founder
  - hybrid_future: spokesperson for
  - cloud_hangover: invokes
  - mckinsey_qb_interview: subject of Sarah's edit

## mathias_lechner
- aliases: [Mathias Lechner]
- category: people
- definition: Co-founder and CTO of Liquid AI; co-author of the LNN papers.
- detail: Technical leadership.
- connects_to:
  - lnn: co-author
  - liquid_ai: founder

## alexander_amini
- aliases: [Alexander Amini]
- category: people
- definition: Co-founder and Chief Science Officer of Liquid AI.
- detail: Research leadership.
- connects_to:
  - liquid_ai: founder

## daniela_rus
- aliases: [Daniela Rus]
- category: people
- definition: Co-founder of Liquid AI; director of MIT's Computer Science and Artificial Intelligence Laboratory (CSAIL).
- detail: Provides Liquid's academic prestige and MIT lineage.
- connects_to:
  - liquid_ai: founder
  - mit_csail: directs
  - lnn: co-author

## sarah_mulligan
- aliases: [Sarah Mulligan]
- category: people
- definition: Hiring manager for the first PMM role at Liquid AI; previously Global Director / Global Head of Marketing & Communications at QuantumBlack (AI by McKinsey).
- detail: Personally edited the QuantumBlack interview "The case for liquid foundation models" with Ramin Hasani — meaning she already knows Liquid's positioning deeply. Self-described as "strategic, story-driven and creative."
- connects_to:
  - pmm_role: hires for
  - mckinsey_qb_interview: edited
  - ramin_hasani: prior professional contact
  - category_creation: her stated strength
  - quantumblack: prior role

## mikhail_parakhin
- aliases: [Mikhail Parakhin]
- category: people
- definition: CTO of Shopify; public endorser of Liquid Nanos.
- detail: Public statement that Shopify uses the Nanos across its platforms.
- connects_to:
  - shopify: CTO of
  - liquid_nanos: endorses

## naval_ravikant
- aliases: [Naval Ravikant]
- category: people
- definition: Angel investor in Liquid AI.
- detail: Credibility signal; namedrop only if asked.
- connects_to:
  - series_a: investor

## aaron_ng
- aliases: [Aaron Ng]
- category: people
- definition: Original designer of the Apollo on-device chat app (which Liquid acquired).
- detail: Product/design background.
- connects_to:
  - apollo: designed

---

# COMPANY ENTITIES

## liquid_ai
- aliases: [Liquid AI]
- category: liquid-products
- definition: A 2023 MIT CSAIL spinout that builds Liquid Foundation Models (LFMs) — efficient, non-transformer foundation models for on-device and edge deployment.
- detail: HQ Cambridge/Boston. ~121 employees as of April 2026. ~$297M total raised. $2.3B Series A valuation.
- connects_to:
  - mit_csail: spun out of
  - lfm: produces
  - ramin_hasani: CEO
  - mathias_lechner: CTO
  - alexander_amini: CSO
  - daniela_rus: co-founder
  - series_a: funded by
  - amd_partnership: lead investor + hardware partner

## mit_csail
- aliases: [MIT CSAIL]
- category: people
- definition: MIT's Computer Science and Artificial Intelligence Laboratory; the lab Liquid AI spun out of.
- detail: Provides the academic lineage and credibility.
- connects_to:
  - liquid_ai: parent lab
  - daniela_rus: directs
  - lnn: birthplace of

## quantumblack
- aliases: [QuantumBlack, AI by McKinsey]
- category: people
- definition: McKinsey's AI consultancy; Sarah Mulligan's prior employer.
- detail: Published "The case for liquid foundation models" interview with Hasani.
- connects_to:
  - sarah_mulligan: prior employer
  - mckinsey_qb_interview: publisher

## mckinsey_qb_interview
- aliases: ["The case for liquid foundation models"]
- category: people
- definition: The QuantumBlack/McKinsey interview with Ramin Hasani that Sarah Mulligan personally edited.
- detail: The single most important "do your homework" artifact for the PMM candidate — it's the language Sarah is comfortable with.
- connects_to:
  - sarah_mulligan: edited
  - ramin_hasani: subject
  - quantumblack: publisher
  - taglines: source of

## amd_partnership
- aliases: [AMD, AMD Ventures]
- category: liquid-products
- definition: AMD is Liquid's lead Series A investor (AMD Ventures) AND strategic hardware partner.
- detail: Reference deployment: on-device meeting summarization on Ryzen AI PCs with custom LFM2-2.6B. Strategic alternative to NVIDIA dependence.
- connects_to:
  - series_a: led
  - hardware_aware_design: paired with
  - gpu: alternative provider
  - npu: provides Ryzen AI
  - wearables_vertical: meeting summarization
  - proof_points: anchor of

## mercedes_benz
- aliases: [Mercedes-Benz]
- category: gtm
- definition: Liquid's flagship automotive customer; multi-year deal announced April 23, 2026, for embedded on-device intelligence in third/fourth-generation MBUX in North America, on MB.OS. First production deployment targeted H2 2026.
- detail: Validates Liquid as automotive-grade and certifiable.
- connects_to:
  - automotive_vertical: anchors
  - enterprise_motion: closed by
  - custom_enterprise_models: example
  - proof_points: anchor

## g42
- aliases: [G42, Core42, Inception]
- category: gtm
- definition: A June 2025 customer for sovereign/private AI for MENA and the Global South, via Core42 and Inception.
- detail: Validates Liquid for sovereign AI.
- connects_to:
  - sovereign_vertical: anchors
  - privacy_sovereignty_debate: customer expression
  - proof_points: anchor

## insilico_medicine
- aliases: [Insilico]
- category: gtm
- definition: A drug-discovery partner; their LFM2-2.6B-MMAI model outperformed the 10× larger TxGemma-27B on 13 of 22 tasks.
- detail: The cleanest "small specialized beats big generic" numerical proof in the Liquid deck.
- connects_to:
  - life_sciences_vertical: anchors
  - slm_thesis: proves
  - proof_points: anchor

## brilliant_labs
- aliases: [Brilliant Labs, Halo]
- category: gtm
- definition: A wearables partner whose $299 Halo AI glasses are powered by LFM2-VL-450M.
- detail: Validates Liquid for consumer wearables and the licensing model.
- connects_to:
  - wearables_vertical: anchors
  - lfm2_vl: licenses
  - licensing_motion: example
  - proof_points: anchor

## shopify
- aliases: []
- category: gtm
- definition: An e-commerce customer; CTO Mikhail Parakhin publicly endorsed the Nanos and said Shopify uses them across its platforms.
- detail: Software-first enterprise validation.
- connects_to:
  - financial_ecommerce_vertical: anchors
  - liquid_nanos: deploys
  - mikhail_parakhin: CTO
  - proof_points: anchor

## alef_education
- aliases: [Alef]
- category: gtm
- definition: A UAE-based edtech customer of Liquid AI.
- detail: Regional + vertical (education) proof.
- connects_to:
  - sovereign_vertical: regional
  - proof_points: anchor

## series_a
- aliases: [$250M Series A]
- category: numbers
- definition: Liquid's December 2024 Series A: $250M at a $2.3B valuation, led by AMD Ventures.
- detail: Other investors include OSS Capital, Duke Capital Partners, PagsGroup, Breyer Capital, and angel Naval Ravikant. Total raised ~$297M including the $46.6M seed (Dec 2023).
- connects_to:
  - amd_partnership: led
  - liquid_ai: funded
  - naval_ravikant: angel

---

# TAGLINES & LANGUAGE

## taglines
- aliases: [Liquid's language]
- category: taglines
- definition: The verbatim phrases Liquid uses to describe itself, which the PMM should mirror.
- detail: See sub-entries for the canonical lines.
- connects_to:
  - efficiency_by_design: anchor line
  - hybrid_future: anchor line
  - mckinsey_qb_interview: source

## tagline_efficient_at_every_scale
- aliases: ["build efficient general-purpose AI at every scale"]
- category: taglines
- definition: Liquid's core mission statement.
- detail: Pairs scale with efficiency — explicitly not "biggest" but "most capable per cost."
- connects_to:
  - taglines: instance
  - efficiency_by_design: encodes

## tagline_every_device_is_ai_device
- aliases: ["turns every device into an AI device, locally"]
- category: taglines
- definition: Liquid's on-device positioning line.
- detail: "Locally" is the key word.
- connects_to:
  - taglines: instance
  - on_device: encodes

## tagline_ship_intelligence_to_device
- aliases: ["ship intelligence to the device"]
- category: taglines
- definition: The architectural inversion: instead of shipping data to the cloud, ship the model to the data.
- detail: Pairs nicely against "ship data to the cloud."
- connects_to:
  - taglines: instance
  - on_device: encodes
  - cloud: contrasts with

## tagline_cloud_free_economics
- aliases: ["planet-scale AI agents with cloud-free economics"]
- category: taglines
- definition: The agentic-AI cost line: many agents only work economically without per-call cloud fees.
- detail: Connects agentic future to on-device thesis.
- connects_to:
  - taglines: instance
  - agentic_ai: encodes
  - cost_equation: encodes
  - heterogeneous_agentic_debate: encodes

## tagline_fast_private_sovereign
- aliases: ["fast, private and sovereign"]
- category: taglines
- definition: The regulated-buyer line summarizing three values at once.
- detail: Lands hardest in healthcare, finance, defense, government.
- connects_to:
  - taglines: instance
  - value_prop_latency: encodes
  - value_prop_privacy: encodes
  - sovereign_vertical: encodes

## tagline_ai_that_works_everywhere
- aliases: ["AI that works — everywhere you need it"]
- category: taglines
- definition: The deployability + reliability line.
- detail: Sales-friendly. Versatile.
- connects_to:
  - taglines: instance
  - value_prop_deployability: encodes
  - value_prop_reliability: encodes

## gotchas
- aliases: [overclaims, traps]
- category: taglines
- definition: Things the PMM must never say because they overclaim or misstate facts.
- detail: Key items: don't call LFM2 a "worm-brain network"; don't call Liquid an AI chip company; don't claim "beats GPT-5 at everything"; don't say "killing the cloud"; AMD (not Nvidia/Samsung) led the Series A; the close was $250M at $2.3B (not $300M at $3.7B).
- connects_to:
  - lineage_vs_production: anti-overclaim
  - amd_partnership: corrects misattribution
  - series_a: corrects misattribution
  - hybrid_future: corrects "anti-cloud"

## performance_claims
- aliases: []
- category: numbers
- definition: Liquid's headline efficiency/speed claims, to be presented as company claims unless otherwise sourced.
- detail: LFM2 "200% faster decode/prefill than Qwen3 and Gemma 3 on CPU"; "3× faster training than prior gen"; LFM2-24B-A2B "112 tok/s on AMD CPU, 293 tok/s on H100, fits in 32 GB"; LFM2-1.2B-Thinking "235 tok/s on CPU under 900 MB"; LFM2 family "10M+ Hugging Face downloads"; LFM2-2.6B-MMAI "beats 10× larger TxGemma-27B on 13 of 22 tasks." Sometimes "50× cheaper, 100× lower energy."
- connects_to:
  - lfm2_hybrid_arch: source of speed
  - lfm2_24b_a2b: source
  - lfm2_5_thinking: source
  - insilico_medicine: source of 13/22 claim
  - gotchas: must be flagged as company claims

---

# QUICK INDEX (alphabetical, by TERM_ID)

aaron_ng, agentic_ai, alef_education, alexander_amini, amd_partnership, apache_license_cap, apollo, attention, autoregressive_generation, big_tech_on_device, brilliant_labs, c_elegans, category_creation, cloud, cloud_hangover, context_window, convolution, cost_equation, cpu, custom_enterprise_models, daniela_rus, decode_speed, distillation, dual_buyer_message, economic_buyer, edge_specialists, efficiency_by_design, enterprise_motion, financial_ecommerce_vertical, fine_tuning, first_principles, foundation_model, frontier_labs, g42, gotchas, gpu, gqa, gtm_funnel, hardware_aware_design, heterogeneous_agentic_debate, hugging_face, hybrid_future, inference, insilico_medicine, kv_cache, latency, leap, lfm, lfm2_5, lfm2_5_8b_a1b, lfm2_5_thinking, lfm2_24b_a2b, lfm2_audio, lfm2_colbert, lfm2_family, lfm2_first_gen (lfm_first_gen), lfm2_hybrid_arch, lfm2_vl, licensing_motion, lineage_vs_production, liquid_ai, liquid_competitive_set, liquid_differentiators, liquid_nanos, liv_convolution, llm, lnn, machine_learning, mathias_lechner, mckinsey_qb_interview, mercedes_benz, mha, mikhail_parakhin, mit_csail, moe, mqa, multiplicative_gate, nas, naval_ravikant, neural_network, npu, nvidia_slm_paper, on_device, open_small_models, open_weights_vs_proprietary, parameters, performance_claims, pmm_role, positioning_truths, prefill_speed, pretraining, privacy_sovereignty_debate, proof_points, quantumblack, quantization, rag, ramin_hasani, rlhf, runtime_ecosystem, sarah_mulligan, scale_vs_efficiency_debate, self_serve_motion, series_a, shopify, slm, slm_thesis, sovereign_vertical, star, startup_program, tagline_ai_that_works_everywhere, tagline_cloud_free_economics, tagline_efficient_at_every_scale, tagline_every_device_is_ai_device, tagline_fast_private_sovereign, tagline_ship_intelligence_to_device, taglines, technical_buyer, throughput, token, tool_calling, training, transformer, value_prop_cost_energy, value_prop_deployability, value_prop_latency, value_prop_memory, value_prop_privacy, value_prop_reliability, wearables_vertical

---

# Source Notes

- Liquid-specific facts (founders, products, model specs, partner deals, funding, taglines) derive from the original briefing the user attached.
- AI/ML foundations (attention, GQA, NPUs, LNNs) align with: [IBM on GQA](https://www.ibm.com/think/topics/grouped-query-attention), [IBM on NPU vs GPU](https://www.ibm.com/think/topics/npu-vs-gpu), [MIT News on liquid networks](https://news.mit.edu/2021/machine-learning-adapts-0128), [Quanta Magazine on liquid networks](https://www.quantamagazine.org/researchers-discover-a-more-flexible-approach-to-machine-learning-20230207/).
- The SLM efficiency paper: [Belcak et al., arXiv:2506.02153](https://arxiv.org/abs/2506.02153).
- Performance numbers attributed to Liquid are tagged as company claims, not independently verified.
