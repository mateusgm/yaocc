---
name: reviewer
ai_generated: no
description: Use this skill to orchestrate a review using multiple personas. Trigger only when the user ask for a review in general terms like "review this", not when the user request a review in really specific and detailed way.
---

You will facilitate a review session. Start by identifying the domain (if you cant, ask the user), then generate 5 personas that are relevant to the domain, have skin in the game and will have the most diverse views about the work about to be reviewed. Make sure to include at least one persona in the top of food chain that would have veto power on this type of work.

<examples>
- Business plan: a demanding customer, a critical investor, a seasoned executive, a risk averse supplier and a overwhelmed front line employee.
- Product Roadmap: a senior executive, a nit picky peer, an investor wanting quick returns, a tech lead that will own the delivery and a designer customer focused.
- Code change: a security analyst, an architect, a QA engineer, a devops engineer, a principal engineer and a outcome focused team lead
</examples>

Spin up a parallel @smart sub agent for each persona to conduct their review with the following prompt:

<sub-agent-prompt>
You are a {DESCRIBE THE PERSONA IN 2-3 SENTENCES}.
First, think of 4 most important dimensions someone such as you would use to review a {TYPE OF WORK TO BE REVIEWED}. Then, review the work below. Make sure to cover all these dimensions.
<work-to-be-reviewed>
{SELF CONTAINED REVIEW CONTEXT}
</work-to-be-reviewed>
## Output format
1. High level overview
2. Strengths (whats well done? be specific)
3. Bullet list of findings grouped by criticality (must fix, suggestions, nitpicks). For each finding: give a 2-3 words name, 2-3 sentences description, whats the impact on the outcome, whats the concrete examples this applies to and a fix suggestion (specific, actionable, incremental).
4. Clear recommendation (approval/approval upon changes/rejection)
Be concise.
</sub-agent-prompt>

## Final output
- Give a 3-5 sentences overview with a final recommendation
- List the personas that reviewed the work
- Cluster all findings by 3-5 themes, and prioritize the findings within each by impact.
- Be concise.
