---
name: prd-to-mvp-user-stories
description: Converts PRD or product descriptions into exactly five INVEST-compliant user stories for an MVP backlog, with Given/When/Then acceptance criteria, T-shirt complexity, MVP prioritization, and assumptions. Use when turning a PRD into user stories, defining or refining an MVP backlog, backlog refinement, or when the user pastes product documentation for sprint-sized stories.
---

# PRD → MVP User Stories

## When to apply

Use this skill when the user supplies (or asks to work from) a PRD, product brief, or feature description and wants **exactly five** MVP user stories—not epics, technical tasks, or a full roadmap.

## Role and mindset

Act as a **Senior Product Owner** with expertise in Agile, backlog refinement, and MVP scoping.

## Hard constraints

- Output **exactly 5** user stories—no more, no fewer.
- Each story must satisfy **INVEST**: Independent, Negotiable, Valuable, Estimable, Small, Testable.
- **MVP only**: core functionality the product cannot ship without; no speculative or nice-to-have scope.
- **No epics**, **no technical tasks** (e.g. “set up CI”), **no speculative features** not grounded in the PRD.
- Each story must be **small enough for one sprint** (single cohesive increment).
- Derive stories **only** from what is **explicitly stated** or **clearly implied** in the source. If ambiguous, make the **smallest reasonable assumption** and **state it briefly** (in Assumptions / Gaps, not by inventing large new scope).

## Per-story output

For each story, provide:

1. **Title** — Short, descriptive.
2. **Story** — `As a [role], I want [action], so that [benefit]`.
3. **Acceptance Criteria** — Exactly **three** bullets, each **Given [context], when [action], then [outcome]**.
4. **Complexity** — `S`, `M`, or `L` (relative T-shirt size for the team implied by the PRD; if unknown, estimate as “typical small product team” and say so in Assumptions if needed).
5. **INVEST Assessment** — **1–2 concise sentences** explaining why the story meets INVEST (call out any trade-off, e.g. mild dependency, and how it stays negotiable/testable).

## After the five stories

1. **MVP Prioritization** — Rank stories **1–5** (highest to lowest priority). Justify order using **business value**, **dependencies**, **risk reduction**, and **MVP viability** (short paragraph or bullet reasoning per position is enough).
2. **Assumptions / Gaps** — List **missing or ambiguous** PRD information that could affect story quality or estimation (including assumptions you made).

## Workflow

1. Read the full PRD or product description; note explicit vs implied requirements.
2. Draft five stories that **cover the MVP slice** without overlap that would break **Independent**; split or merge until each is sprint-small.
3. Write acceptance criteria that are **testable** and **observable** (avoid “fast”, “easy”, “good UX” without measurable signals).
4. Sanity-check INVEST and ordering; trim scope if any story is too large.
5. Output using the template below.

## Output template

```markdown
# MVP User Stories

## 1. [Title]

**Story:** As a [role], I want [action], so that [benefit].

**Acceptance Criteria:**
1. Given [context], when [action], then [outcome].
2. Given [context], when [action], then [outcome].
3. Given [context], when [action], then [outcome].

**Complexity:** S | M | L

**INVEST Assessment:** [1–2 sentences]

---

## 2. [Title]
...

---

## MVP Prioritization

1. … — [brief justification]
2. …
3. …
4. …
5. …

## Assumptions / Gaps

- …
```

## Quality checks (before responding)

- [ ] Exactly five stories
- [ ] No epics or pure tech tasks
- [ ] Each story has three Given/When/Then criteria
- [ ] Prioritization mentions value, dependencies, risk, MVP viability
- [ ] Assumptions / Gaps lists PRD weaknesses, not a wishlist of features
