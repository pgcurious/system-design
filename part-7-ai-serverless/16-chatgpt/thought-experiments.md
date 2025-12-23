# Thought Experiments: ChatGPT

These exercises test deep understanding of LLM inference systems and GPU optimization.

---

## Experiment 1: The Cold Start Problem

**Scenario:**
A new conversation starts. The user expects to see the first token within 500ms. But you need to:
1. Route the request to an available GPU
2. Load context (if any previous conversation)
3. Run prefill on the prompt
4. Generate the first token

**Questions to consider:**

1. Most of the 500ms budget goes to prefill. For a 1,000-token prompt, prefill might take 200ms. How would you reduce time-to-first-token for common use cases?

2. Users often start conversations with similar system prompts (same company, same use case). How could you share computation across these similar prompts? What's the tradeoff between accuracy and efficiency?

3. If you pre-warm GPU slots with "likely" requests, how do you predict what's likely? What happens when prediction is wrong?

4. Some users pay more (enterprise tier). How would you implement priority routing without starving free-tier users?

**The insight:** Cold start optimization is about predicting the future and trading off resources. Pre-warming wastes resources when wrong, but dramatically improves latency when right. The system must balance speculation against utilization.

---

## Experiment 2: The Long Context Challenge

**Scenario:**
A user uploads a 200-page PDF and asks: "Summarize this document." The document tokenizes to 100,000 tokens. Your model supports 128K context, but:
- KV cache for 100K tokens = 5GB per sequence
- You normally batch 32 sequences per GPU
- This single request would consume GPU memory for 16 normal requests

**Questions to consider:**

1. Should you run this request on a dedicated "long context" cluster, or try to fit it in your normal serving infrastructure? What are the tradeoffs?

2. How does continuous batching change when one request is 100x longer than average? Does it still provide efficiency gains?

3. Could you stream the document processing—running prefill in chunks and generating summary as you go? What would break about the attention mechanism?

4. If long-context requests are 10x more expensive but you charge the same per token, what happens to your business? How should pricing model reflect true cost?

**The insight:** Long context breaks the assumptions of most serving systems. It's not just "more tokens"—it's qualitatively different resource consumption that requires architectural changes or business model changes.

---

## Experiment 3: The Viral Prompt

**Scenario:**
A famous influencer posts: "Ask ChatGPT: 'Write a poem about [topic]' and share the results!" Within an hour, 1 million users send nearly identical prompts. Your normal load is 10,000 req/sec; you're now at 100,000 req/sec.

**Questions to consider:**

1. Exact prompt caching seems obvious—return the same response for identical prompts. But LLMs are generative. Is returning the same poem to everyone the right experience? What if you add small variations?

2. Even with different responses, the prefill computation is identical for the same prompt. How can you share this computation across requests? (Hint: prefix caching)

3. Your auto-scaling can add capacity in 10 minutes. The viral spike lasts 30 minutes. What's your strategy for the first 10 minutes? Drop requests? Queue them? Serve degraded (shorter) responses?

4. After the spike, you have 10x over-provisioned capacity. How quickly should you scale down? What if there's a second wave?

**The insight:** Viral events test every assumption about capacity planning. The answer isn't just "more GPUs"—it's caching, sharing, graceful degradation, and anticipating human behavior on social media.

---

## Experiment 4: The Cost Optimization Dilemma

**Scenario:**
Your inference costs are 70% of revenue. The CEO says: "Cut inference costs by 50% without impacting quality." You have three options:
- Quantization (INT8/INT4): 2x efficiency, slight quality drop
- Smaller models for simpler queries: 5x efficiency, needs routing
- Speculative decoding: 2-3x efficiency on some queries, no quality drop

**Questions to consider:**

1. How do you measure "quality impact"? User satisfaction surveys? Benchmark scores? A/B tests? What if different users care about different quality dimensions?

2. Routing to smaller models means classifying query difficulty. How would you build this classifier? What's the cost of misclassification (simple query to big model vs. complex query to small model)?

3. Quantization and speculative decoding can be combined. How do you decide the right combination? Is the improvement multiplicative?

4. Cost per token varies by time of day (cloud spot pricing) and by model. Could you build a system that dynamically picks the cheapest option that meets quality SLA?

**The insight:** Cost optimization in ML systems isn't a one-time decision—it's a continuous optimization problem balancing cost, quality, and latency. The "right answer" changes based on load, pricing, and user expectations.

---

## Experiment 5: The Multi-Modal Future

**Scenario:**
Users start sending images with their prompts: "What's in this picture?" The image must be processed through a vision encoder before being fed to the language model. This changes everything:
- Vision encoding adds 100ms to prefill
- Images consume more "tokens" (patch embeddings)
- Vision model runs on different hardware than language model
- Some requests are text-only, some are image-heavy

**Questions to consider:**

1. Should vision encoding and language modeling run on the same GPU, or should you have specialized vision servers that feed into language servers? What are the latency vs. utilization tradeoffs?

2. Vision encoding is embarrassingly parallel across images. How would you batch image processing? Is it the same batching strategy as text?

3. A user sends 10 images and asks "Which of these matches this description?" How do you handle multi-image inputs? Process sequentially? In parallel?

4. Video support is coming—users want to analyze 60-second clips. How does this 10x the problem? What new caching opportunities exist for video (temporal redundancy)?

**The insight:** Multi-modality breaks the assumption that all tokens are equal. Vision, audio, and video require different compute, different batching, and different caching strategies. The serving system becomes heterogeneous.

---

## Bonus: Design Exercise

**Design a cost-aware inference router that:**
1. Classifies incoming requests by complexity (simple/medium/complex)
2. Routes to appropriate model (7B/70B/175B)
3. Handles fallback if small model fails
4. Maintains latency SLAs
5. Minimizes cost while meeting quality targets

**Sketch the architecture, APIs, and key algorithms.**

Considerations:
- How do you define and measure "complexity"?
- What's the latency overhead of the classification step?
- How do you handle the cold path (first request with no history)?
- What happens during traffic spikes—do you still route optimally?
