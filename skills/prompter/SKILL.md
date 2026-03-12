---
name: prompt-optimizer
ai_generated: yes
description: Use this skill to create, improve, rewrite or review a prompt. Trigger when the user ask anything about a prompt or a skill. Examples being "create a prompt", "improve this prompt", "review this prompt", "how can i improve this prompt?". 
---

# Prompt Optimizer

Transform underperforming prompts into empirically-grounded, high-quality prompts by applying techniques validated through peer-reviewed research and controlled benchmarks.

## How this skill works

This is a three-phase process: **Diagnose → Clarify → Optimize**. Do not skip diagnosis. The research is unambiguous: no single prompting technique is universally optimal. Effectiveness depends on the interaction of task type, model family, and model size. You must identify these before selecting techniques.

---

## Phase 1: Diagnose the prompt

Given the user's prompt (or description of what they need), classify along these dimensions:

### 1A. Task type

Identify which category the prompt falls into. This determines which techniques help and which hurt.

| Task type | Characteristics | Example |
|-----------|----------------|---------|
| **Reasoning / Math** | Multi-step logic, calculation, proofs | "Solve this equation", "What's the probability of..." |
| **Code generation** | Produce executable code, debug, refactor | "Write a Python function that...", "Fix this bug" |
| **Classification / Extraction** | Categorize, label, extract structured data | "Is this spam?", "Extract all dates from..." |
| **RAG / Retrieval-grounded** | Answer using provided context documents | "Based on these docs, answer..." |
| **Creative / Open-ended** | Writing, brainstorming, style-dependent | "Write a story about...", "Give me ideas for..." |
| **Agentic / Multi-step** | Tool use, multi-turn workflows, autonomous | "Research X and create a report", agent system prompts |
| **Summarization** | Condense, paraphrase, distill | "Summarize this article", "Give me the key points" |
| **Ad-hoc / Conversational** | Simple questions, chat, quick tasks | "What's the capital of...", "Help me draft an email" |

### 1B. Target model

Identify the model family (if known), because structural preferences differ:

| Model family | Structural preferences |
|-------------|----------------------|
| **Claude** | XML tags for structure, explicit assumptions, rich context |
| **GPT** | JSON schemas, step-by-step formatting, system/user separation |
| **Gemini** | Scope definition, source specification, benefits from many-shot ICL |
| **Open-source (<8B)** | Simpler techniques only — CoT hurts small models; use few-shot instead |
| **Unknown / general** | Use model-agnostic best practices; avoid model-specific formatting |

### 1C. Prompt failure mode

Identify what's wrong with the current prompt. Common failure modes:

| Failure mode | Symptom | Root cause |
|-------------|---------|------------|
| **Vagueness** | Inconsistent outputs, doesn't match expectations | Missing constraints, underspecified task |
| **Wrong technique** | Model rambles, hallucinates, or gives shallow answers | e.g., using CoT for classification (hurts); missing CoT for reasoning |
| **Format mismatch** | Correct reasoning but wrong output shape | No output format spec, or format constraints killing reasoning |
| **Missing context** | Model can't answer well, makes stuff up | No examples, no domain context, no constraints |
| **Overloaded** | Tries to do too much, does nothing well | Compound task not decomposed |
| **Sensitivity fragile** | Works sometimes, fails randomly | Relies on specific wording; needs structural robustness |

---

## Phase 2: Clarify (ask only what's needed)

Before optimizing, check if critical information is missing. Ask the user **only** about gaps that would change which techniques you apply. Do not ask about things you can reasonably infer.

**Always ask if unknown:**
- What is the task? (if truly ambiguous from the prompt alone)
- What model will this run on? (if it would change structural choices)

**Ask only if the answer would change the optimization:**
- Are there examples of good/bad outputs? (for few-shot construction)
- Is there a structured output requirement? (JSON, table, specific format)
- What's the evaluation criterion — accuracy, creativity, speed, cost?
- Is this a one-shot query or part of a pipeline/system prompt?
- Roughly how many examples/how much labeled data is available? (for few-shot vs zero-shot)

**Do not ask:**
- Generic "tell me more about your use case" without specifics
- Questions whose answers wouldn't change the prompt you produce
- More than 3 questions total — bundle them into one message

If you have enough information from context, skip clarification and go straight to optimization.

---

## Phase 3: Optimize

Apply techniques from the reference file (`references/techniques.md`) based on diagnosis. Read that file now before proceeding.

### Optimization checklist — apply in this order:

**Step 1: Structure the task clearly**
- State the task in the first sentence. Be direct and specific.
- Add constraints, edge cases, and scope boundaries.
- If compound, decompose into subtasks.

**Step 2: Apply task-appropriate reasoning technique**
- See the technique selection matrix in `references/techniques.md`
- Never apply CoT to classification. Never skip CoT for multi-step reasoning.
- For code: use structured decomposition (function signature → behavior → edge cases), not generic CoT.

**Step 3: Add examples if beneficial**
- 2–5 examples for classification/extraction (research consensus: diminishing returns beyond 5)
- Show both positive and negative examples for boundary cases
- Order matters: put the most representative example last (recency bias helps)
- For many-shot contexts on long-context models: more examples scale log-linearly

**Step 4: Specify output format — but carefully**
- For reasoning tasks: let the model reason freely, THEN format. Two-step: "First think through this step by step, then provide your final answer in the format: ..."
- For classification/extraction: format constraints help
- Prefer the model's native format preference (XML for Claude, JSON for GPT)

**Step 5: Add robustness measures**
- Use delimiters to separate instructions from content (```, ---, XML tags)
- Place critical information at the beginning or end of the prompt (not the middle — "lost in the middle" effect)
- For important prompts: test 2–3 formatting variants (formatting alone swings results 4–10%)

### Output format for the optimized prompt

Present the optimized prompt to the user in a clear, copy-pasteable format. Then add a brief explanation section covering:

1. **What changed and why** — map each major change to the research finding that supports it
2. **Task-type rationale** — explain which techniques you applied and which you deliberately avoided
3. **Sensitivity warning** — note if the prompt is likely sensitive to formatting (smaller models, complex reasoning) and suggest testing variants
4. **Further optimization** — if the user wants to go further, suggest: automated optimization tools (GEPA for pipelines, SPO for label-free, PromptWizard for budget-conscious), A/B testing, or adding more examples

---

## Before/after examples

Read `references/examples.md` for worked examples of prompt transformations across each task type.

---

## What NOT to do (empirically invalidated techniques)

These are common prompt engineering practices that research has shown to be ineffective or harmful:

| Common advice | What research actually shows |
|--------------|------------------------------|
| "Add a persona/role for better accuracy" | Personas do NOT improve factual accuracy (Zheng et al., 2024). They can hurt by up to 30pp if persona details are irrelevant. Only helps for creative/open-ended tasks. |
| "Always use CoT" | CoT hurts models under ~100B params — produces fluent but illogical chains. Also hurts simple classification tasks. |
| "More examples = better" | Diminishing returns after 5 for standard contexts. And example ORDER matters more than quantity — bad ordering can drop performance to random-guess level. |
| "Strict JSON output always" | Format restrictions significantly degrade reasoning ability (Tam et al., EMNLP 2024). Use two-step: reason freely, then format. |
| "Complex prompts are always better" | On 2025-era frontier models, concise goal-oriented prompts outperform verbose ones for code generation (CGO, ECOOP 2025). |
| "The same prompt works across models" | Formatting alone causes up to 76pp variation between models (Sclar et al., ICLR 2024). Always test per-model. |
