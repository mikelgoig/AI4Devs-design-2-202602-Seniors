# MVP User Stories

## 1. Create and publish a job with a configurable pipeline

**Story:** As a recruiter, I want to create a job with an ordered set of pipeline stages and publish it, so that candidates can apply and applications progress through our hiring steps.

**Acceptance Criteria:**
1. Given I am signed in as a recruiter, when I create a job with title, description, and requirements and save ordered pipeline stages (e.g. Applied → Phone Screen), then the job and stages are persisted and stages appear in that order.
2. Given a job exists in draft (or unpublished) state, when I publish the job, then its status shows as published and the job is eligible to receive applications.
3. Given a published job, when I open the job detail view, then I see the same pipeline stages and can distinguish terminal vs non-terminal stages if the product supports that flag (per data model).

**Complexity:** M

**INVEST Assessment:** Delivers a standalone recruiting container and stages without depending on AI. Negotiable in how many stages and which fields are required on day one; testable via API/UI persistence and publish state.

---

## 2. Submit an application with resume on a published job

**Story:** As a candidate, I want to apply to a published job and attach my resume, so that the organization receives my profile for that role.

**Acceptance Criteria:**
1. Given a published job with a public or candidate-facing apply entry point, when I submit required application fields and a resume file, then an Application is created linking me to that job at the job’s initial pipeline stage.
2. Given a successful submission, when the recruiter opens that job’s applicant list, then my application appears with my identity fields and applied timestamp.
3. Given I uploaded a resume file, when submission completes, then the resume is stored and associated with my candidate profile for later viewing or processing.

**Complexity:** M

**INVEST Assessment:** Independent of AI timing (application exists before scores). Valuable as the intake path; estimable as standard CRUD + upload; testable end-to-end from apply to recruiter visibility.

---

## 3. Automatically parse resumes and produce AI screening results

**Story:** As a recruiter, I want each new application to be parsed and scored against the job with a short AI summary, so that I get structured signal without manually reading every resume first.

**Acceptance Criteria:**
1. Given a new application with a stored resume, when asynchronous AI processing finishes, then structured parsed data (e.g. skills, experience) is persisted on the candidate/application record per the product’s model.
2. Given processing completes, when I retrieve the application’s screening outcome, then I see a numeric overall score and a one-paragraph AI summary explaining fit.
3. Given the bias-detection step runs, when screening completes, then bias-related signals are stored (e.g. `bias_flags` / equivalent) and exposed for recruiter review alongside the score.

**Complexity:** L

**INVEST Assessment:** Core differentiator from the PRD; depends on application + job existing but can be built behind a queue with clear completion states. Negotiable on model quality thresholds; testable with fixture resumes and expected stored fields.

---

## 4. Review an AI-ranked list, adjust weights, and filter applications

**Story:** As a recruiter, I want a ranked view of applicants with adjustable scoring weights and filters, so that I can tune shortlisting to the role and quickly focus on the strongest matches.

**Acceptance Criteria:**
1. Given several applications on a job with completed screening scores, when I open the ranked dashboard for that job, then applications are listed in descending order of overall AI score by default.
2. Given weighting controls are available for the role, when I change weights and request a recalculation, then rankings refresh to reflect the new ordering within a bounded processing time.
3. Given filter controls (e.g. by score band or structured field), when I apply a filter, then only applications matching the criteria remain visible until the filter is cleared.

**Complexity:** M

**INVEST Assessment:** Delivers recruiter-facing value on top of stored scores; mild dependency on story 3 for meaningful ranking. Testable by seeding applications with known scores and asserting order and filter results.

---

## 5. Move candidates to a pipeline stage and send acknowledgment

**Story:** As a recruiter, I want to advance selected candidates to a target pipeline stage and trigger an acknowledgment to the candidate, so that pipeline state stays accurate and candidates are not left wondering whether we received their application.

**Acceptance Criteria:**
1. Given one or more applications on a job, when I move them to a chosen pipeline stage, then each application’s current stage updates and a stage-change timestamp (or equivalent) is recorded.
2. Given a move completes for an application, when the system sends the candidate acknowledgment, then a notification record or outbound message is created for that candidate for the configured channel (e.g. email or in-app).
3. Given I moved candidates to a downstream stage (e.g. Phone Screen), when I view the job pipeline, then those applications appear under the correct stage counts or column.

**Complexity:** S

**INVEST Assessment:** Closes the UC1 loop from ranking to workflow progression; small and testable. Negotiable on channel (email vs in-app only); dependency on stages from story 1 is explicit but does not block testing in isolation with seeded data.

---

## MVP Prioritization

1. **Create and publish a job with a configurable pipeline** — No job or stages means no applications, no screening context, and no pipeline to move candidates through; highest dependency and baseline viability.
2. **Submit an application with resume on a published job** — Without intake, there is nothing to parse or rank; required before any AI value is observable.
3. **Automatically parse resumes and produce AI screening results** — Matches the PRD’s recommended MVP centerpiece (§8): largest efficiency and data foundation for later collaboration and scheduling.
4. **Review an AI-ranked list, adjust weights, and filter applications** — Turns raw scores into recruiter action; depends on screening completing but is the main “why switch from spreadsheets” moment in UC1.
5. **Move candidates to a pipeline stage and send acknowledgment** — Completes the recruiter loop and candidate experience touchpoint in UC1; lower priority than having scores and a ranked view, but still needed for a credible end-to-end hiring slice.

---

## Assumptions / Gaps

- **MVP scope:** Per §8, UC2 (collaborative scorecards) and UC3 (calendar scheduling) are **out of this five-story MVP**; they are assumed as follow-on once UC1 data and applications exist.
- **Authentication and tenancy:** The data model assumes organizations and users; specifics of sign-up, roles, and multi-tenant isolation are not spelled out—assumed minimally sufficient for one org and recruiter/candidate personas.
- **Apply channel:** Job board distribution (§2.1) is not required for these stories; assumed apply via link or internal posting is enough for MVP.
- **Acknowledgment delivery:** PRD mentions email in the UC1 diagram; exact channel and provider are unspecified—assumed at least one verifiable outbound or in-app notification path.
- **“Bias detection”:** PRD describes flags and shortlist skew signals; exact definitions and compliance claims are not specified—stories only require stored, reviewable signals, not legal certification of fairness.

---

# Ticket for User Story #1

```markdown
# Create and publish a job with a configurable pipeline

## Source summary

- **Primary:** MVP user story #1 in `LTI-MGF/UserStories-MGF.md` — recruiter creates a job with ordered pipeline stages, persists data, publishes so applications are accepted, and views stages (including terminal vs non-terminal when supported).
- **Product context:** `LTI-MGF/LTI-MGF.md` §2.1 (configurable job pipelines), §5 (data model): `Job` and `PipelineStage` entities with relationships and attributes.
- **Related assumptions from user stories doc:** Auth and tenancy are minimally sufficient for one org and recruiter persona; job board distribution is not required for MVP.

## Improved user story

**As a** recruiter signed into the product,  
**I want to** define a job (role content + ordered hiring stages), save it, publish it when ready, and see the same pipeline on the job detail view,  
**So that** candidates can apply only to published roles and downstream features (applications, pipeline moves) have a consistent stage model per job.

## Functional details

1. **Job creation and edit (recruiter-only)**  
   - Create and update a job with at least: **title**, **description**, and **requirements** (the PRD models requirements as `requirements_json`; MVP may use structured JSON or a single rich-text field if the team defers structured parsing).  
   - Associate the job with the recruiter’s **organization** and record **created_by** (per PRD `Job` entity).

2. **Pipeline configuration**  
   - For each job, maintain an **ordered list** of **pipeline stages**.  
   - Order must be stable and explicit (align with `PipelineStage.sort_order` in the PRD).  
   - Stage records must include at least **name** and **sort_order**.  
   - If implementing the full PRD shape for stages, include **`is_terminal`** so the UI can distinguish terminal vs non-terminal stages on the job detail view; **`sla_hours`** can be MVP-optional unless product explicitly needs it on day one.

3. **Lifecycle: draft vs published**  
   - Jobs start in a non-published state (e.g. **draft** / **unpublished**) where they **must not** accept applications.  
   - **Publish** transitions the job to **published**, sets or exposes **`published_at`**, and makes the job eligible for applications (enforced consistently in apply flows when story 2 is implemented).  
   - **Unpublish** (if in scope) should be specified by product; if not in scope, document as out of scope for this ticket.

4. **Job detail view**  
   - Recruiter can open a job and see **the same ordered stages** as stored.  
   - If `is_terminal` exists in the data model, the UI should **surface** terminal vs non-terminal (label, badge, or section — exact UX is negotiable).

## Fields to create or update

Aligned with `LTI-MGF/LTI-MGF.md` §5.2 ERD (names are conceptual; map to actual DB/API naming in implementation).

| Entity | Field | Notes |
|--------|--------|--------|
| Job | `organization_id`, `created_by`, `title`, `description`, `requirements_json` (or equivalent) | Other Job fields (`department`, `location`, `employment_type`, `hiring_manager_id`, etc.) optional for MVP unless required by UI. |
| Job | `status` | Must distinguish unpublished vs published (exact enum values are a team decision). |
| Job | `published_at` | Set when publishing; null when never published. |
| PipelineStage | `job_id`, `name`, `sort_order` | Required for ordering. |
| PipelineStage | `is_terminal` | Include if matching PRD; drives AC3. |
| PipelineStage | `sla_hours` | Optional for MVP slice. |

## API and endpoint notes

**No application codebase exists in this repository** to anchor REST/GraphQL paths, request shapes, or versioning. The implementing team should define:

- Authenticated endpoints (or equivalent) for: create/update job, replace or patch ordered stages, publish job, get job detail (including stages).  
- Authorization: only users with a **recruiter** (or equivalent) role for the job’s organization can create, edit, publish, and view recruiter job detail.  
- Validation rules: e.g. minimum/maximum number of stages, non-empty stage names, unique `sort_order` per job, at least one non-terminal stage if business rules require it.

Document chosen URLs and payloads in the project’s API spec or OpenAPI once defined.

## Files likely to be modified

Not inferable from this repo (design documents only). Implementation will touch the team’s backend (persistence, authz), API layer, and recruiter UI for job create/edit/detail/publish.

## Acceptance criteria

1. **Persist job and ordered stages**  
   Given I am authenticated as a recruiter for my organization, when I create a job with title, description, and requirements and save an ordered list of pipeline stages (e.g. Applied → Phone Screen), then the job and all stages are persisted and any subsequent read returns stages in the same order (`sort_order` or equivalent).

2. **Publish**  
   Given a job exists in an unpublished/draft state, when I publish it, then its status indicates published, `published_at` (or equivalent) is set, and the system treats the job as eligible to receive applications (apply endpoint or UI must reject unpublished jobs when that path exists).

3. **Job detail and terminal flag**  
   Given a published job, when I open the recruiter job detail view, then I see the same pipeline stages in order, and if the persistence model includes a terminal-stage flag (`is_terminal`), the UI clearly distinguishes terminal vs non-terminal stages.

## Definition of done

- End-to-end flow works for the recruiter persona: create → optional edit → publish → view detail with correct stage order.  
- Data matches the intended relational model (job ↔ stages, tenant scoping).  
- Unpublished jobs cannot receive applications wherever apply is implemented (may be enforced in a follow-up ticket if apply is separate; if both ship together, same release must enforce it).  
- Automated tests cover persistence ordering, publish transition, and authorization for recruiter vs non-recruiter (or equivalent negative tests).  
- API or internal contracts documented enough for the next story (candidate apply) to depend on job published state and initial stage.

## Testing notes

- **Unit/integration:** CRUD for jobs and stages; ordering after updates; publish idempotency or double-publish behavior (define expected behavior).  
- **Contract:** Response includes ordered stages; published flag and `published_at` consistent.  
- **Manual:** Create job with 2–3 stages, reorder, refresh, publish, confirm detail view and (if applicable) terminal labels.  
- **Security:** Cross-organization access denied; candidate cannot publish or edit recruiter jobs.

## Documentation updates

- Brief description of job status values and publish semantics for implementers and QA.  
- If a seed or fixture job is added for local dev, document default stages.

## Non-functional requirements

- **Multi-tenancy:** Jobs and stages scoped to `organization_id`; no cross-tenant reads or writes.  
- **Performance:** Job detail with a typical number of stages (for example, up to about twenty) should load within normal product targets; no heavy N+1 queries on stage list.  
- **Auditability (optional MVP+):** Consider logging publish events and stage definition changes for support; not required unless PM asks.

## Missing information and assumptions

| Topic | Note |
|--------|------|
| Exact `status` enum | Assumed to include at least draft/unpublished and published; final values are a product/engineering choice. |
| Minimum/maximum stages | Not specified in source; assume reasonable defaults (e.g. ≥ 1 stage, cap to prevent abuse). |
| Unpublish / close job | `closed_at` exists on Job in PRD; closing vs unpublishing for MVP is not specified — clarify with PM if in scope. |
| Requirements shape | `requirements_json` suggests structured storage; plain text MVP is acceptable if labeled as a short-term assumption. |
| SLA on stages | `sla_hours` in PRD; omitted from MVP UI unless explicitly required. |

**MCP / external tools:** Source is local markdown only; no Jira, Figma, or Notion links were used.

```