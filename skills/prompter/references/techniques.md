# Technique Selection Matrix

> Every recommendation below is backed by peer-reviewed evidence. Paper references and effect sizes are included so you can justify choices to the user.

## Quick selection guide

Given the diagnosed task type, apply these techniques. Cells marked ✅ = apply, ❌ = avoid (harms performance), ➖ = neutral/no effect, 🔶 = conditional.

| Technique | Reasoning/Math | Code gen | Classification | RAG | Creative | Agentic | Summarization |
|-----------|---------------|----------|----------------|-----|----------|---------|---------------|
| Chain-of-Thought | ✅ (+39pp GSM8K) | 🔶 (only for planning) | ❌ (hurts) | ➖ | ➖ | ✅ | ➖ |
| Few-shot (2–5) | ✅ | ✅ | ✅ (+significant) | ✅ | 🔶 | ✅ | ➖ |
| Problem decomposition | ✅ | ✅ (dominant) | ➖ | ➖ | ➖ | ✅ (critical) | ➖ |
| Output format spec | 🔶 (two-step) | ✅ | ✅ | ✅ | ❌ (constrains) | ✅ | ➖ |
| Role/persona | ❌ (no accuracy gain) | ➖ | ❌ (up to -30pp) | ➖ | ✅ (+0.3–0.9 pts) | 🔶 | ➖ |
| Self-consistency | ✅ (+17.9pp) | 🔶 (expensive) | ➖ | ➖ | ➖ | ➖ | ➖ |
| Delimiters/structure | ✅ | ✅ | ✅ | ✅ (critical) | ➖ | ✅ | ✅ |
| Many-shot (100+) | 🔶 (long-context only) | ➖ | ✅ (log-linear) | ➖ | ➖ | ➖ | ➖ |
| Tree-of-Thought | 🔶 (exploration tasks) | ➖ | ❌ | ➖ | ➖ | ➖ | ➖ |

---

## Detailed technique guides

### Chain-of-Thought (CoT)

**When to use:** Multi-step reasoning, math, logic, complex analysis.
**When to avoid:** Classification, simple extraction, models under 100B parameters.

**Evidence:**
- Wei et al. (NeurIPS 2022): PaLM-540B on GSM8K went from 17.9% → 56.9% with CoT.
- Zero-shot CoT "Let's think step by step" (Kojima et al., NeurIPS 2022): InstructGPT MultiArith 17.7% → 78.7%.
- CRITICAL: CoT is an emergent ability requiring ~100B+ parameters. On smaller models, it produces fluent but illogical reasoning chains that DEGRADE performance.
- Wang et al. (2022) found even invalid reasoning steps achieve 80–90% of CoT's benefit — the structure matters more than logical correctness.

**How to apply:**
```
[Task description]

Think through this step by step before giving your final answer.

[Input]
```

For math specifically, add: "Show your work. After completing your reasoning, provide only the final numerical answer on the last line."

**Cost-efficient variant — Chain of Draft (2025):**
Xu et al. (Zoom, Feb 2025) showed CoD matches CoT accuracy while using only 7.6% of the tokens. Tested on Claude 3.5 Sonnet and GPT-4o.

Apply by adding: "For each reasoning step, write only the minimum necessary — a few words or a formula, not full sentences. Like a human solving a problem on a notepad."

### Few-Shot Examples

**When to use:** Almost always beneficial for classification, extraction, and any task where you have examples of desired behavior.
**When to avoid:** Summarization (usually sufficient zero-shot), and when examples might introduce majority label bias.

**Evidence:**
- Brown et al. (NeurIPS 2020, GPT-3 paper): Established few-shot ICL.
- Optimal count: 2–5 examples. Diminishing returns beyond 5 for standard contexts.
- Many-shot ICL (Agarwal et al., NeurIPS 2024 Spotlight): On Gemini 1.5 Pro, performance scales log-linearly up to hundreds of examples. Planning tasks: 42% → 62%.
- CRITICAL: Example ORDER dramatically affects results. Lu et al. (ACL 2022) showed ordering can be the difference between near-SOTA and random-guess performance.

**How to apply:**
- Use examples most similar to the expected input (TF-IDF selection > random).
- Place the most representative/important example LAST (models exhibit recency bias).
- Include at least one edge case or negative example.
- For classification: balance labels across examples to avoid majority label bias.

**Template:**
```
[Task instruction]

Examples:

Input: [representative example 1]
Output: [correct output 1]

Input: [edge case example]
Output: [correct output for edge case]

Input: [most similar to expected query]
Output: [correct output]

Now complete this:
Input: [actual input]
Output:
```

### Problem Decomposition

**When to use:** Complex tasks, code generation, agentic workflows, multi-constraint problems.
**When to avoid:** Simple, single-step tasks.

**Evidence:**
- DUP (Deeply Understanding Problems): 97.1% on GSM8K with GPT-4 by decomposing semantic understanding errors.
- CGO (ECOOP 2025): Outperformed standard CoT on HumanEval with Llama3-8B (62.4 vs 57.5).
- For code specifically: specifying function signature → expected behavior → edge cases outperforms generic "step by step."

**How to apply for code:**
```
Write a function with the following specification:

Function: [name]
Inputs: [typed parameters with descriptions]
Returns: [return type and meaning]
Behavior: [what it does, step by step]
Edge cases to handle: [list specific edge cases]
Constraints: [performance, library restrictions, etc.]
```

**How to apply for complex reasoning:**
```
To answer this question, follow these steps:
1. First, identify [key variables/entities]
2. Then, determine [relationships/constraints]
3. Next, [intermediate reasoning step]
4. Finally, [synthesize into answer]

Question: [the question]
```

### Output Format Specification

**CRITICAL FINDING: Format restrictions hurt reasoning but help classification.**

**Evidence:**
- Tam et al. (EMNLP 2024 Industry): JSON/XML format constraints cause significant decline in reasoning for GPT-3.5-turbo, Claude-3-haiku, and LLaMA-3-8B. Classification accuracy improves.
- Larger models (GPT-4) are less sensitive to format constraints.

**For reasoning tasks — use the two-step pattern:**
```
[Task description]

First, work through your reasoning in whatever format is most natural. Show your full analysis.

Then, after your reasoning, provide your final answer in this exact format:
ANSWER: [format spec]
```

**For classification/extraction — specify format upfront:**
```
Classify the following text into exactly one of these categories: [A, B, C].

Respond with ONLY the category name, nothing else.

Text: [input]
Category:
```

### Role/Persona Prompting

**The evidence is clear: do NOT use for accuracy-dependent tasks.**

**Evidence:**
- Zheng, Pei & Jurgens (2024): 4 LLM families × 2,410 factual questions — personas do NOT improve performance vs no-persona.
- Luz de Araujo et al. (EMNLP 2025): 9 LLMs × 27 tasks — irrelevant persona details caused drops of up to 30 percentage points.
- WHERE IT HELPS: Open-ended, creative, and brainstorming tasks. Auto-generated expert personas scored 0.3–0.9 points above control on advice/brainstorming.

**If you must use a persona (creative tasks only):**
```
You are a [specific domain expert with relevant expertise].
[Task — keep persona details minimal and directly relevant]
```

**Do NOT use patterns like:** "You are the world's best programmer. You never make mistakes." — this adds no signal and can introduce irrelevant persona sensitivity.

### Context Placement (for RAG and long prompts)

**Evidence:**
- Liu et al. (2023, Stanford): LLMs show U-shaped accuracy — performance peaks when relevant information is at the beginning or end, and degrades in the middle. Tested on GPT-3.5-Turbo, Claude-1.3, MPT-30B.
- Practical impact: 45% precision boost in legal RAG when documents were reranked to top and bottom.

**How to apply:**
```
[System instruction — MOST IMPORTANT, at the start]

[Context documents — place most relevant at START and END]

[Less critical context in the middle]

[The actual question/task — at the END, immediately before model responds]
```

### Self-Consistency (for math/reasoning)

**When to use:** High-stakes reasoning where accuracy matters more than cost.

**Evidence:**
- Wang et al. (ICLR 2023): PaLM-540B GSM8K went from 56.5% (greedy CoT) to 74.4% (+17.9pp). SVAMP +11.0pp, AQuA +12.2pp. Optimal temperature: T=0.7.
- Difficulty-Adaptive Self-Consistency (NAACL 2025): Allocates more samples to harder problems, maintaining performance at lower cost.

**How to apply (in system prompt or pipeline logic):**
```
I will ask you to solve this problem [N] times independently. For each attempt, use a different approach or starting point. Then determine the most common answer.

[Problem]
```

Note: This is primarily a pipeline-level technique (run N calls, majority-vote). In a single prompt, instruct the model to self-verify: "After reaching your answer, verify it using a different method."

---

## Model-size cutoffs

| Model size | Safe techniques | Avoid |
|-----------|----------------|-------|
| **<4B params** | Few-shot, simple instructions, one-shot | CoT, ToT, structured reasoning (increases hallucination up to 75% — Mohanty et al., TMLR 2025) |
| **4B–70B** | Few-shot, simple CoT, decomposition | Complex multi-step reasoning, self-consistency (too noisy) |
| **70B–100B** | All standard techniques | Tree-of-Thought (cost rarely justified) |
| **>100B / frontier** | Everything including CoT, self-consistency, ToT | Nothing fundamentally off-limits — but keep in mind: concise > verbose for code tasks on frontier models |

---

## Sensitivity & robustness

Sclar et al. (ICLR 2024) demonstrated that meaning-preserving formatting changes cause up to 76 accuracy points of variation on LLaMA-2-13B, and ~10 points on average across 50+ tasks. This is NOT eliminated by model size, few-shot, or instruction tuning.

**Practical robustness measures:**
1. Use clear delimiters (XML tags, ```, ---) to separate sections
2. Be consistent with formatting within the prompt
3. Test at least 2 formatting variants for important prompts
4. Prefer the target model's native format (XML for Claude, JSON for GPT)
5. Avoid relying on specific punctuation or whitespace patterns
