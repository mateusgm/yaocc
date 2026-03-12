---
name: prd
model: openai/chatgpt-5.2
ai_generated: partially
description: "Use this skill when the user wants to create, write, or iterate on a PRD (product requirements document), product spec, product brief, opportunity assessment, PR/FAQ, or any similar product definition artifact. Triggers include: mentions of 'PRD', 'product spec', 'product requirements', 'feature spec', 'product brief', 'one-pager', 'PR/FAQ', or requests to define what to build and why. Also use when the user wants to refine a problem statement, scope a feature, or prepare a product proposal for review. Do NOT use for technical design docs, architecture specs, or engineering RFCs — those describe how to build, not what or why."
---

Generate concise, outcome-focused product requirements documents through interactive conversation. The process has three phases: clarify, draft, iterate.

## Phase 1: Clarify

Before writing anything, check out the current state of the project in the working directory to understand where we're starting off and have a short interactive conversation to understand what the user needs and help them refine the idea. Use multiple-choice questions to reduce friction — ask at most 2–3 questions per turn.

**What you need to understand before drafting:**
- What they are building and for whom
- What problem or opportunity they are addressing
- What assumptions are in play
- How they will measure success
- What is explicitly out of scope

Do not follow a rigid script. Adapt your questions based on what the user has already told you, so you can uncover blind spots, hidden risks and angles not considered. Skip questions the user has already answered.

**Template selection:** Present the templates below with a one-line description of each and ask the user which approach fits their situation. 

### Templates

Each templates encodes a different philosophy and suits different situations. They might extra clarification questions, so read the chosen template before proceeding to the next phase.

| Template | File | Best for |
|----------|------|----------|
| **Standard** | `templates/standard.md` | Incremental features / epics  |
| **Figma** | `templates/figma.md` | New products / initiatives |
| **Cagan / SVPG** | `templates/cagan.md` | Assessing if the opportunity is worth pursuing  |
| **Amazon PR/FAQ** | `templates/amazon.md` | Pitches and crystal clear alignment on the north star |

## Phase 2: Draft

Once you have enough context, generate a super concise and crispy PRD following the template's structure and rules. Dont deviate.

### Writing principles

Always apply these regardless of template:
- Focus on what and why - the how (implementation details) has no place here.
- Be specific. Replace "improve the experience" with "reduce task completion time from 5 min to 2 min."
- Write for the skimmer. The TL;DR and headers alone should tell the story.
- State what is out of scope. Unspoken exclusions become implicit inclusions.
- Use plain language. No undefined acronyms. No jargon without context.
- Every sentence must earn its place. Delete anything that does not help the reader decide or act.
- Frame requirements/features as user outcomes: "Users can [action] so that [benefit]."
- Skip sections if they dont earn its place
- Do not include strategy (market analysis, go-to-market plans, pricing models, etc) or implementation (milestones & timelines, tech spec, etc) — those belong in separate docs.

Output Format:
- Output the PRD as a markdown document to `docs/<prd-name>/` folder.
- Prefer prose over tables, bullets over columns, clarity over ceremony.
- No bullet points longer than 2 lines.
- No section longer than half a page.
- Be concise, keep it short. (2-5 pages max)
