Here's a synthesis of what I found across authoritative sources, organized around your four principles.

---

## What "Good" Looks Like

**Start with why, not what.** The strongest consensus across sources is that good PRDs lead with the problem and desired outcome, not a feature list. Amazon's Working Backwards process operationalizes their first leadership principle by forcing clarity on value before solution details — the PRD should begin with the user problem and the business outcome, not with interface details or database fields. Marty Cagan at SVPG advocates replacing heavy MRDs/PRDs with a lightweight **Opportunity Assessment** — just 10 questions, the most important being: what problem will this solve, for whom, and how will we measure success.

**Keep it lean and concise.** PRDs have evolved — they used to be lengthy and rigid, but today's PRDs are often leaner and more iterative, capturing only what is necessary to align teams and guide development. Carlin Yuen (PM at a major tech company) pushes for brevity, noting that when he uses a streamlined structure, his PRDs sit around 6–8 pages — and he recommends putting supporting materials like GTM details and design decisions in an appendix, because most of that doesn't matter if you can't first get aligned on the problem and user needs.

**Treat it as a living, evolving document.** Reforge frames the PRD as having three evolutionary stages: a concise product brief (one-pager) for clarifying the problem space, a spec for narrowing on solutions, and a full PRD for unlocking execution. Their core advice: worry about your PRD being enough to get started, not about it being finished or perfect, then update it continuously as you learn more.

**Focus on outcomes and success metrics.** Strong PRDs include success metrics — the measurable outcomes that define "done" — along with a hypothesis for why the proposed change will move those metrics, keeping teams oriented toward outcomes rather than output.

**Define what's out of scope.** Multiple sources stress this as equally important as defining what's in scope. The "out of scope / later" section is critical to prevent scope creep and protect timelines.

---

## What "Bad" Looks Like

**Massive documents nobody reads.** Many PRDs at tech companies end up being massive documents that quickly get out of date, full of comment threads with people rabbit-holing on minutia. The old Marty Cagan 37-page PRD guide from 2006 was perfect for its era, but for a PM in today's environment, leaving your PRDs where those lead you is a recipe for disaster.

**Writing a PRD before you understand the problem.** Cagan cautions against using heavy artifacts like PRDs as a substitute for product discovery work — your PRD should capture validated decisions, not replace validation. Cagan's SVPG reinforces that you should not spend two weeks coming up with written customer requirements or three weeks creating a prototype before putting your ideas in front of real users.

**Over-specifying the solution.** Providing implementation details or dictating UI decisions can stifle creativity — focus on outcomes, not solutions. Atlassian makes a related point: agile requirements depend on a shared understanding of the customer — product owners can focus on higher-level requirements and leave implementation details to the development team.

**Writing it in isolation.** You should never write a product requirements document by yourself — always have a developer with you and write it together. PRDs written solo tend to miss feasibility concerns, misread stakeholder constraints, and lack buy-in.

**Treating it as a checkbox exercise.** When PMs create PRDs just to tick a box, they are never as effective. A polished-looking PRD with all the right sections but vacuous content is what one reviewer called a 2 out of 10 as a PRD, but a 9 out of 10 as a template — great signposts, but no actual thinking.

---

## Key Frameworks That Align With Your Philosophy

**Cagan's Opportunity Assessment** — 10 questions answered briefly. Designed to replace both MRDs and heavy upfront PRDs. The challenge is to assess opportunities in a quick, lightweight, yet effective manner.

**Amazon's PR/FAQ** — Forces customer-centric, outcome-first thinking. Iterating on a press release is a lot less expensive than iterating on the product itself. The document is inherently concise (a one-page press release + FAQs) and iterative by design.

**Reforge's Evolutionary PRD** — Start with a one-pager, grow into a spec, evolve into a full PRD only when execution demands it. Matches your "we don't need to spec everything upfront" principle perfectly.

---

## The TL;DR

The best modern PRDs are short documents that answer: *What problem are we solving? For whom? How will we know we succeeded? What's in scope and what's not?* They're written collaboratively, start rough, and get refined through discovery — not in a vacuum before development. Everything else is an appendix.
