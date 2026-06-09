# Big Tech On-Device AI: Competitive Landscape Analysis for Liquid AI
**Research Date: June 2026 | Audience: Liquid AI competitive strategy**

---

## Executive Summary

The on-device AI race has intensified dramatically in 2025–2026. Every major platform owner — Apple, Google, Microsoft, Meta — now ships foundation models that run locally on consumer silicon. All of them, however, share a critical common trait: they are fundamentally transformer-derived architectures, optimized through quantization and distillation rather than rethought from first principles. This is Liquid AI's structural differentiator. LFM2 and LFM2.5 are the only production on-device models built via automated hardware-in-the-loop architecture search (STAR), combining gated short convolutions with sparse attention — a design chosen not by human intuition but by evolutionary optimization against real-device latency and memory budgets.

---

## 1. Apple Intelligence / Apple Foundation Models (AFM)

### Architecture and Model Family

Apple shipped three generations of on-device foundation models from 2024 to 2026. As of June 8, 2026, the [third generation (AFM 3)](https://machinelearning.apple.com/research/introducing-third-generation-of-apple-foundation-models) comprises five purpose-built models, two of which run on-device:

| Model | Type | Parameters | Active Params | Architecture |
|---|---|---|---|---|
| **AFM 3 Core** | On-device | ~3B | 3B (dense) | Transformer dense, KV-cache sharing, 2-bit QAT |
| **AFM 3 Core Advanced** | On-device | 20B | 1–4B per prompt | Sparse MoE, Instruction-Following Pruning, flash-loaded |
| AFM 3 Cloud | Server (PCC) | Undisclosed | — | PT-MoE transformer |
| ADM 3 Cloud | Server image | Undisclosed | — | Diffusion + MoE hybrid |
| AFM 3 Cloud Pro | Server (PCC + NVIDIA) | Undisclosed | — | PT-MoE on NVIDIA GPUs |

The first-generation on-device model (~3B, dense transformer) was introduced at [WWDC 2024](https://machinelearning.apple.com/research/introducing-apple-foundation-models) with a novel KV-cache sharing scheme that reduced KV-cache memory by 37.5%, combined with a mixed 2-bit/4-bit quantization-aware training (QAT) strategy averaging 3.7 bits-per-weight. The [2025 update](https://machinelearning.apple.com/research/apple-foundation-models-2025-updates) introduced a 3B model with improved multimodal tool-use and structured a 5:3 depth-ratio block split that further cut KV-cache overhead.

The headline innovation in AFM 3 is **AFM 3 Core Advanced**: a 20B-parameter sparse model that stores its full weights in flash (NAND), loading only the relevant expert subset into DRAM per prompt. Unlike standard MoE models that route per-token (requiring continuous NAND-to-DRAM swaps), Apple's **Instruction-Following Pruning (IFP)** makes routing decisions at the prompt level, fixing a set of experts for the entire generation pass. This dramatically reduces memory bandwidth pressure. The result: a 20B model activating only 1–4B parameters at a time, running on Apple silicon without cloud calls for the first time at this scale.

### Hardware Constraints and Targets

- **AFM 3 Core**: iPhone 16 / M2 Mac and above; runs via Neural Engine and Apple Neural Engine on A16/A17/M-series chips
- **AFM 3 Core Advanced**: Requires "most capable Apple silicon systems" — iPhone 16 Pro / M3 Pro or better; flash-storage bandwidth is the key constraint, not DRAM

### Developer Access

Apple opened developer access to the on-device LLM through the [**Foundation Models framework**](https://developer.apple.com/documentation/FoundationModels), shipped with iOS 26 / macOS 26 (September 2025). The Swift API provides text generation, guided generation (type-safe structured output via `@Generable`), streaming, tool-calling, and session context management. Crucially, inference is **free of cost** and works offline — [Apple's newsroom](https://www.apple.com/newsroom/2025/09/apples-foundation-models-framework-unlocks-new-intelligent-app-experiences/) emphasized that users' data stays on-device and no API fees apply. Developer access is limited to the ~3B Core model; AFM 3 Core Advanced is not directly accessible via Foundation Models framework.

### Key Differentials vs. Liquid AI

Apple's approach is "efficiency by compression" — take a transformer, apply KV-cache sharing, then quantize aggressively to 2-bit with QAT. AFM 3 Core Advanced adds MoE sparsity on top, but it is still a transformer-derived architecture extended with pruning heuristics. The architecture was **not discovered via automated hardware-in-the-loop search**; it was hand-engineered by Apple researchers. Apple's models are also **entirely proprietary and closed-weight**, locked to Apple hardware. Liquid AI's LFM2, by contrast, ships open weights, runs on any CPU/GPU/NPU (AMD, Qualcomm, Arm), and the architecture itself was **selected by the STAR evolutionary engine** optimizing directly against Snapdragon and Ryzen hardware targets.

---

## 2. Google Gemini Nano & Gemma 3n

### Gemini Nano: Architecture and Deployment History

Google's on-device model strategy bifurcates into **Gemini Nano** (system-level, proprietary, locked to AICore) and **Gemma** (open-weight, developer-accessible). Gemini Nano runs as a system service via [Android AICore](https://developer.android.com/ai/gemini-nano), which acts as a sandboxed inference daemon updated independently of the OS.

Current Gemini Nano variants as of mid-2026:

| Variant | Parameters | Quantization | Memory | Hardware |
|---|---|---|---|---|
| **Nano-1** | 1.8B | 4-bit | ~1GB | Pixel 8 Pro, lower-end devices |
| **Nano-2** | 3.25B | 4-bit | ~1GB | Pixel 9 series, Galaxy S24/S25 |
| **Nano v3** | Not publicly disclosed | TBD | Requires 12GB+ RAM | Pixel 10, flagships with Gemini Intelligence |

[Gemini Intelligence](https://9to5google.com/2026/05/15/gemini-intelligence-android-spec-requirements/), Google's Android AI tier announced in mid-2026, requires Nano v3, a flagship chip, and **12GB or more of RAM** — raising the bar for which devices qualify. Notably, Pixel 9 devices were left out of Gemini Intelligence despite shipping with Nano v2.

Developer access flows through [ML Kit GenAI APIs](https://developer.android.com/ai/gemini-nano), which provide high-level endpoints (summarization, proofreading, rewriting, image captioning) built on top of AICore. Direct prompt access to the base Nano model has been in experimental status; [community comparisons](https://www.reddit.com/r/apple/comments/1ntkp0e/apples_foundation_models_framework_unlocks_new/) noted that Apple's Foundation Models API was more openly accessible on launch than Gemini Nano's developer API.

### Gemma 3n: The Edge-First Open Model

[Gemma 3n](https://developers.googleblog.com/introducing-gemma-3n/) (preview: May 20, 2025; full release: June 2025) is Google's explicit answer to on-device open-weight deployment. It introduces two novel architectural techniques:

1. **MatFormer (Matryoshka Transformer)**: A nested architecture that allows a single trained model to activate different parameter subsets. The E4B model (8B total parameters, 4B active) contains a nested 2B-active submodel, and "mix'n'match" enables dynamically composed submodels mid-deployment.

2. **Per-Layer Embeddings (PLE)**: A Google DeepMind technique that caches embedding parameters separately on slower storage (analogous in spirit to Apple's flash-loading), dramatically reducing the DRAM footprint for a given parameter count.

| Model | Total Params | Active Params | Memory Footprint | Modalities |
|---|---|---|---|---|
| **Gemma 3n E2B** | ~5B | 2B effective | ~2GB | Text, image, audio, video |
| **Gemma 3n E4B** | ~8B | 4B effective | ~3GB | Text, image, audio, video |

[Gemma 3n E4B became the first sub-10B model to exceed 1300 on the LMArena benchmark](https://www.reddit.com/r/machinelearningnews/comments/1llmkgq/google_ai_releases_gemma_3n_a_compact_multimodal/). It starts responding approximately **1.5× faster on mobile** than Gemma 3 4B. Hardware partners include Qualcomm Technologies, MediaTek, and Samsung System LSI — the same trio that partnered with Meta for Llama 3.2. Gemma 3n is deployed via Google AI Edge, compatible with Android and Chrome.

### Key Differentials vs. Liquid AI

Gemma 3n's MatFormer is genuinely architecturally innovative — more so than most competitors — but it is still a transformer. The MatFormer nesting is a post-hoc training trick applied to an otherwise standard transformer backbone; Gemma 3n's architecture was not **discovered** via automated search against hardware constraints. LFM2's STAR system, by contrast, explicitly measured latency and peak memory on Snapdragon 8 Gen 3 and AMD Ryzen AI during architecture selection. Gemma 3n also requires more memory (2–3GB active RAM) than LFM2's sub-1GB footprint for comparable quality tiers, and Google's Gemini Nano remains a closed-weight, platform-locked model unavailable for fine-tuning or third-party deployment.

---

## 3. Microsoft Phi Family

### Model Lineage and Current State

Microsoft's Phi family is the most prolific and developer-facing of any big-tech SLM program, spanning [Phi-3 (2024)](https://www.microsoft.com/en-us/research/publication/phi-4-technical-report/) through Phi-4 and its variants (2025). All models are released under the **MIT License** (open source), available on Azure AI Foundry, Hugging Face, and Ollama.

| Model | Params | Architecture | Context | Key Capability |
|---|---|---|---|---|
| **Phi-3-mini** | 3.8B | Dense decoder transformer, GQA | 128K | Baseline SLM, first Phi on NPU |
| **Phi-3.5-mini** | 3.8B | Dense transformer | 128K | Multilingual, improved instruct |
| **Phi-4** | 14B | Dense decoder transformer | 128K | Reasoning-focused; math/coding |
| **Phi-4-mini** | 3.8B | Dense decoder-only transformer, GQA, 200K vocabulary | 128K | Text reasoning, function-calling, on-device target |
| **Phi-4-multimodal** | 5.6B | Transformer backbone (Phi-4-mini) + vision/speech encoders via LoRA | 128K | Speech, vision, text; omni-modal |
| **Phi-4-reasoning** | 14B | Dense transformer + RL fine-tuning | 128K | Extended reasoning (chain-of-thought) |
| **Phi-4-mini-reasoning** | 3.8B | As Phi-4-mini + RL | 128K | Mobile/edge reasoning |

[Phi-4-mini](https://azure.microsoft.com/en-us/blog/empowering-innovation-the-next-generation-of-the-phi-family/) (released February 26, 2025) is a 3.8B parameter dense decoder-only transformer with grouped-query attention (GQA) and a 200K-token vocabulary for multilingual support. It supports sequences up to 128K tokens and achieves competitive performance against models twice its size on math and coding. On reasoning benchmarks, [Phi-4-reasoning outperforms DeepSeek-R1 distill variants and Claude 3.7 Sonnet](https://windowsforum.com/threads/microsofts-phi-4-reasoning-models-small-efficient-ai-revolution-for-windows.364245/).

[Phi-4-multimodal](https://arxiv.org/abs/2503.01743) (5.6B) uses a novel modality extension approach: LoRA adapters and modality-specific routers are added on top of the Phi-4-mini language backbone, allowing speech, vision, and text to be processed in a single model. The speech LoRA component alone has only 460M parameters. It ranked **#1 on the HuggingFace OpenASR leaderboard** with a 6.14% word error rate (as of March 2025), outperforming WhisperV3 and SeamlessM4T-v2-Large.

### Copilot+ PC Integration

Microsoft has integrated Phi models directly into Windows via **Copilot+ PC** hardware requirements (>40 TOPS NPU) and the **Windows Copilot Runtime**. A special variant, **Phi-Silica** (3.3B), is a Copilot+ PC-specific model optimized for the Snapdragon X NPU. Phi-4-mini runs via ONNX Runtime with DirectML (Windows GPU backend) or Intel OpenVINO for NPU execution on Intel AI PCs. [Intel benchmarks show 1,955 tokens/s throughput on Xeon 6 for Phi-4-mini](https://www.intel.com/content/www/us/en/developer/articles/technical/accelerate-microsoft-phi-4-small-language-models.html) at BF16 precision.

### Key Differentials vs. Liquid AI

Microsoft's Phi models are consistently described in their own technical reports as "minimal changes to Phi-3 architecture" — the quality gains come from superior data curation and training curriculum, not architectural innovation. Phi-4-mini is a standard dense transformer, Phi-4-multimodal is that transformer plus bolt-on encoders via LoRA. Microsoft's route to efficiency is "better data, same architecture." Liquid AI's route is "better architecture, hardware-optimized by design." The practical consequence: Phi-4-mini at 3.8B needs substantially more memory and runs slower on CPU than LFM2-1.2B at comparable quality levels. LFM2-1.2B achieves **1.5–1.7× higher prefill throughput and 1.3–1.5× higher decode throughput** than Llama-3.2-1B on the Galaxy S25 ([LFM2 Technical Report](https://arxiv.org/html/2511.23404v1)), and at 3.8B Phi-4-mini would face even steeper CPU latency penalties on mobile hardware.

---

## 4. Meta Llama On-Device

### Llama 3.2: The On-Device Entry

Meta's explicit on-device strategy began with [Llama 3.2](https://ai.meta.com/blog/llama-3-2-connect-2024-vision-edge-mobile-devices/) (September 2024), which introduced 1B and 3B text-only models alongside 11B and 90B vision models. The 1B and 3B models support 128K token context, are built on the standard Llama transformer (GQA, RoPE) and were derived from larger models via **pruning and distillation** from Llama 3.1 8B/70B.

The key on-device runtime is **PyTorch ExecuTorch**, with day-one optimization for Qualcomm Snapdragon and MediaTek SoCs. Arm CPU Kleidi AI kernels were used for optimized mobile CPU inference.

In October 2024, Meta released [quantized Llama 3.2 1B and 3B models](https://ai.meta.com/blog/meta-llama-quantized-lightweight-models/) using two complementary techniques:

- **QLoRA (Quantization-Aware Training + LoRA)**: 4-bit groupwise quantization (group size 32) for linear layers, 8-bit per-token dynamic quantization for activations, preserving accuracy through QAT
- **SpinQuant**: A post-training quantization method for maximum portability

Results: **2–4× inference speedup**, **56% average reduction in model size**, **41% average memory reduction** versus BF16 baseline. The 1B SpinQuant model went from 2,858 MB to 438 MB (for Llama Guard 3 1B).

### Llama 4: Not an On-Device Story (Yet)

[Llama 4 Scout](https://ai.meta.com/blog/llama-4-multimodal-intelligence/) (released April 2025) has 17B active parameters across 16 experts (109B total), requiring **at minimum a single H100 GPU** even with aggressive quantization. Community quantization from Unsloth compressed Scout from 115GB to 33.8GB, requiring 20GB+ RAM to run. [Analysis of Llama 4 for edge deployment](https://www.embedl.com/knowledge/llama-4-on-the-edge-overcoming-limitations-of-deploying-mixture-of-experts-on-edge-devices) concluded the MoE architecture creates significant challenges for constrained edge devices — large routing overhead, non-contiguous memory access patterns, and high minimum memory requirements. Llama 4 is **not an on-device model**; Meta's on-device story remains Llama 3.2 1B/3B.

### Meta's On-Device Ecosystem Strategy

Meta's distribution approach is the most open: Llama models are downloadable from llama.com and Hugging Face under the [Llama Community License](https://huggingface.co/meta-llama/Llama-3.2-1B) (commercial use permitted for companies under 700M monthly users; larger companies require a separate agreement). The on-device stack covers iOS (ExecuTorch), Android (Qualcomm/MediaTek SoCs), and local deployment via Ollama. Partners include Arm, Qualcomm, MediaTek, AWS, Google Cloud, Microsoft Azure, and 25+ others at launch.

### Key Differentials vs. Liquid AI

Llama 3.2 1B/3B are distilled, pruned, then quantized transformers — the archetypal "efficiency by compression" pipeline. LFM2-1.2B, benchmarked directly against Llama-3.2-1B, delivers **1.5–1.7× faster prefill and 1.3–1.5× faster decode** on the Galaxy S25, while matching or exceeding it on MMLU, GSM8K, and IFEval benchmarks ([LFM2 Technical Report](https://arxiv.org/html/2511.23404v1)). The LFM2.5-1.2B-Thinking reasoning model [outperforms Llama 3.2 1B Instruct on all evaluated benchmarks](https://www.deeplearning.ai/the-batch/liquid-ais-small-reasoning-model-mixes-attention-with-convolutional-layers-for-efficiency) while running roughly twice as fast and using ~45% less memory. Critically, Liquid AI's approach to quantization uses QAT at higher precision rather than post-training quantization, so quantized models exhibit very little accuracy loss — a structural quality advantage.

---

## 5. Amazon Nova Micro and the Edge Story

### What Amazon Actually Ships for Edge

Amazon's Nova family, [introduced at AWS re:Invent December 2024](https://www.amazon.science/publications/the-amazon-nova-family-of-models-technical-report-and-model-card), positions **Nova Micro** as its lowest-latency, most cost-efficient text model. Nova Micro is text-only, optimized for speed and low cost — but it is a **cloud-hosted API model** running on AWS Bedrock, not a model designed for deployment on consumer devices. The Nova 2 generation (launched late 2025) extended the family with Nova 2 Lite, Nova 2 Pro, and Nova 2 Sonic (speech-to-speech), all cloud-hosted.

Amazon's edge strategy at AWS re:Invent 2025 focused on **Strands Agents SDK** with [general availability of edge device support for the Strands framework](https://www.aboutamazon.com/news/aws/aws-re-invent-2025-ai-news-updates), enabling agentic AI workflows on small-scale devices in automotive, gaming, and robotics — but powered by **third-party local models** (Ollama, llama.cpp) rather than Amazon-trained edge models. AWS SageMaker sessions discussed deploying fine-tuned SLMs from third parties to edge locations via Local Zones and Outposts.

**Conclusion**: Amazon does not have a competitive on-device foundation model for consumer or mobile edge deployment as of mid-2026. Nova Micro is a cost-optimized cloud API. Amazon's edge AI play is infrastructure and tooling (Bedrock, SageMaker, Greengrass, Strands) rather than model development. This is not a direct competitive threat to Liquid AI's on-device model segment.

---

## 6. Liquid AI LFM2 / LFM2.5: Architecture and Competitive Position

### What Makes LFM2 Structurally Different

Liquid AI is an MIT spinout (AMD-backed, $2.3B valuation, $250M Series A December 2024) whose core thesis is that transformer architecture is not optimal for edge deployment — and that the right architecture should be **discovered via hardware-in-the-loop automated search rather than hand-engineered**.

**STAR (Synthesis of Tailored Architectures)**: [Published at ICLR 2025 (oral)](https://www.liquid.ai/research/automated-architecture-synthesis-via-targeted-evolution), STAR represents model architectures as numerical "genomes" encoding Linear Input-Varying (LIV) system configurations. An evolutionary algorithm iterates over hundreds of candidate architectures, compiling each to target hardware (Snapdragon 8 Gen 3, AMD Ryzen AI) and measuring actual latency and peak memory. After 20–24 generations, STAR converges on a minimal hybrid: **10 gated short convolution blocks + 6 GQA blocks** in a 16-layer design.

**LFM2 architecture** ([Technical Report, Nov 2025](https://arxiv.org/html/2511.23404v1)):
- Dense variants: 350M, 700M, 1.2B, 2.6B parameters; 32K context window
- MoE variant: LFM2-8B-A1B (8.3B total, **1.5B active parameters**, 3–4B-class quality)
- 16 layers: 10 double-gated short-range convolution blocks (fast, localized, low memory) + 6 GQA blocks
- Pre-trained on 10–12T tokens; mid-training on 1T high-quality long-context tokens
- Post-training: 3-stage pipeline with RL; quantization-aware training for 8da4w and Q4_0 quantization
- Runtimes: llama.cpp, ExecuTorch, Transformers, vLLM (open weights on HuggingFace)

**LFM2.5** ([announced January 2026](https://www.liquid.ai/blog/introducing-lfm2-5-the-next-generation-of-on-device-ai)): Extended pretraining from 10T to 28T tokens, significantly scaled RL post-training pipeline. Text + vision (LFM2.5-VL-1.6B, improved multi-image and multilingual) + audio (LFM2.5-Audio, 8× faster detokenizer via QAT INT4, llama.cpp-compatible GGUFs).

**LFM2.5-1.2B-Thinking** ([February 2026](https://www.liquid.ai/blog/lfm2-5-1-2b-thinking-on-device-reasoning-under-1gb)): Reasoning model, **fits in 900MB**, runs on AMD Ryzen NPUs at ~52 tok/s at 16K context and ~46 tok/s at full 32K context. Matches or exceeds Qwen3-1.7B on most reasoning benchmarks with 40% fewer parameters.

### Benchmark Summary: LFM2 vs. Competitors

| Metric | LFM2-1.2B | Llama 3.2 1B | Gemma 3 1B | Qwen3-1.7B |
|---|---|---|---|---|
| Prefill @ 1K (S25) | 335 tok/s | 229 tok/s | ~450 tok/s | 140 tok/s |
| Decode @ 1K (S25) | 69.8 tok/s | 54.6 tok/s | ~80 tok/s | 39.7 tok/s |
| Decode @ 4K (S25) | 55.5 tok/s | 37.8 tok/s | ~65 tok/s | 26.9 tok/s |
| MMLU quality | Competitive w/ Qwen3-1.7B | Below LFM2-1.2B | Below LFM2-1.2B | Reference |
| Memory (approx.) | ~900MB (quantized) | ~1.2GB | ~1.1GB | ~1.5GB |

*Sources: [LFM2 Technical Report](https://arxiv.org/html/2511.23404v1), benchmarks on Samsung Galaxy S25 (Snapdragon 8 Elite), llama.cpp Q4_0.*

**Note**: Gemma-3-1B has higher raw throughput than LFM2-1.2B on prefill (LFM2-1.2B reaches 0.75–0.9× of Gemma-3-1B's prefill), but LFM2-1.2B offers higher quality at the 1B scale, making it Pareto-superior on the quality-per-compute frontier.

---

## 7. Cross-Competitor Strategic Analysis

### Architecture Taxonomy

| Company | On-Device Model | Architecture Class | Route to Efficiency |
|---|---|---|---|
| **Apple** | AFM 3 Core / Core Advanced | Dense transformer + sparse MoE (IFP) | KV-cache sharing, QAT 2-bit, flash-loading |
| **Google** | Gemini Nano v3 / Gemma 3n | MatFormer (nested transformer) + PLE | Nested submodels, PLE caching, 4-bit quant |
| **Microsoft** | Phi-4-mini / Phi-Silica | Dense decoder transformer, GQA | Better training data ("textbooks"), ONNX quant |
| **Meta** | Llama 3.2 1B/3B | Pruned/distilled dense transformer | Pruning + distillation + QLoRA/SpinQuant |
| **Amazon** | Nova Micro (cloud only) | Cloud transformer (no on-device) | Cloud API, not an edge model |
| **Liquid AI** | LFM2 / LFM2.5 | Hybrid: gated conv + GQA (STAR-selected) | Architecture search, hardware-in-the-loop, QAT |

### Key Competitive Observations

1. **Every major competitor is a transformer or transformer variant.** Apple's AFM 3 Core Advanced adds flash-loaded MoE; Google's Gemma 3n adds MatFormer nesting; Microsoft's Phi-4-mini adds GQA and a large vocabulary. None departed from the attention-mechanism foundation. Liquid AI is the only production on-device model family not derived from the transformer paradigm.

2. **"Efficiency by compression" vs. "efficiency by design."** The industry pattern is: train a large transformer → prune/distill to smaller size → quantize to 4-bit or lower → deploy. Liquid AI's inversion of this process — discover the most hardware-efficient architecture first, then train — means LFM2 models are not suffering residual quality losses from compression; the architecture itself is optimized for the hardware.

3. **Platform lock-in as a moat — and a ceiling.** Apple's Foundation Models framework gives Apple the strongest developer lock-in (free, offline, private, pre-installed) but is iOS/macOS only. Gemini Nano is Android-only, gated behind AICore. Microsoft's Phi models run broadly (Windows, Linux, mobile via ONNX) but without deep OS integration. Liquid AI targets all platforms simultaneously — AMD, Qualcomm, Arm CPUs, llama.cpp, ExecuTorch, ONNX — with no platform exclusivity.

4. **Gemini Nano remains a black box for developers.** Despite being on hundreds of millions of Android devices, direct developer API access to Gemini Nano is still experimental and restricted (requiring Pixel 9+ hardware and AICore APK sideloading as of early 2026). Apple's Foundation Models API launched with broad access on iOS 26. Liquid AI ships open weights day one.

5. **The MoE convergence.** Both Apple (AFM 3 Core Advanced) and LFM2-8B-A1B use sparse activation MoE to achieve higher quality within DRAM budgets. Apple's IFP approach routes at the prompt level; Liquid AI's activates 1.5B of 8.3B parameters per token. The conceptual similarity signals that the field has converged on sparse activation as a necessary technique for on-device quality scaling.

6. **Benchmark reliability concern.** LFM2's Artificial Analysis Intelligence Index score of 6/10 (below median for similarly-sized models) provides a counterpoint to Liquid AI's internal benchmark claims. Third-party holistic evals may penalize LFM2's verbosity (19M output tokens vs. a median of 7M). This is a commercial credibility issue that Liquid AI will need to address.

---

## 8. Licensing and Distribution Summary

| Company | License | Weight Access | Distribution Channels |
|---|---|---|---|
| Apple | Proprietary (closed) | No | iOS/macOS only, Foundation Models API |
| Google Nano | Proprietary (closed) | No | Android AICore, ML Kit GenAI APIs |
| Google Gemma 3n | Gemma ToS (permissive) | Yes | HuggingFace, Google AI Edge |
| Microsoft Phi-4 | MIT License | Yes | Azure AI Foundry, HuggingFace, Ollama |
| Meta Llama 3.2 | Llama Community License | Yes | llama.com, HuggingFace, 25+ partners |
| Liquid AI LFM2 | LFM Open License v1.0 | Yes | HuggingFace, llama.cpp, ExecuTorch, LM Studio |
| Amazon Nova | Proprietary | No | AWS Bedrock only (cloud) |

---

## 9. Implications for Liquid AI Strategy

1. **The architecture gap is real and measurable.** LFM2's 1.5–2× speed advantage over same-size transformers on CPU is reproducible across independent hardware. This should be the primary external message: not "we're a non-transformer" (opaque to buyers) but "we run 2× faster at the same quality, on the same phone, no cloud."

2. **Developer openness is a structural advantage vs. Apple/Google.** Apple's Foundation Models API restricts fine-tuning and limits to 3B Core; Gemini Nano is locked to Android/AICore. Liquid AI's open weights enable the same fine-tuning, RAG, and agentic workflows that enterprise customers want without platform dependency.

3. **The AMD partnership positions LFM2 for Copilot+ PC competition.** As Microsoft Phi-4 and Phi-Silica target Snapdragon X and Intel NPUs on Windows, LFM2 benchmarked on AMD Ryzen AI 9 HX 370 at 7,534 tok/s prefill at 1K context — the fastest result in that class. AMD's investment in Liquid AI creates a direct channel to Ryzen AI PC integration.

4. **Amazon is not a credible on-device competitor.** Nova Micro is a cloud model. AWS's edge strategy relies on third-party SLMs. This creates an opportunity for Liquid AI to position LFM as the preferred model for AWS IoT Greengrass / SageMaker edge deployments.

5. **Multimodal parity is needed.** Apple's AFM 3 Core Advanced is natively multimodal (audio, image, dictation). Gemma 3n handles audio, images, and video from launch. Phi-4-multimodal covers all three modalities. LFM2.5 now ships VL and Audio variants, but Liquid AI needs to accelerate VLM benchmark parity to compete in multimodal on-device agent use cases.

---

*Research compiled June 2026. Primary sources include [Apple Machine Learning Research](https://machinelearning.apple.com), [Google Developers Blog](https://developers.googleblog.com), [Microsoft Azure Blog](https://azure.microsoft.com/en-us/blog), [Meta AI Blog](https://ai.meta.com/blog), [Liquid AI Blog](https://www.liquid.ai/blog), and the [LFM2 Technical Report (arXiv:2511.23404)](https://arxiv.org/html/2511.23404v1).*
