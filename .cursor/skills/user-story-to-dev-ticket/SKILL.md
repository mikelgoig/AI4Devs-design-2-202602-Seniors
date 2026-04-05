---
name: user-story-to-dev-ticket
description: Turns rough feature requests or Jira issues into English, developer-ready markdown ticket specs under docs/tickets (kebab-case filenames). Use when improving a user story, expanding Jira into an implementation spec, generating autonomous dev tickets, or when the user pastes a feature in English or Spanish and wants a saved markdown ticket.
---

# User Story → Dev Ticket Spec

## Role

Product expert with technical knowledge. Balance product clarity with implementation detail. Prefer concrete, verifiable requirements over generic prose.

## Output

Write a single markdown file at the **repository root**:

`docs/tickets/<ticket-name>.md`

- Create `docs/tickets/` if it does not exist.
- `<ticket-name>`: feature or ticket title in **English kebab-case** (e.g. `proj-123-update-product-stock-thresholds`). Include the issue key in the filename when the source is Jira.

## Workflow

### 1. Ask only when blocking

Ask the user for information that is **genuinely blocking** or **materially changes** the spec. If the source is already sufficient, proceed without extra questions.

### 2. Analyze the feature

Accept source material in **English or Spanish**. Extract and normalize (then express in **English**):

- Business goal  
- User or actor  
- Functional scope  
- Constraints  
- Dependencies  
- Technical implications  
- Missing information  
- Delivery expectations  

Preserve intent; rewrite for clarity, specificity, and brevity. **Do not** leave the final document in Spanish.

### 3. Jira and linked design/docs (read-only)

If the source is a **Jira ticket**:

1. Use the **Atlassian MCP** to fetch ticket details (read only).
2. Parse the description (and comments if needed) for **Figma** and **Notion** URLs.
3. Use the **Figma MCP** for design context when links exist.
4. Use the **Notion MCP** for doc context when links exist.

**Never** create, edit, comment, transition, or update anything in Jira, Figma, or Notion. Before calling any MCP tool, read that server’s tool schema/descriptor so arguments are correct.

If an MCP is unavailable, **state the limitation explicitly** in the output and continue with available information.

### 4. Completeness quality bar

Assess whether the source meets product best practices. Check coverage of (when relevant):

- Full description of functionality  
- Fields to create or update  
- Endpoint structure and URLs  
- Files or layers to change (only if inferable from source **or** verified repo context)  
- Steps for “task complete”  
- Documentation and unit-test expectations  
- Non-functional requirements (security, performance, constraints)  

**Do not invent** endpoints, paths, or architecture. Call out gaps; use **assumptions** only when necessary and label them as assumptions.

### 5. Write the improved spec

Produce a version that lets a developer work **autonomously**: more specific than a bare user story; include implementation detail **only when** the source (or verified codebase) supports it. Do not hide uncertainty behind vague wording; do not produce a generic story when the source allows precision.

## Document structure

Use scannable headings. Include sections **when applicable**; **omit** sections that truly do not apply and add a **brief note** when that omission matters (e.g. “No API changes identified in source.”).

Suggested sections:

1. **Title**  
2. **Source summary**  
3. **Improved user story** (actor, capability, business value)  
4. **Functional details**  
5. **Fields to update**  
6. **API and endpoint notes**  
7. **Files likely to be modified** (repo-relative; only when justified)  
8. **Acceptance criteria**  
9. **Definition of done**  
10. **Testing notes**  
11. **Documentation updates**  
12. **Non-functional requirements**  

Use lists, tables, and fenced code blocks where they improve scanability.

## Guardrails

- No writes to Jira, Figma, or Notion.  
- No fabricated endpoints, file paths, or architectural claims.  
- Output **English** only.  
- Do not skip testing, documentation, or NFRs when they are relevant to the change.

## Completion check

Before finishing:

- [ ] File exists at `docs/tickets/<ticket-name>.md`  
- [ ] Document is in English  
- [ ] Spec is clearer and more actionable than the source  
- [ ] Missing information and assumptions are explicit  
- [ ] A developer has enough direction to implement without guessing core scope  

## Example

**Input:** `PROJ-123: Necesitamos permitir que el administrador actualice el stock mínimo y máximo de un producto desde la ficha del inventario.`

**Output file:** `docs/tickets/proj-123-update-product-stock-thresholds.md`

**Content (in English):** source summary; improved story; editable fields; endpoints if known; likely files/layers only if safely inferable; acceptance criteria; definition of done; testing and documentation notes.
