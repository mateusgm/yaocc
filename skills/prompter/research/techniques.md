# Empirical prompt engineering: what works, what doesn't, and what changed in 2025

**No single prompting technique is universally optimal** — this is the most replicated finding across 2025 prompt engineering research. Effectiveness depends on the interaction of task type, model family, model size, and specific configuration, making empirical validation per use case essential rather than optional. The field has shifted decisively from artisanal prompt crafting toward automated optimization and adaptive strategies, with systems like GEPA outperforming both manual prompting and reinforcement learning on hard benchmarks. Yet prompt design still matters enormously: even frontier models show **4–10% performance swings from formatting changes alone**, and benchmark leaderboard rankings flip on 3 out of 7 tasks when prompts are properly optimized.

This report synthesizes findings from peer-reviewed papers, controlled experiments, and rigorous benchmarks — prioritizing 2025 publications while noting model generation and date for older work. It covers the full lifecycle: writing prompts, optimizing them, and evaluating their quality.

---

## The 2025 landscape: prompt engineering matured, not died

The narrative that "prompt engineering is dead" gained traction in 2025 as frontier models improved. The evidence tells a more nuanced story. Multiple large-scale studies confirmed that prompt design materially affects performance on even the most capable models. Aali et al. (Stanford, November 2025) integrated DSPy with the HELM benchmark framework and found that without structured prompting, **HELM underestimates LM performance by 4% on average** and leaderboard rankings flip on 3 of 7 benchmarks. Google Research (Leviathan, Kalman, Matias, late 2025) demonstrated that simply repeating the prompt improved performance across 7 non-reasoning models, achieving **47 wins out of 70 head-to-head tests with zero losses** — and on one benchmark, Gemini 2.0 Flash-Lite jumped from 21% to 76% accuracy from repetition alone.

What changed is *how* prompt engineering is practiced. Andrej Karpathy coined the shift from "prompt engineering" to **"context engineering"** in mid-2025, emphasizing that designing the full context fed to models matters more than isolated prompt phrasing. Andrew Ng's flow engineering results showed GPT-3.5 with an agentic workflow outperforming GPT-4 with a single prompt on HumanEval by more than 2×. The Prompt Report (Schulhoff et al., arXiv:2406.06608, updated February 2025) — the field's most comprehensive survey — catalogued **58 distinct prompting techniques** across 1,500+ papers, establishing a standardized taxonomy. Sahoo et al. (arXiv:2402.07927, updated March 2025) identified 41+ techniques with detailed strengths and limitations per application area.

The consensus from 2025 evidence: basic techniques like chain-of-thought and few-shot examples are now "table stakes." The frontier has moved to automated prompt optimization, adaptive per-model strategies, and system-level prompt design for agentic workflows.

---

## Writing effective prompts: what the evidence says for each technique

### Chain-of-thought and its descendants

Chain-of-thought prompting (Wei et al., NeurIPS 2022) remains the most empirically validated technique for reasoning tasks. On PaLM-540B, CoT raised GSM8K accuracy from **17.9% to 56.9%** — a 39-point gain. Zero-shot CoT ("Let's think step by step," Kojima et al., NeurIPS 2022) achieved dramatic gains on InstructGPT: MultiArith jumped from 17.7% to **78.7%**, and GSM8K from 10.4% to **40.7%**. However, CoT is an **emergent ability requiring ~100B+ parameters** — smaller models produce fluent but illogical chains that degrade performance. Wang et al. (2022) found even invalid reasoning steps achieve 80–90% of CoT's benefit, suggesting the technique works partly by structuring output format rather than enabling genuine reasoning.

Self-consistency (Wang et al., ICLR 2023) compounds CoT gains by sampling multiple reasoning paths and majority-voting on answers. On PaLM-540B, it pushed GSM8K from 56.5% (greedy CoT) to **74.4%** — an additional **17.9 percentage points**. SVAMP gained 11.0 points, AQuA 12.2 points. The optimal sampling temperature was T=0.7. A 2025 follow-up (Difficulty-Adaptive Self-Consistency, NAACL Findings) showed that allocating more samples to harder problems maintains performance while significantly reducing cost.

Tree-of-Thought (Yao et al., NeurIPS 2023) demonstrated massive gains on tasks requiring exploration and backtracking, tested on GPT-4. On the Game of 24, standard prompting achieved 7.3%, CoT actually performed worse at 4.0%, while **ToT with BFS reached 74%**. However, ToT requires significantly more API calls per problem.

Two 2025 innovations deserve attention. **Chain of Draft** (Xu et al., Zoom, February 2025) matches or surpasses CoT accuracy while using only **7.6% of the tokens**, tested on Claude 3.5 Sonnet and GPT-4o — a 92% cost reduction with no accuracy loss. **DR-CoT** (Sinha et al., *Scientific Reports*, October 2025) showed consistent gains even on frontier reasoning models: Grok 3 Beta improved from 84.6% to **87.3%** on GPQA Diamond, and o3 Mini gained 4.4 percentage points.

### Few-shot and many-shot prompting

The GPT-3 paper (Brown et al., NeurIPS 2020) established few-shot in-context learning, and subsequent research has refined understanding of optimal example counts. The empirical consensus is that **2–5 examples** typically offer optimal guidance, with diminishing returns beyond 5 for standard-context models. However, quality and similarity to the target matter more than quantity — TF-IDF-based or KNN few-shot selection consistently outperforms random sampling.

Many-shot in-context learning (Agarwal et al., NeurIPS 2024 Spotlight) changed the picture for long-context models. Testing primarily on Gemini 1.5 Pro with its 1M-token context, performance scaled **log-linearly** with example count across translation, math, planning, and code verification. In-context planning improved from **42% to 62%** with hundreds of examples. Critically, many-shot ICL can overcome pre-training biases and performs **comparably to supervised fine-tuning** for many tasks. But this is model-dependent: open-weights models like Llama 3.2-Vision did not benefit from many-shot ICL.

Known biases in few-shot prompting include majority label bias (favoring answers frequent in examples), recency bias (weighted toward later examples), and common token bias. Example ordering dramatically affects results — Lu et al. (ACL 2022) found ordering can be **the difference between near-SOTA and random-guess performance** across all model sizes, and a good ordering for one model does not transfer to another.

### Role prompting: surprisingly weak evidence

Despite its popularity, role/persona prompting has **weak and inconsistent empirical support** for accuracy-based tasks. Zheng, Pei, and Jurgens (2024) tested 4 LLM families on 2,410 factual questions and found that adding personas in system prompts **does not improve performance** compared to no-persona controls. Luz de Araujo et al. (EMNLP 2025) tested 9 state-of-the-art LLMs across 27 tasks: expert personas showed positive or non-significant changes, but models were highly sensitive to **irrelevant persona details**, with drops of **almost 30 percentage points**. Where role prompting does help is in open-ended and creative tasks — auto-generated expert personas scored 0.3–0.9 points above control on advice and brainstorming tasks. A 2025 large-scale SE study (Santana et al.) found role prompting to be a cost-efficient alternative despite not being the top performer.

### Structured output formatting: a double-edged sword

Tam et al. (EMNLP 2024 Industry Track) tested GPT-3.5-turbo, Claude-3-haiku, and LLaMA-3-8B and found that format restrictions (JSON mode, XML) cause **significant decline in reasoning abilities** — stricter constraints produce greater degradation. However, format restrictions **improve classification task accuracy**. The practical solution is a two-step pipeline: reason freely first, then format the output. GPT-4 is less sensitive to format changes than GPT-3.5, suggesting larger models handle format constraints more gracefully.

### Prompt sensitivity: the elephant in the room

Sclar et al. (ICLR 2024) demonstrated that meaning-preserving formatting changes alone cause **up to 76 accuracy points of variation** on LLaMA-2-13B, and ~10 points on average across 50+ tasks. This sensitivity is **not eliminated** by increasing model size, adding few-shot examples, or instruction tuning. A format that works well for one model may not work for another. "When Punctuation Matters" (2025) confirmed across 52 tasks and 8 models (including GPT-4.1 and DeepSeek V3) that even whitespace and punctuation introduce large performance shifts.

---

## Findings segmented by application type

### Reasoning and math

CoT remains foundational, with self-consistency as the primary amplifier. DUP (Deeply Understanding Problems) achieved **97.1% on GSM8K with GPT-4** in zero-shot by addressing semantic misunderstanding errors that CoT misses. Program-of-Thought (generating code to solve math) yields ~12% average gains over natural language CoT. GSM8K is now **saturated** for frontier models (most exceed 94%); harder benchmarks like MATH, AIME, and LiveBench-Math better differentiate techniques. The progression for math tasks: standard prompting → CoT → CoT + self-consistency → problem decomposition → program-of-thought.

### Code generation

CoT alone is often insufficient for code. **Structured decomposition and iterative refinement dominate.** CGO (Chain of Grounded Objectives) outperformed standard CoT on HumanEval with LLaMA3-8B-Instruct: **62.4 vs. 57.5 pass@1** (Yeo et al., ECOOP 2025). EPiC (evolutionary approach) with o3-mini reached **0.95 pass@1 on HumanEval+**, beating Reflexion (0.92) and LATS (0.93). Reflexion with GPT-4 achieved **91% on HumanEval** vs. 80% baseline. BODE-GEN automated prompt optimization improved CodeLlama-7b from 0.23 to 0.50 accuracy. Prompts specifying function signatures, expected behaviors, and edge cases outperform generic step-by-step reasoning.

### Retrieval-augmented generation

The dominant finding is the **"lost in the middle" phenomenon** (Liu et al., 2023, Stanford/UW): LLMs show a U-shaped accuracy curve — performance peaks when relevant information is at the beginning or end of context and degrades significantly in the middle. Tested across GPT-3.5-Turbo, Claude-1.3, and MPT-30B. Practical solutions include reranking (placing key documents at top and bottom, yielding **45% precision boost** in one legal use case), contextual compression, and adaptive-k retrieval. RAG prompt engineering is fundamentally about **context engineering** — what to include, how to order it, how much to retrieve — rather than instruction phrasing alone.

### Classification, NER, and extraction

Task decomposition and entity definitions are critical. A zero-shot approach decomposing NER into boundary detection plus classification achieved **75.39% F1 on CoNLL-2003** vs. 72.90% for unified approaches (Yang et al., WWW 2025). PromptNER (Ashok et al., 2023) using entity definitions plus explanation generation gained +4% F1 on CoNLL, +9% on GENIA, and +4% on FewNERD. GPT-4o with prompt ensemble achieved **F1=0.95 and recall=0.98** for medical NER. Cross-model differences are notable: Gemini excels at contextually ambiguous entities while GPT-4 handles structured tags best.

### Agentic and multi-turn systems

The AgentArch benchmark (ServiceNow, 2025) evaluated 18 agentic configurations and found best models achieved only **35.3% success on complex tasks**. ReAct prompting showed significant weaknesses in multi-agent systems; sensitivity analysis (Verma et al., 2024) found performance driven by **exemplar-query similarity** rather than genuine interleaved reasoning. Reflexion self-critique loops proved powerful: **130/134 AlfWorld challenges solved** and 91% HumanEval with GPT-4. The critical insight is that architecture (single vs. multi-agent, memory management, function calling) matters as much as prompt content. Santana et al. (June 2025) tested 14 prompting techniques across 10 SE tasks on 4 LLMs and found o3-mini showed fundamentally different performance patterns than other models.

### Creative writing and summarization

Creative tasks benefit from *less* constraint and *more* role/persona specification — the inverse of accuracy tasks. Temperature settings matter more here. Claude captures writing style best from examples; ChatGPT excels at personality and storytelling; Gemini tends toward verbose outputs. Summarization is among the most "prompt-forgiving" tasks — zero-shot prompting is often sufficient, with sentence-based chunking strategies mattering more for long documents than prompt sophistication.

---

## Systematic prompt optimization outperforms manual crafting

The strongest 2025 finding is that **automated prompt optimization consistently beats manual engineering** on tasks with clear evaluation metrics. GEPA (Agrawal et al., Stanford/Berkeley, accepted as **ICLR 2026 Oral**) combines genetic prompt evolution with natural language reflection and Pareto-based selection. On Qwen3-8B, GEPA outperformed GRPO (RL baseline) by **+6% average and up to +20%** while using **35× fewer rollouts**. It outperformed MIPROv2 by +12% on AIME-2025 and achieved **93% on MATH** vs. 67% with basic DSPy chain-of-thought. This is a landmark result: prompt optimization outperforming RL-based fine-tuning with far fewer samples.

The broader optimization landscape in 2025 includes several validated approaches:

| Method | Venue | Key result | Computational cost |
|--------|-------|------------|--------------------|
| GEPA | ICLR 2026 Oral | +13% over MIPROv2 across benchmarks; 35× fewer rollouts than RL | Moderate |
| DSPy/MIPROv2 | EMNLP 2024 | +13% accuracy on multi-stage programs; 0.82 F1 on hallucination detection | Moderate–high |
| OPRO | ICLR 2024 | +8% on GSM8K; discovered "Take a deep breath" prompt; +50% on some BBH tasks | Moderate |
| TextGrad | Nature 2024 | Pushed GPT-3.5 near GPT-4 on BBH reasoning; +20% on LeetCode-Hard | High (needs GPT-4o) |
| APE | ICLR 2023 | Outperformed human prompts on 19/24 instruction induction tasks | Low–moderate |
| EvoPrompt | ICLR 2024 | +25% on BBH tasks; +3 SARI points on text simplification | Moderate |
| RIOT | ACL 2025 | 77.2% weighted accuracy, beating all baselines including DSPy by 2.7% | Moderate |

OPRO (Google DeepMind, ICLR 2024) discovered that the instruction "Take a deep breath and work on this problem step-by-step" outperformed human-designed prompts on GSM8K with PaLM 2-L, reaching **80.2%** — confirming that optimal prompts are often non-obvious. The LangChain benchmark (January 2025) found that **Claude-Sonnet outperformed GPT-4o as a meta-optimizer** across all approaches, and that prompt optimization was most effective where models lack domain knowledge, achieving **~200% accuracy increase** over naive baselines.

The key insight from MIPRO research is that **jointly optimizing instructions and few-shot demonstrations yields the best results** — optimizing either alone leaves significant performance on the table.

---

## Evaluating prompts: metrics, judges, and pitfalls

### LLM-as-judge reaches practical maturity

The foundational study (Zheng et al., NeurIPS 2023) established that **GPT-4 achieves >80% agreement with human preferences** — matching inter-human agreement levels. Three systematic biases persist: position bias (favoring certain response positions), verbosity bias (preferring longer outputs), and self-enhancement bias (rating own outputs higher). Mitigation strategies include position swapping, reference answers, and chain-of-thought judge prompting.

More recent evaluations strengthen confidence in this approach. Prometheus (Kim et al., 2024), a 13B open-source judge, achieved **Pearson correlation of 0.897 with human evaluators** on customized rubrics — exceeding GPT-4's 0.882. Length-controlled AlpacaEval achieved **0.98 Spearman correlation** with Chatbot Arena human rankings. A 2025 benchmark testing 54 LLMs as judges found 27 achieved Tier 1 performance with human-like judgment patterns. The ICLR 2025 "Trust or Escalate" paper showed that even weak cascades of judges (Mistral-7B, Mixtral-8×7B, GPT-3.5) can guarantee human-level agreement while **reducing costs by 40%** compared to GPT-4.

However, ICLR 2025's "LLM as Judge Won't Beat Twice the Data" argues outputs "often correlate poorly with expert annotations" and that doubling evaluation data may outperform LLM judges. GAUSS Eval (2025) identified **leniency bias** in math grading: models over-credit structurally plausible but mathematically flawed solutions, assigning non-zero credit to 28% of fully invalid responses.

### Traditional metrics versus LLM judges

Traditional metrics correlate poorly with human judgment for open-ended tasks. BLEU and ROUGE typically achieve correlations below 0.3 for creative generation, while BERTScore shows **59% alignment** with human majority voting versus BLEU's 47%. G-Eval (using GPT-4 with CoT) reached **Spearman correlation of 0.514** on summarization — a leap over traditional metrics but still far from perfect. For machine translation, BLEU remains competitive at 0.78–0.91 correlation. The practical recommendation from the literature: use **layered evaluation** combining automated metrics, LLM-as-judge, and human spot-checks.

### Benchmark contamination undermines published results

Benchmark reliability is a serious concern. MMLU-CF (Contamination-Free) variant shows top models' accuracy drops **14–16 points** versus original MMLU when memorization artifacts are removed. Roberts et al. (ICLR 2024) found LLM performance on Codeforces plummets after training cutoff, with pre-cutoff performance "highly correlated with the number of times the problem appears on GitHub." GPT-4 was able to guess missing MMLU options with 57% exact match rate — strong contamination evidence. LiveBench (ICLR 2025 Spotlight) addresses this with frequently updated questions and verifiable ground truth, where top models stay below 70%.

### Practical evaluation framework

The evidence converges on a recommended evaluation approach. Build a **proprietary test set of 200–500 examples** from production data — immune to contamination. Use rubric-based evaluation rather than Likert scales (higher inter-annotator agreement per TN-Eval 2025). Deploy LLM-as-judge calibrated against human annotations targeting 85–90% agreement. Report performance across **multiple prompt formats** rather than just one (per Sclar et al.). Rotate test sets every 6 months and version everything. For statistical comparison of prompt variants, use matched-pairs tests with minimum 200 examples.

---

## Model-specific findings shape strategy choices

Performance varies substantially across model families with identical prompts. On 147 engineering problems, Claude 3 Opus (58.5%) outperformed GPT-4 (45.6%), which outperformed Gemini 1.0 Ultra (34.0%). On spatial tasks, GPT-4 completed 76.3% correctly versus Gemini's 55.3%. But by late 2025, top-tier models converged: gemini-2.5-flash, claude-opus-4.1, and gpt-5 showed **statistically indistinguishable evaluation performance** on automated grading tasks.

Structural preferences differ by model family. Claude prefers XML tags, more context, and explicit assumptions. GPT models prefer JSON schemas and step-by-step formatting. Gemini favors scope definition and source specification. These preferences mean that **prompt structure must be adapted per model**, not just prompt content.

Model size creates a hard boundary for technique effectiveness. CoT only works with models above approximately **100 billion parameters** — below this threshold, it produces fluent but illogical reasoning chains that hurt performance. This was consistent across GPT-3, LaMDA, and PaLM families (Wei et al., 2022). The TMLR 2025 study (Mohanty et al.) found that structured reasoning prompts (CoT, ToT) **increased hallucination rates up to 75% in small (<4B parameter) multimodal models**. Simpler methods (one-shot, few-shot) were more effective for small models. For open-source strategy, the evidence supports decomposing tasks into granular subtasks to compensate for weaker single-step reasoning, rather than applying complex prompting techniques designed for frontier models.

---

## Conclusion

Three findings reshape how practitioners should approach prompt engineering in 2025 and beyond. First, **automated optimization has decisively overtaken manual prompting** for any task with measurable evaluation criteria — GEPA's ICLR 2026 Oral result showing it beats both the leading prompt optimizer (MIPROv2) and reinforcement learning (GRPO) while using 35× fewer compute resources makes manual prompt tuning on well-defined tasks increasingly hard to justify. Second, **technique selection must be task-aware and model-aware** — the 14-technique × 10-task × 4-model study found no universal winner, and structured reasoning prompts that help large models actively harm smaller ones. Third, **evaluation methodology is as important as prompt design** — prompt sensitivity studies show formatting alone swings results by up to 76 points, benchmark contamination inflates scores by 14–16 points, and LLM judges carry systematic biases that must be calibrated against human annotations.

The field has evolved from "what magic words make the AI work better" to a rigorous engineering discipline encompassing context design, automated optimization, systematic evaluation, and model-specific adaptation. The most productive investment is not in crafting individual prompts but in building evaluation infrastructure that enables rapid, empirical iteration.
