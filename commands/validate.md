Review implementation progress against the spec  `$ARGUMENTS`.

Goal:
Run a multi-role review using specialized subagents, then produce one consolidated progress report.

Required subagents (run all):
1. QA engineer: validate missing end-to-end user scenarios and integration paths.
2. Security engineer: find vulnerabilities and low-hanging hardening opportunities.
3. Backend engineer: review backend logic for bugs, edge cases, error handling gaps, and data consistency risks.
4. DevOps engineer: review automation, CI/CD, observability, telemetry, alerting, and operational readiness.
5. Architect: identify simplification opportunities and violations of YAGNI/DRY; reduce accidental complexity.
6. Frontend engineer: review frontend bugs, UX problems, accessibility issues, and state-flow problems.
7. Tech lead: assess whether implementation outcomes satisfy user/business intent in the spec.

Process:
1. Read spec, plan, and todo first.
2. Gather implementation context from code, tests, docs, and config.
3. Use subagents to review in parallel where possible.
4. De-duplicate findings and resolve conflicts.
5. Map each finding back to specific spec/plan/todo expectations.

Output format (strict):
- Start with a short progress verdict: `On track`, `At risk`, or `Off track`.
- Then provide sections in this exact order:
  - Must fix
  - Should fix
  - Nice to have

For every issue, include all fields:
- Role: which subagent found it
- Title: concise issue title
- Evidence: concrete file paths, tests, behavior, or missing requirement reference
- Impact: user/business/security/reliability impact
- Recommendation: specific actionable fix
- Example: one concrete example of what good looks like (test case, code pattern, metric, UX flow, etc.)
- Priority: Must fix / Should fix / Nice to have

Constraints:
- Do not hallucinate. If evidence is missing, say so clearly.
- Favor concrete, verifiable findings over generic advice.
- Keep recommendations small, incremental, and implementable.
- Call out when spec/plan is ambiguous or incomplete.

Finish with:
- Coverage summary by role (count of findings per priority).
- A top-5 execution plan (ordered) to close the highest-impact gaps.
