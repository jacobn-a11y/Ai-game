# Edge AI Specialists & Novel Architecture Competitors: Competitive Intelligence Report

*Research compiled June 2025 | Positioning against Liquid AI's LFM / STAR / edge-first architecture*

---

## Framing: Liquid AI's Competitive Position

Liquid AI's differentiation rests on three interlocking claims: (1) its Liquid Foundation Models (LFMs) use a non-transformer recurrent architecture derived from liquid neural networks / ODEs, (2) its **STAR** (Synthesis of Tailored Architectures) system uses evolutionary algorithms to automatically co-design architectures for specific hardware targets—GPU, NPU, CPU—rather than adapting one universal design, and (3) the entire stack is built "edge-first," co-optimizing pretraining, quantization, and deployment for constrained processors. The LFM2.5-1.2B family (released with 28T training tokens, multimodal audio/vision variants, and launch partners AMD and Nexa AI) is the current flagship edge product. This is the backdrop against which all competitors below should be evaluated.

---

## CLUSTER A — Edge AI / Efficient Deployment Specialists

### 1. webAI

**What they do:** Austin-based webAI positions itself as a "sovereign AI platform" that enables enterprises to build, deploy, and operate custom AI models on their own local infrastructure—on-premise, air-gapped, or device-local—eliminating cloud dependency. Their flagship product is **Companion**, an enterprise AI assistant that runs entirely on local hardware with no data transmitted off-device.

**Architecture:** webAI does not publicly describe a proprietary model architecture; their differentiation is at the *deployment and orchestration* layer, not the model-architecture layer. They appear to work with fine-tuned open-weight models deployed through a proprietary runtime. The pitch is sovereignty, latency, and data ownership rather than novel model primitives.

**Funding & Scale:** webAI raised **$60M** at a **$700M valuation** as of September 2024, with board members from Apple, Benchmark Capital, and Activision Blizzard. By January 2026, [SiliconAngle reported](https://siliconangle.com/2026/01/13/sovereign-ai-unicorn-webais-value-soars-2-5b-double-digit-funding-round/) a Series A extension with Marc Benioff's Time Ventures and Atreides Management pushed the valuation to **$2.5B**—a 3.5× step-up in roughly 16 months. Customers span retail, logistics, and aviation.

**Positioning vs. Liquid AI:** webAI operates above the model layer. It competes for the same *enterprise sovereign AI* budget, but webAI's value prop is *deployment infrastructure and orchestration*; Liquid's is *the model itself being intrinsically more efficient*. A webAI customer still needs efficient models to run locally—Liquid's LFMs are a potential supply-side partner/competitor. The overlap is highest in regulated-industry accounts (healthcare, defense, finance) where data-residency matters. webAI's 2.5B valuation signals strong enterprise traction but not a direct model-architecture threat.

---

### 2. Arcee AI

**What they do:** San Francisco-based Arcee is a **small language model (SLM) specialist** with a vertically-integrated platform: proprietary Foundation Models (AFM), model merging tooling (mergekit, now widely adopted open-source), distillation pipelines, and an enterprise deployment layer.

**Architecture & Models:** Arcee does not replace the transformer—instead it extracts maximum efficiency *from* transformer-based models through novel post-training. Their technique stack includes:
- **Model merging** via [mergekit](https://github.com/arcee-ai/mergekit): merging multiple specialized fine-tunes into one model without full retraining, dramatically more compute-efficient than data blending (DoReMi-style).
- **Spectrum training**: selective layer training to reduce compute.
- **Knowledge distillation** from frontier models (e.g., Llama-3.1-405B → 70B via the [SuperNova-70B pipeline](https://www.arcee.ai/blog/arcee-supernova-training-pipeline-and-model-composition)).
- **AFM-4.5B**: their in-house foundation model, "optimized for CPU and edge environments," combining distillation, merging, and synthetic data. A >1T-parameter open-weight model is reportedly in development.

**Funding:** July 2025 [strategic round](https://www.arcee.ai/blog/arcee-ai-announces-new-strategic-funding-round) led by **Prosperity7 Ventures** and **M12 (Microsoft Ventures)**, with Hitachi Ventures, Wipro, JC2 Ventures, Samsung Next, and others. Valuation estimated at ~$23M–bootstrapped up to this round per public data, with a reported $200M+ round in process targeting $1B+ valuation.

**Positioning vs. Liquid AI:** Arcee competes directly in the SLM enterprise segment. The key architectural distinction: Arcee is *transformer-centric and merge-centric*, using post-training cleverness to punch above weight class. Liquid uses *architecture search (STAR) to design fundamentally non-transformer models*. For edge deployment specifically, Arcee's AFM-4.5B is explicitly CPU/edge-optimized—overlapping directly with LFM2.5. Arcee's mergekit ecosystem gives it strong open-source community moat; Liquid has no equivalent community tool. However, Liquid's approach yields architectures that are inherently more memory-efficient at inference time (no KV cache growth), which Arcee cannot match with transformer-based merging.

---

### 3. Nexa AI

**What they do:** Nexa AI is an on-device AI platform company with three product layers: (1) **NexaSDK**—a unified inference engine for edge/IoT/robotics; (2) **NexaQuant**—a proprietary model compression framework claiming 3× model size reduction, 100%+ accuracy recovery, and 2.5× speed improvement; (3) a model repository optimized for NPU-first deployment.

**Architecture:** Nexa is a deployment/runtime company, not a model developer. Their differentiation is compression fidelity and hardware-aware inference, not novel model primitives. NexaQuant specifically addresses the accuracy-degradation problem in aggressive quantization.

**Hardware Partnerships:** [Qualcomm partnered with Nexa AI and Docker](https://www.qualcomm.com/developer/blog/2026/03/qualcomm-nexa-ai-docker-bring-ai-to-iot-robotics-with-nexasdk-linux) to deliver NexaSDK for Linux targeting IoT and robotics on Qualcomm NPUs. Notably, **Nexa AI is also a Liquid AI launch partner**—the LFM2.5-1.2B announcement specifically names Nexa AI as delivering "optimized performance on NPUs" for LFM2.5.

**Funding:** Nexa AI is reportedly bootstrapped with ~$3.4M ARR and a ~$10.2M estimated valuation as of 2025—a small but focused company.

**Positioning vs. Liquid AI:** The Nexa/Liquid partnership is the clearest example of how these relationships are both competitive and collaborative. Nexa distributes Liquid's models and optimizes them for Qualcomm hardware; Liquid provides the model primitives. The tension point: if Liquid builds its own NPU-optimized runtime (via STAR hardware-aware synthesis), it reduces dependency on Nexa. For now, Nexa is an amplifier for Liquid, not a head-to-head rival.

---

### 4. Cactus Compute

**What they do:** YC S25 startup (Roman Shemet, Henry Ndubuaku) building an **open-source, cross-platform AI inference engine** optimized for mobile devices and wearables. The tagline: "fastest kernels & energy-efficient inference engine for phones."

**Architecture & Performance:** Cactus is ARM-CPU-first by design, deliberately departing from NPU/GPU-centric frameworks (which cover only 30% of the installed phone base). The runtime is available as React Native and Flutter bindings. Key published benchmarks on INT8 Qwen3-600M:
- iPhone 17 Pro: 75 tok/s
- Galaxy S25 Ultra: 60 tok/s
- iPhone 14 Pro: 48 tok/s

File size comparison: Cactus format at 370MB vs. GGUF at 800MB for the same Qwen3-0.6B-INT8 model—a 55% size reduction. Battery drain is a first-class metric, with Cactus reportedly yielding significantly lower power draw than GPU-based inference.

**Ecosystem:** 4,200+ GitHub stars at launch; [open-source under YC S25](https://www.linkedin.com/posts/y-combinator_cactus-yc-s25-is-an-open-source-framework-activity-7350554691916681216-N8sM).

**Positioning vs. Liquid AI:** Cactus is at the runtime/SDK layer, similar to Nexa but mobile-native and developer-focused. It is potentially a *distribution channel* for Liquid's LFMs rather than a direct model competitor—but Cactus's own compression format creates a dependency that bypasses the model's native architecture advantages. If Cactus delivers Llama-3.2-1B at competitive quality/speed on ARM, it reduces the uniqueness of Liquid's edge story for mobile-app developers who care more about ecosystem than architecture purity.

---

### 5. Qualcomm AI Hub

**What they do:** Qualcomm AI Hub is a developer platform offering **175+ pre-optimized ML models** guaranteed to run on Qualcomm Snapdragon hardware, with a cloud-hosted Workbench for compiling, quantizing, profiling, and validating models before device deployment.

**Scope:** The hub covers image classification, object detection, LLM inference, speech recognition, and generative image models. Key integrations: Amazon SageMaker for fine-tuning → Qualcomm AI Hub for optimization → edge deployment pipeline. Partnerships include Nexa AI, Docker, Lantronix, and others.

**Positioning vs. Liquid AI:** Qualcomm AI Hub is infrastructure for *any* model, not a model competitor. However, it shapes the on-device market in a way that matters for Liquid: (1) Qualcomm defines what "optimized" means on Snapdragon—if Liquid's STAR-synthesized architectures don't have first-class Qualcomm Hub support, they may lag commercially against Hub-certified alternatives; (2) Qualcomm's own research teams (e.g., Qualcomm AI Research) publish efficient architectures—the boundary between platform and model is blurring. The Liquid/Qualcomm/Nexa relationship (AMD and Nexa as LFM2.5 launch partners) suggests Liquid is actively courting silicon-layer validation.

---

### 6. Baseten

**What they do:** Baseten is a **cloud inference platform** ("The Inference Cloud for the multi-model era") targeting high-performance, high-scale serving of open-source, custom, and fine-tuned models. Features include custom kernels, advanced KV caching, speculative decoding, and multi-cloud/VPC deployment.

**Architecture:** Baseten is cloud-first, not edge-first. Its differentiation is in inference infrastructure engineering—custom CUDA kernels, the latest decoding techniques—not in model architecture. Target customers are AI companies and enterprises running large-scale cloud workloads.

**Positioning vs. Liquid AI:** Baseten competes at the cloud inference layer where Liquid is weakest (Liquid's current commercial motion is edge/device). Baseten does not play in the on-device space. The relevance to this analysis is as a counter-positioning reference: Baseten is where customers go when cloud-scale throughput matters more than latency/privacy/efficiency. If Liquid fails to establish edge distribution, these cloud inference platforms capture the budget. Baseten's $50M+ raised (Andreessen Horowitz, Greylock) reflects strong traction in AI-company infrastructure.

---

### 7. Hugging Face

**What they do:** Hugging Face is the dominant **open-source model distribution and deployment platform** with 13M+ users, 2M+ public models, and 500K+ public datasets as of Spring 2026. Products include the model Hub, Inference Endpoints (serverless and dedicated), Transformers library, and PEFT/TRL training tooling.

**Edge relevance:** Hugging Face is increasingly active in edge deployment: partnership with Cloudflare for serverless GPU inference, integration with all major on-device frameworks, and hosting of quantized GGUF/ONNX model variants. LFM2.5 is explicitly distributed "on Hugging Face," making HF a distribution dependency for Liquid.

**Positioning vs. Liquid AI:** Hugging Face is both partner and structural competitor. As a platform, it commoditizes model distribution—making it harder for any model company to maintain proprietary moat purely through model weights. The deeper tension: HF's open ecosystem amplifies Arcee's mergekit, RWKV's weights, and every Mamba variant, creating a "marketplace" that benefits Liquid's competitors as much as Liquid itself. Liquid's edge moat needs to rest in *architecture performance + STAR hardware synthesis*, not distribution—HF makes distribution a commodity.

---

### 8. FastFlowLM

**What they do:** FastFlowLM (FLM) is a startup building an **NPU-first LLM inference runtime** targeting AMD Ryzen AI NPUs, with planned betas for Qualcomm Snapdragon and Intel Core Ultra. Self-described as "Ollama for NPUs."

**Technical differentiation:** 
- 10× power efficiency vs. GPU-based stacks on the same chip
- 256K context window (vs. AMD's official stack limited to ~2,000 tokens)
- Multi-modal: vision, audio, text, embedding, and MoE support
- Runtime footprint: ~16MB
- Targets tile-structured NPU accelerators, not GPU SIMD

**Status:** Recently launched, startup-stage, open-source GitHub presence with [community traction on LocalLLaMA](https://www.linkedin.com/posts/ken-qing-yang-3b30331_from-the-localllama-community-on-reddit-activity-7364310760576962560-F4j7). AMD Ryzen AI GA release live; Qualcomm/Intel in beta.

**Positioning vs. Liquid AI:** FastFlowLM is the closest direct runtime competitor to the "run LFM-class models on NPUs" use case. If FastFlowLM successfully makes any GGUF/standard model run with 10× efficiency on AMD NPUs, it undercuts Liquid's claim that architecture-level efficiency (via STAR) is necessary for edge performance—the runtime can compensate. Liquid's counter-argument is that STAR-synthesized models will be even more efficient *within* a well-optimized NPU runtime. This is the key battleground: architecture-first efficiency vs. runtime-first efficiency.

---

## CLUSTER B — Novel Architecture / Non-Transformer Competitors

### 1. Cartesia AI

**Architecture:** Cartesia is the most direct architectural rival to Liquid AI in the voice/edge segment. Founded by **Karan Goel** and **Albert Gu** (lead author of the S4 and Mamba papers) from Stanford AI Lab, Cartesia builds **state space models (SSMs)**—specifically Mamba-derived architectures that replace self-attention with a recurrent state-update mechanism. SSM inference scales linearly with sequence length (vs. transformer's quadratic attention complexity), enabling constant-time generation regardless of context length.

Key architectural properties:
- **Linear-time inference** with fixed memory footprint (no KV cache growth)
- **Continuous-time dynamics** — conceptually related to but architecturally distinct from Liquid's ODE-based liquid neural networks
- **Streaming-native**: processes audio/text token-by-token without buffering
- Designed for hardware-constrained, latency-critical environments

**Models:** 
- [**Sonic (2024)**](https://www.indexventures.com/perspectives/building-the-next-generation-of-real-time-ai-models-our-investment-in-cartesia/): First TTS model—"world's fastest text-to-speech," offline-capable
- **Sonic 3 (2025)**: Sub-200ms latency, multilingual, described as passing a TTS Turing test
- [**Sonic 3.5 (current)**](https://docs.cartesia.ai/build-with-cartesia/tts-models/latest): Sub-90ms latency, ranked #1 naturalness, 42 languages, 40ms for Sonic Turbo variant

**The latency gap they're solving:** [Contrary Research analysis](https://research.contrary.com/company/cartesia) shows production voice agent median latency is 1.4–1.7 seconds (5× slower than 300ms human threshold), with transformer-based LLM inference accounting for 70% of that latency. Cartesia's SSM architecture directly addresses this.

**Funding:** **$27M total** as of 2024 funding—$5M seed + $22M Series A led by [Index Ventures](https://www.indexventures.com/perspectives/building-the-next-generation-of-real-time-ai-models-our-investment-in-cartesia/), with A*, Conviction, General Catalyst, and others. [TechCrunch reported](https://techcrunch.com/2024/12/12/cartesia-claims-its-ai-is-efficient-enough-to-run-pretty-much-anywhere/) Cartesia's ambitions extend beyond TTS to general multimodal SSM deployment.

**Positioning vs. Liquid AI:** This is the most direct head-to-head. Both companies build non-transformer recurrent architectures for edge/latency-critical use cases. The distinctions:
- **Architectural lineage**: Cartesia descends from Mamba/SSM (structured state space, discretized linear ODEs); Liquid descends from continuous-time ODE-based liquid neural networks. Both achieve linear inference complexity but via different mathematical formalisms.
- **Application focus**: Cartesia is voice-first, with TTS as commercial anchor. Liquid is multimodal general-purpose (text, audio, vision LFMs). Cartesia has a narrower beachhead; Liquid has a broader but more competitive surface.
- **Hardware synthesis**: Liquid's STAR system auto-discovers hardware-optimal architectures. Cartesia does not have a publicly described equivalent—their architecture is hand-designed by SSM experts.
- **Funding**: Liquid AI has raised ~$300M+; Cartesia is at $27M—significantly earlier-stage, which means less capacity for compute-intensive pretraining.

---

### 2. RWKV

**Architecture:** RWKV (pronounced "RWaKuV") is an **attention-free, RNN-Transformer hybrid** developed by Bo Peng (BlinkDL) as an open-source project under the Linux Foundation (joined September 2023). It combines:
- **Transformer-style parallelizable training** (processes sequences in parallel using time-decay mechanisms)
- **RNN-style constant-memory inference** (fixed-size recurrent state, no KV cache)
- Four learned parameters per block: R (receptance), W (weight/decay), K (key), V (value)

RWKV achieves GPT-level performance with O(1) memory and O(n) compute at inference time. The latest **RWKV-7** (2024–2025) improves on earlier versions with refined gating mechanisms. The community has also released **RWKV-X**, a hybrid that adds sparse attention layers for long-range context capture while preserving linear complexity for most tokens.

**Ecosystem:** Strong open-source community; active development on multiple scales (1.5B to 14B+ parameters); runs efficiently on CPU without GPU. Used in research and hobbyist deployments. The project is community-funded with compute support from Linux Foundation partners.

**Positioning vs. Liquid AI:** RWKV is philosophically similar to Liquid (non-transformer, recurrent, efficient inference) but is a research community project rather than a commercial product with deployment infrastructure, enterprise support, or hardware-co-design. Liquid's STAR system gives it a systematic process for generating hardware-optimal variants; RWKV relies on human architecture iteration. RWKV's open-source nature commoditizes the "efficient recurrent model" concept, potentially undercutting Liquid's architectural differentiation for developers who can self-host.

---

### 3. AI21 Labs — Jamba

**Architecture:** [Jamba](https://www.ai21.com/research/jamba-a-hybrid-transformer-mamba-language-model/) is a **hybrid Transformer-Mamba-MoE architecture** developed by AI21 Labs. It interleaves Transformer attention blocks with Mamba SSM blocks in a configurable ratio, with Mixture-of-Experts (MoE) layers in select positions. This design:
- Retains attention's *strong local pattern recognition* for precision-critical contexts
- Uses Mamba's *linear-time recurrence* for long-context efficiency
- Uses MoE to increase model capacity while keeping active parameters manageable
- Achieves 256K context window in a single 80GB GPU (unprecedented for its generation)

**Models (Jamba family):**
- **Jamba (2024)**: First large-scale hybrid SSM-Transformer, 52B total / ~12B active parameters
- [**Jamba 1.5 Mini & Large (2024)**](https://www.ai21.com/blog/announcing-jamba-model-family/): "First time a non-Transformer model successfully scaled to frontier quality"—256K context, 2.5× faster than transformer equivalents on long contexts
- **Jamba2 Mini (12B active) & Jamba2 3B**: Production-grade, including a 3B on-device variant
- All models available under Jamba Open Model License

**Performance claims:** Jamba 1.5 outperforms comparable-size models across benchmarks, with particular advantages at 100K+ context lengths where transformer memory costs become prohibitive. It is the only open model with production-grade 256K context.

**Positioning vs. Liquid AI:** AI21 Jamba is a more direct architectural competitor than most:
- Both achieve sub-quadratic inference complexity
- Jamba's 3B on-device variant explicitly targets the same edge/constrained-hardware segment as Liquid's LFMs
- Key distinction: Jamba is a *hybrid*—it retains transformer attention blocks, accepting some quadratic overhead to maintain attention's precision advantages. Liquid's LFM/STAR approach eschews attention entirely in favor of fully recurrent ODE-derived primitives
- AI21 is a well-capitalized company (raised $140M+, backed by Google, NVIDIA, Intel); Jamba is backed by a substantial R&D budget
- Jamba's enterprise focus (AI21's Studio API, Bedrock/Azure availability) gives it enterprise distribution that Liquid is still building

---

### 4. Mistral — Codestral Mamba

**Architecture:** [Codestral Mamba](https://mistral.ai/news/codestral-mamba/) (July 2024) is a **pure Mamba-2 architecture** (7B parameters) specialized for code generation. Developed with input from Albert Gu and Tri Dao (Mamba co-authors). Key properties:
- Linear-time inference, O(n) vs. O(n²)
- Theoretical infinite context length (practical 256K token window tested)
- Designed specifically to handle long code files where transformer KV-cache memory becomes prohibitive
- Free use, modification, and distribution (research-permissive license)

**Performance:** Benchmarks show Codestral Mamba frequently matching or outperforming 22B and 34B transformer models on coding tasks—i.e., 3–4× more efficient parameter utilization through architectural advantage. The [HuggingFace community noted](https://www.reddit.com/r/LocalLLaMA/comments/1e4qgoc/mistralaimambacodestral7bv01_hugging_face/) that linear-time inference plus 256K context is "a coding model with functionally infinite linear attention."

**Status:** This is a research/architecture exploration release from Mistral, not their primary commercial product. Mistral's main commercial line (Mistral Large, Codestral) remains transformer-based.

**Positioning vs. Liquid AI:** Codestral Mamba validates the production viability of pure-SSM architectures at 7B scale, but it is a single point release rather than a sustained edge AI strategy. Mistral's broader commercial focus remains transformer-based cloud models. The significance is that Mistral's participation in SSM research legitimizes the architecture class—and Mistral's distribution (Mistral API, Azure, Google Cloud) means SSMs now have enterprise-grade distribution pipelines that Liquid must compete with.

---

### 5. IBM — Granite 4.0

**Architecture:** [IBM Granite 4.0](https://www.ibm.com/new/announcements/ibm-granite-4-0-hyper-efficient-high-performance-hybrid-models) is a family of **hybrid Mamba-2/Transformer open-weight models** announced in 2025, available under Apache 2.0. The architecture interleaves Mamba-2 SSM blocks with transformer attention blocks (similar in spirit to Jamba but with IBM's enterprise-specific engineering choices). Key design goals: reduce memory requirements on long sequences without sacrificing benchmark performance, enabling deployment on "significantly cheaper GPUs."

**Model sizes:** Dense variants at 1B, 4B, 7B, 9B, 32B; a Granite 4.0 Tiny Preview (sub-1B) specifically designed for constrained deployment with FP8 support for concurrent sessions.

**IBM's framing:** [IBM's internal architecture explainer](https://www.ibm.com/think/news/hybrid-thinking-inside-architecture-granite-4-0) positions this as a direct response to the "AI has a scale problem"—transformer's quadratic attention drives "spiraling compute bills" that IBM is solving for enterprise customers with hybrid SSMs.

**Certifications:** First open models to receive ISO 42001 AI management certification; cryptographically signed for supply-chain integrity—enterprise governance differentiators that are highly relevant in regulated industries.

**Positioning vs. Liquid AI:** IBM's entry into hybrid SSM models is significant because IBM brings enterprise distribution (Watson.x.ai, AWS/Azure/Dell/Lenovo partnerships), regulatory certification credibility, and a large installed base of enterprise customers. Granite 4.0 directly competes with LFM2.5 in the "small, efficient, enterprise-grade" segment. IBM's Apache 2.0 licensing and ISO certification may be more commercially compelling for conservative enterprise buyers than Liquid's proprietary architecture, even if Liquid's efficiency benchmarks are stronger. IBM's hybrid approach (keeping attention layers) may lag behind Liquid's fully recurrent STAR models in raw efficiency but offers better out-of-the-box quality on precision-sensitive tasks.

---

### 6. Sakana AI

**Architecture & Research:** Tokyo-based Sakana AI (founded 2023 by former Google Brain researchers Llion Jones, Ren Ito, and CEO David Ha) focuses on **nature-inspired AI architectures and evolutionary model development**. Their headline research is **Evolutionary Model Merge**—using evolutionary algorithms to automatically discover optimal *merging recipes* across both weight space and data flow space, enabling cross-domain capabilities (e.g., a Japanese LLM with math reasoning created by merging specialist models). The paper was published in [Nature Machine Intelligence](https://sakana.ai/evolutionary-model-merge/) (January 2025) and integrated into mergekit and Optuna Hub.

**Recursive Self-Improvement (RSI):** Sakana's current R&D direction is [open-ended adaptive architectures that self-improve](https://sakana.ai/rsi-lab/)—AI systems that iteratively redesign themselves, inspired by biological evolution.

**Funding:** Series A (~$200M) led by NEA, Khosla Ventures, Lux Capital; [Series B of $135M](https://techcrunch.com/2025/11/17/sakana-ai-raises-135m-series-b-at-a-2-65b-valuation-to-continue-building-ai-models-for-japan/) at $2.65B valuation (November 2025), with Mitsubishi UFJ, MUFG, Khosla, Macquarie, In-Q-Tel. Total raised: ~$379M from 37 investors.

**Edge relevance:** Sakana's evolutionary merging directly addresses the on-device constraint: by creating highly specialized compact models through merging, they can achieve edge-deployable quality without large-scale pretraining compute. The Japan focus (Japanese language, Japanese enterprise partnerships with Daiwa, MUFG, NTT) is a geographic differentiation.

**Positioning vs. Liquid AI:** Sakana's evolutionary approach is philosophically parallel to Liquid's STAR but at the *model composition* level rather than the *architecture design* level. STAR evolves architectures; Sakana's evolutionary merge evolves *weight combinations across existing models*. Sakana is well-capitalized ($379M), has strong Japan-market traction, and has research credibility (Nature Machine Intelligence) that rivals Liquid's academic pedigree. On edge deployment specifically, Sakana's merged compact models and Liquid's STAR-synthesized LFMs are targeting the same performance/efficiency frontier via different paths.

---

### 7. xAI & Together AI — Small Model Offerings

**xAI:** Elon Musk's xAI (Grok models) remains primarily focused on large frontier models (Grok-3, Grok-3-mini) for cloud API consumption. There is no public evidence of a dedicated edge/on-device inference strategy or novel non-transformer architecture. Grok-3-mini is a capable reasoning model but architecturally transformer-standard. Not a direct competitor in the edge AI or non-transformer space.

**Together AI:** Together AI positions as an ["AI Native Cloud"](https://www.together.ai) for inference, model shaping, and pretraining. Key claims: 2× faster inference than cloud alternatives, 60% cost reduction, 90% faster pretraining via Together Kernel Collection. Together's focus is cloud-scale research infrastructure—the opposite end of the deployment spectrum from edge. Their small-model API access (serving open-source models like Llama, Mistral, Qwen) is relevant to developers but Together itself does not build or architect models. **Neither xAI nor Together AI represents a meaningful non-transformer or edge-AI architectural threat to Liquid.**

---

## Comparative Architecture Summary

| Company | Architecture Type | Transformer-Free? | Edge-First? | Hardware Co-Design (STAR-like)? | Key Model Sizes |
|---|---|---|---|---|---|
| **Liquid AI** | LFM (ODE/recurrent via STAR) | Yes | Yes | Yes (STAR) | 1.2B, 8B, 40B |
| **Cartesia AI** | Mamba/SSM | Yes | Yes | No | TTS (undisclosed) |
| **AI21 Jamba** | Hybrid Mamba+Transformer+MoE | Partial | No (cloud-first) | No | 3B, 12B active, 52B total |
| **IBM Granite 4.0** | Hybrid Mamba-2+Transformer | Partial | Partial (Tiny) | No | 1B–32B |
| **Mistral Codestral Mamba** | Pure Mamba-2 | Yes | No | No | 7B |
| **RWKV** | Attention-free RNN hybrid | Yes | Yes (CPU-native) | No | 1.5B–14B+ |
| **Sakana AI** | Evolutionary merge (transformer base) | No | Partial | Yes (evolutionary) | Various compact |
| **Arcee AI** | Transformer (distilled/merged) | No | Partial (AFM-4.5B) | No | 4.5B–70B |
| **Cactus Compute** | Runtime (any model) | N/A | Yes (mobile) | No | Runtime only |
| **Nexa AI** | Runtime + compression | N/A | Yes (NPU/IoT) | No | Runtime only |
| **webAI** | Deployment platform | N/A | Yes (sovereign) | No | Platform only |
| **Qualcomm AI Hub** | Hardware platform (175+ models) | N/A | Yes (Snapdragon) | Partial (compile-optimize) | 175+ models |
| **FastFlowLM** | NPU runtime | N/A | Yes (NPU-first) | No | Runtime only |

---

## Strategic Takeaways for Liquid AI Positioning

**Where Liquid's moat is strongest:**
1. **STAR hardware co-design** is genuinely unique — no competitor has a publicly described equivalent automated architecture synthesis system that produces *different architectures for different hardware targets*. Cartesia hand-designs SSMs; Jamba/Granite hand-pick hybrid ratios; Sakana merges existing weights. Liquid automates the discovery.
2. **Fully recurrent, fully transformer-free at scale** — Jamba and Granite retain attention blocks. Liquid's LFMs (RWKV and Cartesia being the closest comparables) are fully non-transformer, delivering stronger asymptotic efficiency gains at inference.
3. **Multimodal edge (text + audio + vision in 1.2B)** — The LFM2.5-1.2B with audio (8× faster than predecessor), VLM, and language variants in one compact family has no direct analog among non-transformer competitors at this stage.

**Where Liquid faces serious competition:**
1. **Distribution & ecosystem**: Arcee (mergekit community), Hugging Face (2M+ models), IBM Granite (enterprise channels, Apache 2.0), AI21 Jamba (AWS Bedrock, Azure). Liquid's open-weight presence on HuggingFace is good but its developer ecosystem is nascent.
2. **Enterprise trust signals**: IBM's ISO 42001, Arcee's Microsoft (M12) backing, AI21's Google/NVIDIA investment. Liquid needs comparable enterprise-grade certification and partnership credibility.
3. **Voice AI specifically**: Cartesia's Sonic 3.5 (sub-90ms, 42 languages, ranked #1 naturalness) is better-positioned commercially in voice than Liquid's audio models today. Cartesia has a focused product with clear enterprise buying motion; Liquid's audio story is newer.
4. **Runtime commoditization risk**: FastFlowLM and Cactus Compute demonstrate that aggressive runtime optimization (10× efficiency gains on NPUs via tile-structured kernels) can approach architecture-level efficiency advantages without novel architectures. If NPU runtimes mature faster than STAR models scale, Liquid's architectural advantage narrows.

---

*Sources: All inline citations link to primary sources. Research conducted June 2025.*
