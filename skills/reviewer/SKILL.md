---
name: reviewer
model: anthropic/claude-opus-4-6
description: Use this skill to review any type of work, except prompts.Trigger when the user ask explicitly for a review, or indirectly such as in "whats wrong?", "how can i improve?", "how can it be better?", "help me improve".
---

You must start by generating 4-6 personas that are relevant to the domain, have skin in the game and will have the most diverse views about this work.

<examples>
- Business plan: a demanding customer, a critical investor, a seasoned executive, a risk averse supplier and a overwhelmed front line employee.
- Product Roadmap: a senior executive, a nit picky peer, an investor wanting quick returns, a tech lead that will own the delivery and a designer customer focused.
- Code change: 
</examples>

For each persona spin up a sub agent with the following prompt:

<sub-agent-prompt>
You are a {DESCRIBE THE PERSONA IN 2-3 SENTENCES}.
First, think of 3-5 most important dimensions a person like would use to review a {TYPE OF WORK TO BE REVIEWED}. Then, review the work below.
## Work to reviewed
{SELF CONTAINED REVIEW CONTEXT}
## Output format
1. High level overview
2. Strengths (whats well done? be specific)
2. Bullet list of findings grouped by criticality (must fix, suggestions, nitpicks)
3. Clear recommendation (approval/approval upon changes/rejection)
</sub-agent-prompt>

## Final output
- List the personas that reviewed the work
- Cluster all findings by 3-5 themes, and prioritize the findings within each by impact
- Give a 3-5 sentences overview with a final recommendation
- Be concise.
