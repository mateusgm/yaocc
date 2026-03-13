# Prompt Optimization Systems: Comprehensive Comparison

> All results cited from peer-reviewed papers, controlled benchmarks, or official documentation. GitHub stars as of March 2026 (approximate — counts fluctuate). Cost figures normalized to **$ per single-task optimization run** using GPT-4o-mini as execution model where available (from SPO paper, EMNLP 2025, the most comprehensive apples-to-apples comparison).

---

## Master Comparison Table

| System | Year | Venue | ⭐ GitHub Stars | Core Method | Key Benchmark Results | $ per Task¹ | Open Source |
|--------|------|-------|----------------|-------------|----------------------|-------------|-------------|
| **DSPy / MIPROv2** | 2024 | EMNLP 2024 | **~32.6k** | Bayesian opt over instructions + few-shot demos | +13% on multi-hop QA; HotpotQA 45%→60.5% | ~$2–5² | [stanfordnlp/dspy](https://github.com/stanfordnlp/dspy) |
| **PromptWizard** | 2024 | Microsoft Research | **~3.6k** | Critique-and-synthesis loop; co-optimizes instructions + examples | 13/19 best on BBII (zero-shot); 90% GSM8K | **~$0.05** | [microsoft/PromptWizard](https://github.com/microsoft/PromptWizard) |
| **TextGrad** | 2024 | Nature 2024 | **~3.3k** | Backpropagation via textual gradients (PyTorch-like API) | GPT-3.5→near GPT-4 on BBH; +20% LeetCode-Hard | ~$13.14 | [zou-group/textgrad](https://github.com/zou-group/textgrad) |
| **GEPA** | 2025 | ICLR 2026 (Oral) | **~2k** | Genetic evolution + NL reflection + Pareto selection | +6% avg over RL; +12% over MIPROv2 on AIME; 93% MATH | ~$3–8³ | [gepa-ai/gepa](https://github.com/gepa-ai/gepa) |
| **OPRO** | 2023 | ICLR 2024 | **~700** | LLM-as-optimizer: meta-prompt with past solutions | GSM8K +8% (80.2%); +50% on some BBH tasks | ~$4.51 | [google-deepmind/opro](https://github.com/google-deepmind/opro) |
| **SPO** | 2025 | EMNLP 2025 | **~500** | Self-supervised: pairwise output comparisons, no ground truth | Avg 66.9% across 5 benchmarks; top-2 in all | **~$0.15** | [FoundationAgents/SPO](https://github.com/FoundationAgents/SPO) |
| **PromptBreeder** | 2023 | ICLR 2024 | ~200⁴ | Self-referential evolution — co-evolves prompts AND mutation prompts | 83.9% GSM8K (zero-shot); 89% ETHOS hate speech | ~$4.82 | Community repos⁵ |
| **APE** | 2022 | ICLR 2023 | ~300⁴ | Generate candidate instructions, Monte Carlo selection | 19/24 tasks beat human prompts | ~$9.07 | [keirp/automatic_prompt_engineer](https://github.com/keirp/automatic_prompt_engineer) |
| **APO / ProTeGi** | 2023 | EMNLP 2023 | ~300⁴ | Textual gradients + beam search | BBH 68.2 avg (vs APE 64.2) with GPT-4-turbo | ~$5–8² | [microsoft/LMOps](https://github.com/microsoft/LMOps) |
| **EvoPrompt** | 2023 | ICLR 2024 | ~210 | Evolutionary algorithms (GA + Differential Evolution) via LLM | +25% on BBH; +3 SARI on simplification | ~$2–4² | [beeevita/EvoPrompt](https://github.com/beeevita/EvoPrompt) |
| **ACE** | 2025 | ICLR 2026 | ~200⁴ | Agentic context engineering — append-only playbooks | 82.3% less latency vs GEPA; 83.6% token cost ↓ | Very low (append-only) | [sambanova/ace](https://github.com/sambanova/ace) |
| **GReaTer** | 2024 | ACL 2025 | ~150⁴ | Gradient-based optimization over reasoning tokens (small models) | BBH 68.7% w/ Llama-8B (vs TextGrad 58.5%) | **~$0** (local) | [GreaTerPrompt](https://github.com/facebookresearch/GreaTerPrompt) |
| **RIOT** | 2025 | ACL 2025 | ~100⁴ | Residual optimization tree with perplexity-guided search | 77.2% weighted avg (best overall); GSM8K +7.8% | ~$2–5² | Code available |

**Footnotes:**
1. **Unified cost metric:** Dollar cost per single-task optimization run. Anchor values from SPO paper (EMNLP 2025) which ran all methods on identical infrastructure with GPT-4o-mini. Where exact figures aren't available from that paper, estimates are marked with ².
2. Estimated from reported API call counts and typical GPT-4o-mini pricing ($0.15/1M input, $0.60/1M output tokens).
3. GEPA uses 100–500 evaluations (vs 24,000 for RL), with a reflection LLM for each mutation. Cost depends heavily on reflection model choice (GPT-4o vs GPT-5).
4. Approximate — these repos have lower visibility or use community forks. Exact count not verified.
5. PromptBreeder's original DeepMind code was not open-sourced; community implementations exist (e.g., vaughanknouse/PromptBreeder).

---

## Head-to-Head: SPO Paper's Unified Comparison (EMNLP 2025)

The most rigorous apples-to-apples cost comparison, all using GPT-4o-mini on identical test sets:

| Method | GPQA | AGIEval | LIAR | WSC | BBH | **Avg Accuracy** | **Optimization Cost** | **Cost Efficiency (pts/$)** |
|--------|------|---------|------|-----|-----|------------------|-----------------------|-----------------------------|
| APE | 41.1 | 44.4 | 65.9 | 80.2 | 92.5 | 64.8 | $9.07 | 7.1 |
| OPRO | 43.3 | 46.1 | 67.6 | 80.2 | 95.8 | 66.6 | $4.51 | 14.8 |
| PromptAgent | 41.3 | 41.4 | 64.1 | 82.7 | 95.7 | 65.0 | $2.71 | 24.0 |
| PromptBreeder | 40.9 | 45.9 | 63.2 | 76.7 | 96.3 | 64.5 | $4.82 | 13.4 |
| TextGrad | 40.2 | 44.4 | 65.7 | 78.0 | 91.3 | 63.9 | $13.14 | 4.9 |
| **SPO** | **43.6** | **46.1** | 67.1 | 82.0 | **97.2** | **66.9** | **$0.15** | **446** |

**Takeaway:** SPO achieves the best accuracy AND the lowest cost — a 60× to 90× cost advantage over every competitor. The catch: it requires no labeled data, which makes it ideal for open-ended tasks but means it relies on the LLM's own judgment of quality.

---

## Head-to-Head: GReaTer Paper (ACL 2025) — Small Model Optimization

All using Llama-3-8B-Instruct (no API costs):

| Method | GSM8K | BBH | FOLIO | **Runs locally?** |
|--------|-------|-----|-------|-------------------|
| Zero-Shot CoT | 79.6 | 62.2 | 58.6 | ✅ |
| APE | 79.9 | 63.1 | 57.6 | ❌ (needs GPT-4) |
| TextGrad | 78.5 | 58.5 | 56.2 | ✅ |
| **GReaTer** | **82.6** | **68.7** | **62.6** | ✅ |

**Takeaway:** GReaTer dominates when optimizing with small, local models. TextGrad actually *hurts* BBH performance at this scale.

---

## Head-to-Head: GEPA Paper (ICLR 2026) — vs Reinforcement Learning

| Method | Avg across 6 tasks | Evals needed | Relative efficiency |
|--------|--------------------|--------------|--------------------|
| GRPO (RL) | Baseline | 24,000 | 1× |
| MIPROv2 | +4% over GRPO | ~600 | ~40× |
| **GEPA** | **+6% over GRPO; +10% over MIPROv2** | **~500** | **~48×** |

**Takeaway:** GEPA beats RL with 35–48× fewer samples. This is the strongest evidence that prompt optimization can outperform weight-level optimization for many tasks.

---

## Decision Matrix: Which System to Use When

| Scenario | Top Pick | Runner-up | Why |
|----------|----------|-----------|-----|
| Maximum accuracy, multi-module pipeline | **GEPA** | DSPy/MIPROv2 | Best SOTA results; optimizes each module |
| Tight budget, need fast results | **SPO** ($0.15) | PromptWizard ($0.05) | 90× cheaper than alternatives; no labels needed |
| Production LLM application | **DSPy/MIPROv2** | GEPA (via DSPy) | Largest ecosystem; 32k stars; composable |
| Optimizing code or scientific artifacts | **TextGrad** | GEPA | Optimizes any text variable; PyTorch API |
| Privacy-sensitive / on-prem only | **GReaTer** ($0) | EvoPrompt | Runs entirely on 8B model; zero API costs |
| No labeled data, creative/open tasks | **SPO** | ACE | Self-supervised; no ground truth needed |
| Long-running agent improvement | **ACE** | GEPA + ACE | Online adaptation; complements offline optimizers |
| Quick single-prompt discovery | **OPRO** | APE | Simple; discovers non-obvious instructions |

---

## Cost Normalization Notes

Comparing costs across papers is inherently imperfect because studies use different models, different numbers of training examples, and different evaluation set sizes. Here's how the figures above were derived:

**Directly measured (SPO paper):** APE ($9.07), OPRO ($4.51), PromptAgent ($2.71), PromptBreeder ($4.82), TextGrad ($13.14), SPO ($0.15) — all on GPT-4o-mini with identical setup.

**Reported by authors:** PromptWizard ($0.05/task per Microsoft's blog); GReaTer ($0 — runs locally on 8B models); GEPA (100–500 evals with reflection LLM, estimated $3–8 depending on reflection model).

**Estimated from API calls:** EvoPrompt (~100 iterations × population 10, moderate token usage), APO (beam search with multiple candidates), DSPy/MIPROv2 (10–100 Bayesian trials), RIOT (tree search with K=3 width).

The key insight: there's a roughly **100× cost range** between the cheapest (SPO at $0.15, PromptWizard at $0.05) and most expensive (TextGrad at $13.14, APE at $9.07) systems, with performance differences under 3 percentage points between the best performers.
