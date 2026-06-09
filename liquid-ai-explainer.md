# Liquid AI, explained from scratch

A guide for a smart high schooler or undergrad. No prior ML background assumed — we'll build up the ideas, then show what makes Liquid different.

## The big picture in one paragraph

Liquid AI is a Boston startup, spun out of MIT, that builds a new kind of AI model designed to run *on your phone, car, or laptop* instead of in a giant data center. Most modern AI (ChatGPT, Gemini, Claude) is built on an architecture called a **transformer**, which is powerful but extremely memory- and energy-hungry. Liquid takes a fundamentally different approach inspired by how a tiny worm's brain works — using math that's leaner, faster, and built to run on small devices from day one. Their bet: the future of AI isn't one giant brain in the cloud, it's millions of small, specialized models running everywhere.

## Part 1: The history

### Where it came from: a worm with 302 neurons

The story starts at MIT's Computer Science and Artificial Intelligence Lab (CSAIL) with a researcher named Daniela Rus (the lab's director) and her students Ramin Hasani, Mathias Lechner, and Alexander Amini. They were studying *C. elegans*, a microscopic roundworm whose entire nervous system contains just **302 neurons** ([MIT News](https://news.mit.edu/2021/machine-learning-adapts-0128)).

Here's the puzzle that fascinated them: a worm with 302 neurons can navigate, find food, avoid danger, and react to brand-new situations. Meanwhile, a modern AI model has *billions* of artificial neurons and still gets confused by things it wasn't trained on. Something about how the worm's brain works is dramatically more efficient than how we build AI.

In 2020–2021, the team published a new kind of artificial neural network they called a **liquid neural network (LNN)**, also known as a "liquid time-constant network" ([Quanta Magazine](https://www.quantamagazine.org/researchers-discover-a-more-flexible-approach-to-machine-learning-20230207/)). The famous demo: a self-driving car steered by a liquid network with just **19 neurons** stayed in its lane better than a conventional deep network with thousands. Even more interesting, when the researchers checked what each network was "looking at," the conventional network was distracted by bushes and trees at the side of the road. The liquid network focused on the road's horizon and edges — "which is how people drive" ([Science News Explores](https://www.snexplores.org/article/the-brain-of-a-tiny-worm-inspired-a-new-type-of-ai-liquid-neural-network)).

### From research lab to company

In 2023, the four founders — Ramin Hasani (CEO), Mathias Lechner (CTO), Alexander Amini (Chief Scientist), and Daniela Rus — spun the research out into a company called **Liquid AI**. The timeline since:

- **December 2023:** $46.6M seed round.
- **October 2024:** First Liquid Foundation Models (LFMs) released — 1.3B, 3.1B, and 40B parameter sizes.
- **December 2024:** $250M Series A led by AMD at a $2.3B valuation ([Bloomberg](https://www.bloomberg.com/news/articles/2024-12-13/ai-startup-liquid-ai-set-to-raise-250-million-at-2-3-billion-valuation)). AMD became both their biggest investor and their key hardware partner.
- **July 2025:** Released **LFM2**, their open-weight second generation, which now has over 10 million downloads on Hugging Face ([Liquid AI](https://www.liquid.ai/blog/liquid-foundation-models-v2-our-second-series-of-generative-ai-models)).
- **2025–2026:** A wave of products — Liquid Nanos (task-specific tiny models), LFM2-VL (vision), LFM2-Audio (speech), and major customer wins including **Mercedes-Benz** (in-car AI starting in 2026 MBUX vehicles), **AMD** (Ryzen AI PC features), **Brilliant Labs** (Halo smart glasses), **Shopify**, and **Insilico Medicine** (drug discovery).

The company has roughly 121 employees as of 2026 and is headquartered in Cambridge, Massachusetts.

## Part 2: The problem they're trying to solve

To understand why Liquid is different, you have to understand the dominant approach first.

### How ChatGPT-style AI works today (the transformer)

Almost every famous AI model today — GPT-4, Claude, Gemini, Llama — is a **transformer**. Transformers were invented at Google in 2017, and their central trick is something called **attention**.

The intuition: when you read the sentence *"The chicken didn't cross the road because it was tired,"* you instantly know "it" refers to the chicken, not the road. The transformer's attention mechanism lets the model do this — for every word, it looks at every other word in the input and figures out which ones matter ([IBM](https://www.ibm.com/think/topics/attention-mechanism)).

This is incredibly powerful. It's why ChatGPT can write a coherent essay or hold a long conversation. **But it comes at a huge cost:**

1. **Quadratic compute.** If your input doubles in length, the work attention does roughly quadruples. A 10,000-word document is 100× more expensive to process than a 1,000-word document — not 10×.
2. **Memory hunger.** To make attention fast at inference time, transformers store a thing called a **KV cache** — basically a running memory of every token seen so far. The longer the conversation, the more RAM it eats. This is why running a big LLM on your phone melts the battery.
3. **Built for giant GPUs.** Transformers were designed for data centers stuffed with NVIDIA H100s. To run them on a phone, you have to **quantize** them — squeeze the numbers from high precision down to low precision, which loses accuracy and is fundamentally a workaround.

So the standard recipe today is: train a huge model in the cloud, then compress it down to fit a smaller device. Liquid's founders thought this was backwards.

### Liquid's bet: design for the device from day one

Liquid's slogan is **"efficiency by design, not compression"** ([Liquid AI](https://www.liquid.ai/blog/liquid-foundation-models-v2-our-second-series-of-generative-ai-models)). Instead of shrinking a cloud model down, what if you designed a new architecture from scratch that was lean, fast, and hardware-aware from the very first line of code?

## Part 3: How the technology actually works

### Liquid neural networks: neurons that flow

In a regular artificial neural network, each "neuron" is a simple thing — it multiplies its inputs by some learned weights, adds them up, and outputs a number. Once training is over, the weights are frozen.

A **liquid neuron** is different in three important ways ([Communications of the ACM](https://cacm.acm.org/news/how-liquid-networks-make-robots-smarter/)):

1. **Continuous time.** Instead of just spitting out a number, each neuron's behavior is governed by a *differential equation* — math that describes how something changes smoothly over time, like how water flows or how a pendulum swings. The neuron's state evolves continuously rather than jumping in discrete steps.
2. **Adaptable after training.** Traditional networks freeze their weights once training ends. Liquid networks can shift their internal dynamics in response to new inputs they see at runtime. This is the "liquid" part — they can flow and adapt.
3. **Richer connections.** The links between neurons aren't just single numbers (weights) — they're governed by functions inspired by how real biological synapses work.

The payoff: you can do complex tasks with a tiny number of neurons (remember: 19 neurons drove a car). And the network is **causal** — it learns the actual cause-and-effect of a task rather than memorizing surface patterns ([Communications of the ACM](https://cacm.acm.org/news/how-liquid-networks-make-robots-smarter/)).

That's the *origin story*. The production models today are a bit more pragmatic — let's look at them.

### LFM2: the practical hybrid

Liquid Foundation Models v2 (LFM2) — the models actually shipping in products today — are a **hybrid architecture**. They take the liquid-network philosophy of "do more with less" but combine two different kinds of building blocks ([Liquid AI](https://www.liquid.ai/blog/liquid-foundation-models-v2-our-second-series-of-generative-ai-models)):

| Block type | What it does | How many in LFM2 |
|---|---|---|
| **Short convolutions** (LIV) | Fast, memory-light pattern matching over nearby tokens. No big memory cache needed. | 10 (about 60%) |
| **Grouped query attention** (GQA) | Standard transformer-style attention for long-range reasoning, but trimmed down. | 6 (about 40%) |

So instead of attention doing 100% of the work (as in a transformer), attention only does about 20–40% of the work in LFM2. The rest is handled by short convolutions — which are *much* faster and don't grow a giant memory cache as the conversation gets longer ([Spheron](https://www.spheron.network/blog/liquid-foundation-models-lfm-deployment-gpu-cloud-2026/)).

**Why this matters for your phone:** in a pure transformer, every layer adds to the KV cache, and the cache balloons with context. In LFM2, only the few attention layers grow a cache, and even those use the trimmed-down "grouped query" variety. The total memory footprint is dramatically smaller, so a 1.2-billion-parameter LFM2 model can run smoothly on a phone while a same-size transformer would struggle.

### STAR: letting AI design AI

Here's the cool part. Liquid didn't hand-design LFM2's exact recipe (10 convolution blocks + 6 attention blocks). They built a system called **STAR** — a neural architecture search engine that tries thousands of architecture variations *in simulation on the actual target hardware* and evolves toward the one that hits the right speed/accuracy/memory tradeoff.

So if a customer like Mercedes-Benz says "we need a model that fits in this exact chip with this exact latency budget," STAR can search for a custom architecture tailored to that hardware. This is very different from the transformer world, where everyone uses essentially the same blueprint.

### The performance claim

Liquid says LFM2 is **2× faster** at generating text on a CPU than comparable open models like Alibaba's Qwen3 and Google's Gemma 3, and **3× faster to train** than their own previous generation ([Liquid AI](https://www.liquid.ai/blog/liquid-foundation-models-v2-our-second-series-of-generative-ai-models)). Their smallest "thinking" model (LFM2.5-1.2B-Thinking) reportedly fits in under 900 MB on a phone and generates 235 tokens per second — fast enough to feel instant.

These are Liquid's own benchmarks, so take them as company claims rather than independently verified, but the *direction* — much faster on small hardware — is consistent across third-party evaluations.

## Part 4: What makes this different from everyone else

There are basically four camps in AI today, and Liquid sits in a distinct one.

### Camp 1: Frontier labs (OpenAI, Anthropic, Google DeepMind)
**Strategy:** Build the biggest, smartest model possible. Run it in the cloud. Charge per API call.
**Strength:** Best raw capability for open-ended reasoning.
**Weakness:** Expensive, slow, requires internet, your data leaves your device.

### Camp 2: Big-tech on-device (Apple Intelligence, Google Gemini Nano, Microsoft Phi)
**Strategy:** Build a small transformer that fits on the company's own devices.
**Strength:** Integrated with the OS.
**Weakness:** Tied to that company's ecosystem; still fundamentally compressed transformers.

### Camp 3: Open small models (Meta Llama 3.2, Alibaba Qwen, Mistral, IBM Granite)
**Strategy:** Release small open-weight transformers that anyone can run.
**Strength:** Free, flexible, big community.
**Weakness:** Still transformers, still designed cloud-first.

### Camp 4: Liquid AI's bet — first-principles efficient AI
**Strategy:** Invent a *new* architecture (not a transformer) that's designed for edge hardware from the start. Build custom variants per customer using STAR. Offer a deployment platform (LEAP) so developers can ship these models in mobile apps in ~10 lines of code.

**Four things Liquid does that nobody else really does:**

1. **A genuinely different architecture.** Most "small models" are just smaller transformers. LFM2 is a fundamentally different shape — heavy on convolutions, light on attention — which is why it can be faster and smaller without sacrificing as much quality.
2. **Hardware-aware architecture search.** STAR can custom-design a model for a specific chip. Imagine ordering a suit tailored to your body vs. buying off the rack — that's the analogy.
3. **AMD alignment.** Liquid's deep partnership with AMD positions them as a hedge against everyone-depends-on-NVIDIA. This matters strategically because Big AI is bottlenecked by NVIDIA's chip supply.
4. **Task-specific tiny models (Nanos).** Instead of one giant model that does everything okay, Liquid ships small models that do *one thing* really well — a 350MB model just for English-to-Japanese translation, another just for extracting data from documents, another just for math. They claim these match GPT-4o on their specific tasks while being a fraction of the size.

## Part 5: Where it actually runs

The whole point of efficient on-device AI is that it shows up in real products. As of mid-2026:

- **Mercedes-Benz** is embedding LFM2 in next-gen MBUX in-car systems so the voice assistant works without internet. Hasani's argument: "you cannot rely on the cloud to power a critical safety feature inside a car."
- **AMD** uses a custom LFM2-2.6B model for on-device meeting summarization on Ryzen AI PCs.
- **Brilliant Labs Halo** smart glasses ($299) use LFM2-VL-450M to understand what you're looking at.
- **Insilico Medicine** uses an LFM2 variant for drug discovery that outperformed a Google model 10× its size on most benchmarks.
- **Shopify** uses Liquid Nanos across its platform.
- **G42** (UAE) uses Liquid for sovereign AI deployments where data isn't allowed to leave the country.

## Part 6: The honest caveats

A few things to keep in mind so you have a calibrated view:

- **Frontier models still win at open-ended reasoning.** If you want to brainstorm a novel, debate philosophy, or write complex code, GPT-5 or Claude will beat any on-device model. Liquid's pitch is "frontier-quality on *specialized* tasks" — not "we beat OpenAI at everything."
- **The "worm brain" branding is the lineage, not literally what's shipping.** LFM2 production models are best understood as a hybrid convolution/attention architecture inspired by liquid networks, not as literal C. elegans simulations.
- **Most performance claims come from Liquid's own materials.** Independent benchmarks are still catching up. The architectural advantages are real; the exact speedups will get more rigorous over time.
- **Liquid believes in a hybrid future, not anti-cloud.** Their CEO explicitly says cloud and device should work together — small specialized models handle the routine 95% of work locally, and big cloud models get called for the hard 5%.

## TL;DR

Liquid AI built a new kind of AI architecture, inspired by a worm's brain, that's designed from the start to run on small devices instead of giant data centers. Where transformers (the dominant AI design) get slow and memory-hungry as conversations grow longer, Liquid's hybrid convolution-plus-attention design stays fast and tiny. Their bet is that the future of AI is millions of small, specialized models running on your phone, car, and watch — not one giant brain in the cloud. They're backed by AMD and shipping in products from Mercedes-Benz to smart glasses. It's not trying to replace ChatGPT; it's trying to put useful AI everywhere ChatGPT can't go.
