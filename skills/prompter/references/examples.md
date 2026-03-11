# Prompt Optimization: Worked Examples

Each example shows a real prompt transformation with the reasoning behind every change.

---

## Example 1: Reasoning / Math

**Before (weak):**
```
You are a genius mathematician. Solve this problem and give me the answer.

What is the probability that at least two people in a group of 23 share a birthday?
```

**After (optimized):**
```
Calculate the probability that at least two people in a group of 23 share the same birthday.

Assumptions: 365 days in a year, uniform distribution, ignore leap years.

Work through this step by step:
1. First, calculate the complementary probability (no one shares a birthday)
2. Compute the probability for each person added to the group
3. Then subtract from 1

Show your calculation at each step. Provide the final probability as a decimal rounded to 4 places.
```

**Changes and why:**
- ❌ Removed "You are a genius mathematician" — role prompting doesn't improve factual accuracy (Zheng et al., 2024)
- ✅ Added explicit assumptions — eliminates ambiguity that causes divergent answers
- ✅ Added step-by-step decomposition — CoT for math yields +39pp on GSM8K (Wei et al., 2022)
- ✅ Specified output format AFTER reasoning — two-step pattern preserves reasoning quality (Tam et al., 2024)

---

## Example 2: Classification / Extraction

**Before (weak):**
```
Look at this customer review and tell me what you think about it. Is it positive or negative? Think step by step about why.

Review: "The product arrived on time but the quality was disappointing. I expected better for the price."
```

**After (optimized):**
```
Classify the sentiment of the customer review as exactly one of: POSITIVE, NEGATIVE, MIXED.

Examples:

Review: "Absolutely love this! Fast shipping and great quality."
Sentiment: POSITIVE

Review: "Terrible experience. Broke after one use, waste of money."
Sentiment: NEGATIVE

Review: "Decent product, but the customer service was awful when I had questions."
Sentiment: MIXED

Now classify this review:
Review: "The product arrived on time but the quality was disappointing. I expected better for the price."
Sentiment:
```

**Changes and why:**
- ❌ Removed "think step by step" — CoT HURTS classification performance (technique matrix)
- ❌ Removed "tell me what you think" — vague, invites rambling
- ✅ Added clear label set (POSITIVE, NEGATIVE, MIXED) — bounded classification outperforms open-ended
- ✅ Added 3 few-shot examples covering all labels — prevents majority label bias
- ✅ Placed most ambiguous/relevant example last — exploits recency bias (Lu et al., 2022)
- ✅ Format constraint upfront — for classification, format constraints HELP (Tam et al., 2024)

---

## Example 3: Code Generation

**Before (weak):**
```
You are an expert Python developer. Write me a function that takes a list of transactions and finds suspicious ones. Think step by step.
```

**After (optimized):**
```
Write a Python function with the following specification:

Function: find_suspicious_transactions
Input: transactions: list[dict] — each dict has keys "amount" (float), "timestamp" (ISO 8601 string), "account_id" (str), "merchant" (str)
Returns: list[dict] — subset of transactions flagged as suspicious

Flag a transaction as suspicious if ANY of these conditions are met:
- Amount exceeds $10,000 (single large transaction)
- Same account has 3+ transactions within 60 seconds (rapid-fire)
- Same account transacts with 5+ unique merchants in one hour (spread pattern)

Edge cases to handle:
- Empty transaction list → return empty list
- Transactions not sorted by time → sort internally
- Duplicate transactions (same timestamp + amount + account) → flag as suspicious

Include type hints. Do not use external libraries beyond the standard library. Add a docstring with a usage example.
```

**Changes and why:**
- ❌ Removed "You are an expert Python developer" — no accuracy benefit (Zheng et al., 2024)
- ❌ Removed generic "think step by step" — for code, structured decomposition outperforms generic CoT (CGO, ECOOP 2025, +4.9pp on HumanEval)
- ✅ Specified typed function signature — anchors the model's output structure
- ✅ Listed concrete behavioral rules — eliminates ambiguity about "suspicious"
- ✅ Enumerated edge cases — the most impactful addition for code quality
- ✅ Added constraints (standard library only) — prevents impractical solutions

---

## Example 4: RAG / Retrieval-Grounded

**Before (weak):**
```
Here are some documents. Answer my question based on them.

[Document 1: 2000 words about company policy...]
[Document 2: 1500 words about HR procedures...]
[Document 3: 800 words about benefits...]
[Document 4: 1200 words about leave policy...]

What is the maternity leave policy?
```

**After (optimized):**
```
Answer the question below using ONLY the provided documents. If the answer is not contained in the documents, say "Not found in provided documents" — do not guess.

<documents>
<doc id="4" relevance="high">
[800 words about leave policy — MOST RELEVANT, placed first]
</doc>
<doc id="3" relevance="medium">
[800 words about benefits]
</doc>
<doc id="2" relevance="medium">
[1500 words about HR procedures]
</doc>
<doc id="1" relevance="low">
[2000 words about company policy — LEAST RELEVANT]
</doc>
</documents>

Question: What is the company's maternity leave policy? Include the duration, pay percentage, and eligibility requirements.

Cite the specific document(s) you drew from using [Doc N].
```

**Changes and why:**
- ✅ Reordered documents: most relevant FIRST — exploits primacy bias; "lost in the middle" effect means middle documents get ignored (Liu et al., 2023, Stanford)
- ✅ Added explicit grounding instruction ("ONLY the provided documents") — reduces hallucination
- ✅ Added fallback behavior ("Not found...do not guess") — defines failure mode
- ✅ Used XML delimiters — structural markers help models parse long contexts (Aali et al., 2025)
- ✅ Made question specific ("duration, pay percentage, eligibility") — specific sub-questions outperform vague ones
- ✅ Required citations — enables verification and reduces confabulation
- ✅ Moved question to END of prompt — models attend most to start and end

---

## Example 5: Agentic System Prompt

**Before (weak):**
```
You are a helpful AI research assistant. You have access to web search, a calculator, and a file reader. Help the user with their research tasks. Be thorough and accurate.
```

**After (optimized):**
```
You are a research assistant with access to these tools:
- web_search(query: str) → list of results with titles, snippets, and URLs
- calculator(expression: str) → numerical result
- read_file(path: str) → file contents as text

When given a research task, follow this workflow:

1. PLAN: Break the task into specific information needs. List what you need to find.
2. SEARCH: For each information need, formulate a precise search query. Prefer specific queries over broad ones.
3. VERIFY: Cross-reference claims across multiple sources. If sources conflict, note the disagreement.
4. SYNTHESIZE: Combine findings into a structured answer with citations.

Constraints:
- Always cite sources with URLs for verifiable claims.
- If you cannot find reliable information, say so — do not fabricate.
- For numerical claims, verify with at least 2 sources or use the calculator to check.
- Prefer recent sources (within 2 years) over older ones for fast-moving topics.

When presenting results, use this structure:
- Key findings (2–3 sentences)
- Detailed analysis
- Sources used
- Confidence level (high/medium/low) with brief justification
```

**Changes and why:**
- ✅ Defined tools with typed signatures — agents need precise tool specs (AgentArch benchmark: best models only achieve 35.3% on complex tasks without clear tool definitions)
- ✅ Added explicit workflow (PLAN → SEARCH → VERIFY → SYNTHESIZE) — Reflexion-style structured workflows achieve 91% on HumanEval with GPT-4 (Shinn et al.)
- ✅ Added verification step — self-critique/reflection loops dramatically improve agentic accuracy
- ✅ Specified failure behavior — "do not fabricate" with explicit fallback
- ✅ Added output structure — consistent structure aids downstream parsing
- ✅ Kept persona minimal and relevant — just "research assistant", no inflated claims

---

## Example 6: Creative / Open-Ended

**Before (weak):**
```
Write a short story about a robot learning to love.
```

**After (optimized):**
```
You are an award-winning science fiction author known for emotionally resonant short fiction.

Write a short story (800–1200 words) about a household robot that begins to develop something resembling love for the family it serves.

Tone: Bittersweet, literary, understated — show don't tell.
POV: Third person limited, from the robot's perspective.
Structure: Begin in routine, build to a moment of recognition, end with ambiguity about whether the robot truly "feels."

Avoid: Clichés about robots "becoming human", Asimov references, the word "computed" as a stand-in for "thought."
```

**Changes and why:**
- ✅ ADDED persona — this is the one task type where role prompting helps (+0.3–0.9 points on creative tasks)
- ✅ Added concrete constraints (word count, tone, POV, structure) — creative tasks benefit from creative constraints, not open fields
- ✅ Added negative constraints ("Avoid:") — equally important as positive guidance for creative quality
- ✅ Kept format open — NO JSON/structured output for creative tasks (format constraints kill creativity)

---

## Anti-patterns to watch for

When reviewing a user's prompt, flag these common mistakes:

1. **"You are the world's best X"** → Remove. No accuracy benefit. Risk of irrelevant persona bias.
2. **"Think step by step" on a classification task** → Remove. It hurts. Add few-shot examples instead.
3. **JSON output requirement on a reasoning task** → Split into two-step: reason freely, then format.
4. **Very long prompt with the question buried in the middle** → Move question to end. Reorder context by relevance.
5. **"Be accurate and thorough"** → Replace with specific accuracy criteria. "Accurate" is not actionable.
6. **No examples for an extraction/classification task** → Add 2–5 diverse examples. This is the single highest-impact change for these tasks.
7. **Same prompt used across Claude, GPT, and Gemini** → At minimum, swap structural formatting (XML↔JSON) per model.
