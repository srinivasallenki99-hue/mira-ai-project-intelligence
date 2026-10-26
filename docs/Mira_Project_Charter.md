# Mira – Project Charter

**Version:** 1.2 – n8n source-validated capstone rebaseline (from Charter v1.0)  
**Status:** Proposed program baseline; ADR-014 and sponsor approval pending; not implemented or authorized for production launch  
**Prepared:** October 25, 2026  
**Initiative:** Mira – AI-Powered Project Intelligence Assistant  
**Delivery organization:** Nexora Pvt. Ltd.  
**Initial business context:** ABCDE Ltd. six-month AI Adoption Project  
**Charter owner:** AI PM / TPM (name to be assigned)  
**Executive sponsor:** Nexora CTO (proposed; confirmation required)  
**Baseline references:** Mira PRD v1.3 (original v1.1 FR-001–FR-018 and T1–T12 preserved); `Technical_Design_Document.md` v2.2; `Architecture.md` v2.2; historical Charter v1.0, TDD v2.0 and Architecture v2.0 retained in Git history  
**Scope-control note:** This revision changes the **proposed technical implementation baseline**, not the approved requirements. PRD v2.0 / CR-PROD-001 and production funding/approval remain pending. The **capstone proof of concept** and **proposed production program** have distinct authorization gates. See §15 (ADR-014).

---

## 1. Executive summary and authorization requested

Mira is a proposed, governed **AI-powered project intelligence assistant** for Nexora PMs/TPMs, initially grounded in the ABCDE Ltd. six-month AI Adoption Project. It addresses slow manual planning (3–4 hours), weekly status production (1–2 hours), inconsistent risk reviews, and late detection of blocked work.

**Implementation rebaseline from Charter v1.0:** Mira retains four **logical specialist AI capabilities**—**Planner, Risk Assessor, Status Reporter, and Milestone Tracker**—but implements them as reusable **n8n sub-workflows**, coordinated by one n8n business/AI orchestrator. Approved project files and, in a separately authorized production phase, read-only Jira/Trello data provide authoritative facts. n8n performs deterministic record validation, filtering, counting and rule evaluation **before** optional LLM calls. Stakeholder reports remain drafts until an authorized PM/TPM reviews and approves them; internal evidence-backed warnings need not wait for report approval.

**Why the change:** The earlier Langflow-first design and evaluated n8n–Langflow hybrid introduced either additional business-automation dependencies or a second service boundary. n8n-only is proposed as the **lower-complexity capstone implementation** while preserving all baseline business acceptance cases. This is an architectural choice pending review and experimental confirmation, **not evidence that n8n or the project is production ready**.

**Authorization requested now:** Approve a limited capstone/discovery phase using approved synthetic or assignment data, n8n environment access, controlled model expenditure, and G1–G7 test activities. **Not requested/authorized:** production release, real-client sensitive data, enterprise-wide access, unattended external stakeholder publication, or the proposed 10-user production pilot. These require PRD v2.0 / CR-PROD-001, budget, security, legal/privacy and operational sign-offs.

## 2. Business problem and opportunity

| Business problem in the capstone | Current baseline | Intended improvement |
|---|---|---|
| Manual project-plan creation | 3–4 hours per project | Grounded plan drafts with less preparation and review effort |
| Inconsistent risk assessments | No consistent matrix | Standardized risk register views and mitigations |
| Weekly status reporting overhead | 1–2 hours per report | Accurate filtered counts and repeatable reports |
| Late discovery of blocked or slipping milestones | Primarily reactive | On-demand alerts in capstone; scheduled detect–notify–acknowledge–escalate in proposed production phase |
| Inconsistent project documents | Formats vary | Governed plan, risk, report, and update templates |
| Multiple projects with limited PM capacity | Few PMs manage concurrent work | Reusable workflows; project-scoped selection in production portal |

**Value hypothesis:** The system will allow PMs/TPMs to spend less time compiling project artifacts and more time managing risks, decisions, dependencies, and stakeholders. Benefits are hypotheses until measured against manual baselines during evaluation and pilot.

## 3. Vision and measurable objectives

**Vision:** Deliver a trustworthy, auditable project intelligence capability that provides project-specific recommendations without inventing tasks, statuses, risks, milestones, or approvals.

| Objective | Proposed measure / acceptance target | Measurement stage |
|---|---|---|
| Accurate baseline capabilities | Execute T1–T12; target 12/12 pass after remediation | Capstone |
| Correct task calculations | 100% agreement with valid source rows in baseline tests | Capstone |
| Evidence-backed outputs | Zero unsupported factual claims in accepted baseline reports | Capstone |
| Better plan productivity | Target ≤30 minutes total PM time per plan, including review, versus 3–4 hours | Pilot; to be validated |
| Better report productivity | Target ≤15 minutes total PM time per report, including review, versus 1–2 hours | Pilot; to be validated |
| Earlier detection | 100% detection of seeded blocked tasks in controlled tests; measure time-to-detect for source updates | Capstone / production pilot |
| Reliable monitoring | Proposed ≥99% scheduled-monitor success; notifications within 15 minutes **after successful detection**, subject to source freshness and approved policy | Production pilot; requires approval |
| Governed communications | 100% of externally distributed reports require a recorded human approval | Production pilot |

Targets are provisional and must be approved by sponsors before they become service commitments. No system can guarantee prevention of every milestone delay.

## 4. Scope and phased deliverables

### Phase A – Capstone / controlled proof of concept (PRD v1.3; original requirement set preserved)

**In scope:** Single ABCDE project baseline (`ABCDE-AI-001` as proposed internal identifier); source files `project_description.txt`, `project_timeline.csv`, `project_risks.csv`, `sample_task_board.csv`, and `eval_mira_inputs.txt`; approved TXT/CSV file ingestion; n8n Form Trigger or controlled test inputs; extraction, schema checks, field normalization and deterministic task/date logic; n8n **Router/Dispatcher** (`Switch`/`IF`) and four reusable specialist sub-workflows (`Execute Sub-workflow`); source-grounded plans and risks; sprint-specific status reporting; on-demand blocker and upcoming-milestone checks; a **draft-only** Sprint 2 stakeholder email; human PM/TPM review; all T1–T12 evaluations; n8n execution/evaluation and model-token/cost records as technically available; redacted evidence and reviewed workflow JSON committed to GitHub.

**Blocking technical spike, MIRA-001:** Ingest the real 25-row task-board fixture; reconcile **5 Done, 3 In Progress, 1 Blocked, 16 To Do**; identify **T024 Security review of AI infrastructure** as source status `Blocked`; prove Sprint 3 filtering and missing/duplicate/unknown-status rejection; never use an LLM to calculate counts. Actual CSV headers have been verified; n8n node mappings still require execution evidence.

**Out of scope:** Production-grade SSO/project membership, real-client data without authorization, authenticated multi-project portal, live Jira/Trello sync, autonomous Jira/Trello updates, production SLOs, durable automated approval enforcement, unsupervised external publication, and unattended escalations.

**Required artifacts:** Updated PRD implementation addendum if platform references conflict, TDD v2.2, Architecture v2.2, this Charter v1.2, GitHub README, actual exported **n8n JSON** workflows under `n8n/`, prompt and schema documentation, T1–T12 execution ledger and screenshots, LLM unit-cost/latency and failure-path observations, reflection/Q1–Q4 materials, and reproducible import instructions. **Do not label architecture diagrams or placeholder exports as executed evidence.**

### Phase B – Production engineering / limited pilot (change-controlled, not authorized)

**Proposed in scope:** Authenticated project portal and authorized Project Selector; trusted business-project RBAC; approved project registry; encrypted, versioned data store and snapshots; secure scanning/validation of uploads; optional authorized **read-only** Jira/Trello integration; persistent reports, human approvals and decisions; n8n Schedule Trigger/source-event monitoring and deterministic rules; durable deduplicated alert register, notification outbox, acknowledgement, owner action and escalation; controlled secrets, model/provider governance, monitoring and incident response; backup/restore and staged deployment; **P01–P15 production verification**; an initial **10-PM/TPM controlled pilot** subject to approval.

**Out of scope without subsequent authorization:** Automatic task or committed-baseline changes, unsupervised stakeholder email sending, AI-led remediation, unsupported predictive claims, customer-wide onboarding and enterprise-scale rollout.

**Scope protection:** The production extension (FR-019–FR-026 / CR-001, also tracked under CR-PROD-001 for PRD v2.0) does not become capstone scope merely because it appears in a target-state diagram. Phase B requires a fresh go/no-go decision.

## 5. Solution approach and architecture

**Reference implementation:** `docs/Technical_Design_Document.md` v2.2 and `docs/Architecture.md` v2.2 (seven GitHub-renderable Mermaid diagrams). ADR-014 records the recommendation and review conditions.

| Architecture capability | Phase A implementation / owner | Phase B extension (subject to authorization) |
|---|---|---|
| User experience | n8n test form/test webhook and developer execution; assignment data only | Enterprise-authenticated portal, project selector and controlled uploads |
| Source ingestion | `Form Trigger` / `Extract From File` plus no-code validation | Secure source gateways and approved read-only Jira/Trello ingestion |
| Project scoping | Fixed, checked `project_id`; avoid implicit ABCDE context on data-free tests | Verified user membership and tenant/project authorization **before** lookup |
| Orchestration | n8n `Switch`/`IF` router + `Execute Sub-workflow` | Governed n8n runtime with approved throughput/concurrency |
| Specialist AI | Planner, Risk Assessor, Status Reporter, Milestone Tracker as **four independently testable n8n sub-workflows** | Reusable, versioned AI contracts with supported model and prompt governance |
| Facts and rules | Node/data-service calculated counts, filters, IDs and dates; AI cannot override | Persisted deterministic rules, freshness policy, alerts and audit |
| Model | Approved OpenAI model selected and pinned for reproducible test runs; prior GPT-4o-mini is a historical candidate, not an indefinite mandate | Privacy-vetted provider/model with quality, security, pricing and lifecycle review |
| Draft review | Manual PM/TPM confirmation; no automatic distribution | Durable reviewer identity, approval version, immutable decision record and publication gates |
| Monitoring | n8n execution history, captured model usage and evaluation ledger; redacted GitHub evidence | Dedicated security/operational telemetry, independent audits, incident and recovery monitoring |
| Source control | Actual reviewed n8n workflow JSON, prompts, schemas and tests in GitHub | Approved version promotion, release manifests, backup and rollback |

**Workflow boundaries:**

1. **User request → approved input → validation and source scoping.** Missing source data must not trigger a fabricated answer.
2. **n8n router → one relevant specialist (or bounded multi-step plan).** Prefer deterministic routing for explicit intents; avoid redundant LLM router calls.
3. **Deterministic facts/rules → optional AI narrative → schema/factual validation → draft artifact → PM/TPM review.** Preserve source record IDs and snapshot references.
4. **Phase B monitoring:** verified schedule/source event → fresh authorized snapshot → rules → idempotent persisted alert → PM/TPM notification → acknowledgement/action/escalation → recheck/resolution. Do not hold immediate **internal** safety alerts behind stakeholder-report approval.

**Trust boundary:** n8n workspace/editor access is **not** end-user project-level authorization. An authenticated portal, trusted access service, per-project data policies and credential isolation are required for production. The n8n Form Trigger is only a capstone convenience, not a complete enterprise portal. Use sanitized fixtures and do not expose sensitive client data on public test forms.

**No-code-first qualification:** `Summarize`, `Filter`, `IF`, `Date & Time`, expressions and node mappings are implementation candidates, not proof that all uniqueness, branch-join and calendar rules work without code. Gate G3 validates them on actual fixtures. If inadequate, request explicit authorization for a small deterministic service rather than approximating with AI.

## 6. Governance, stakeholders and decision rights

| Role | Accountability / decision authority | Assignment |
|---|---|---|
| Executive sponsor / Steering Committee | Business case, budget, scope expansion, production go/no-go | Proposed: Nexora CTO; confirm |
| Product Owner / AI PM/TPM | Product outcomes, PRD, backlog priorities, business acceptance | TBD |
| Technical Architect / AI technical lead | Architecture, integration, data contracts, technical risk acceptance recommendation | TBD |
| Security/privacy lead | Data handling, access, third-party AI processing and production security approval | TBD |
| Platform/SRE owner | Environments, deployments, SLOs, incidents, recovery | TBD |
| QA/Evaluation owner | Test strategy, T1–T12 evidence, production verification | TBD |
| Project/data owner | Source accuracy, update cadence, source authorization | TBD |
| PM/TPM pilot participants | UAT, output review, feedback, acknowledgement of alerts | Proposed 10 users |

**Decision framework:** Product Owner may prioritize within an approved phase; Technical Architect approves design within organizational delegated authority; sponsor approves material cost, production scope, and business exceptions. Security/privacy owners must approve real-client data use and production access controls. No person may self-approve a report where separation of duties is required by policy.

**Meeting cadence (proposed):** Weekly delivery review; fortnightly architecture and risk review; phase-gate steering meeting. ADR-014 and n8n G1–G7 readiness require named reviewers. Maintain a decision log, RAID log, issue backlog, and change-request register.

## 7. Delivery plan, milestones and decision gates

**Single traceable gate register:** The charter intentionally adopts the TDD v2.2 identifiers **G1–G10** rather than reusing Charter v1.0's different G1–G5 labels. **C0** is the charter authorization checkpoint. Phase B requires independent production verification **P01–P15**.

| Gate | Deliverable and objective evidence | Accountable reviewer | Stage |
|---|---|---|---|
| **C0 – Charter / ADR** | Confirm scope boundaries, owners, funding assumptions, ADR-014 selection and trial-data policy | Sponsor / CTO delegate | Before Phase A authority |
| **G1 – Platform** | n8n workspace/approved host, scoped OpenAI credential, security settings and export/import smoke test | Platform / Architect | Phase A |
| **G2 – Source readiness** | Five canonical files inspected offline; approve exact CSV schemas, 25-row source fixture, historical date policy and T7/T11 ambiguities; approval pending | Data owner / QA | Phase A |
| **G3 – MIRA-001** | n8n execution proves 25 valid fixture rows and **5/3/1/16**, exact T024, Sprint 2/3 fixture filters, malformed/duplicate/unknown-status rejection and deterministic counts | Architect / QA | Phase A **blocker** |
| **G4 – Specialists** | Four separately executable n8n specialist sub-workflows with input/output and insufficient-data contracts | Technical lead / QA | Phase A |
| **G5 – Router and review** | Documented `Switch`/`IF` dispatch, no-data handling, PM/TPM draft review, no unauthorized send | PM/TPM / Architect | Phase A |
| **G6 – Evaluation** | Connect and demonstrate evaluation/observability tool, run **T1–T12**, record pass/fail and defects, actual traces/screenshots, token use, model cost and latency | QA/Evaluation lead | Phase A |
| **G7 – Capstone release** | Reviewed n8n JSON, README, diagrams, charter, tests, screenshots, reproduction and rollback smoke test | PM/TPM + Architect | Phase A exit |
| **G8 – Production scope** | Sponsor approves PRD v2.0 / FR-019–FR-026, business thresholds, policy, funding and risk ownership | Sponsor / Product Owner / Security | **Phase B entry** |
| **G9 – Alert POC** | Scheduled check, freshness test, durable alert deduplication, verified delivery and acknowledgement | Delivery operations / SRE | Phase B |
| **G10 – Production controls** | Security, isolation, approval integrity, backup/recovery, quotas, P01–P15 and approved operating targets | Security / SRE / CTO delegate | Pre-pilot |
| **Pilot / scale decision** | Ten authorized PMs/TPMs, measurable benefits, support readiness, incident results, written go/no-go | Sponsor / Product Owner | After G10 |

**Indicative sequence:** Week 1 – C0/G1/G2/G3; Week 2 – G4/G5; Week 3 – G6/G7. This is a planning hypothesis for a capstone, **not** an agreed schedule or capacity guarantee. Re-estimate after G3 feasibility and confirmed contributors. Production timeline/budget are independently planned after G8.

**Delivery controls:** GitHub issues linked to FR IDs/tests; weekly RAID/decision review; short demo at each gate; scope change log; no fabricated completion checkmarks; rollback of broken workflow imports using reviewed exports. GitHub commits do not deploy n8n automatically; plan-dependent features require license verification.

## 8. Budget, resources, economics and dependencies

**Budget status:** No approved cost baseline or staffed delivery capacity exists in the supplied charter. The sponsor must approve project funding before production. Track capstone free/trial limits and permitted model usage; do not imply a commercial plan is included at no charge.

**Capstone cost ledger** (record actuals): n8n Cloud subscription/trial or approved self-hosted operations, OpenAI **input/output tokens by model**, workflow executions, test repetitions, retries and optional evaluation tooling. Token-price formula: `LLM cost = (input tokens × approved input rate + output tokens × approved output rate) / 1,000,000` when rates are quoted per million tokens. Include model version, date/rate source and call count. **No platform has an intrinsic token-price advantage when token counts/model match.**

**Production cost categories:** n8n hosting/license/edition, secure portal/auth services, storage and backups, connector/API access, notification provider, model usage, external logging, cybersecurity/compliance, staffing/on-call, load/evaluation tests, recovery infrastructure, contingency and vendor support. Compare **total cost per accepted report** and **per monitored project**, not only LLM cost.

**Key dependencies:** Original five source/evaluation files; confirmed data owner and approved synthetic-data classification; n8n environment and licensed feature availability; approved API-key secret store/provider access; GitHub repository and reviewer; PM/TPM acceptance availability; source schemas and task status taxonomy; optional Jira/Trello permission scope; production portal/IdP; data store and alert outbox; notification channel and on-call ownership. No n8n→Langflow HTTP dependency, Make.com subscription or LangWatch runtime is required by the selected baseline.

**Workforce assumptions:** Named accountable owners are pending. In the capstone, one person may perform several build/test roles, but approval and security separation-of-duties requirements must not be waived in a production environment.

## 9. Risk, assumptions, issues and dependencies (RAID)

| Risk ID / concern | Business or technical impact | Mitigation, gate and owner |
|---|---|---|
| R01 – n8n no-code node semantics unproven | Wrong counts, IDs, dates or incomplete joins | **G3 MIRA-001**: reconcile actual CSV, uniqueness, unknown statuses, date tests; Architect/QA |
| R02 – Workflow branch loses project context | Mis-scoped report or mixed-project data | Explicit `project_id`, trusted authorization in Phase B, negative tests P02; Security/Data owner |
| R03 – LLM invents task facts or planned dates | Misleading status/risk decisions | Deterministic facts, data-source IDs, schema assertions, missing-data tests and reviewer; QA |
| R04 – Upload/API data stale, malformed or malicious | Missed alerts, prompt injection, privacy incident | Validation, source timestamps, quarantine, read-only access and P03/P04/P09; Data/Security |
| R05 – n8n forms/workspace are mistaken for an enterprise portal | Client data exposure | Synthetic capstone data only; SSO + trusted project RBAC in Phase B, P01/P02; Security |
| R06 – Single n8n runtime fails or hits plan limits | Business/AI automation outage and lost checks | Hosting/edition due diligence, retry/outbox/backups, scale and recovery tests; SRE |
| R07 – Alert retries create duplicate notifications | Alert fatigue and repeated escalation | Durable alert lifecycle, idempotency keys, outbox/dead-letter, P05–P07; Ops/SRE |
| R08 – Human approval bypass or report tampering | Unauthorized stakeholder communication | Draft-only Phase A, durable role-aware approval before publication in Phase B, P08; Product/Security |
| R09 – Model/provider version or pricing changes | Quality regressions or unpredictable usage costs | Model pinning, regression runs, budget caps and provider review; Architect/Finance |
| R10 – n8n evaluation/telemetry gaps | Weak evidence for instructor or release decision | G6 evidence ledger, compare evaluation functionality in subscribed edition; QA |
| R11 – n8n JSON export leaks credentials/payloads | Exposure through GitHub | Credential/redaction review, least privilege, secret scanning, P10; Security |
| R12 – Feature and scope inflation | Delivery delay and unclear approvals | Strict G8 production authorization, RAID/change log, PRD v2.0; Sponsor/PM |

**Assumptions:** Phase A handles approved instructional/synthetic datasets; real client data, production user identity and integrations are **not** available by implication. Missing dependencies or field mappings are marked **unknown**, not inferred. Workflow features, pricing, security capabilities and execution limits must be validated in the actual n8n edition and hosting plan.

**Current blocking issues:** (a) confirm the five authentic assignment files and headers; (b) verify MIRA-001 counting/uniqueness/sprint tests with an actual execution; (c) obtain ADR-014/C0 sign-off; (d) confirm pilot owners and data-access approvals if Phase B is initiated.

## 10. Quality, acceptance and release gates

### Phase A – Capstone evaluation and exit criteria

**Assignment submission integrity:** Q1 (2–3 ideation use cases, three metrics each and knowledge base), Q2 (build and demonstrate required core + milestone extension, a **connected** evaluation/observability tool, T1–T12 test results, cost per plan, three or more metrics and 1–2-page report), Q3 (Program Charter) and Q4 (one-page trace/reflection/privacy/ten-PM/TPM rollout proposal) remain capstone deliverables. A screenshot of n8n execution logs alone does not constitute verified observability-tool connection. The repository must include README, n8n JSON exports, diagrams and canvas/output/trace screenshots. These are delivery commitments for the capstone; **production controls and live integrations remain separately proposed**.


Run the unmodified **T1–T12** baseline with each test's input, source fixture/version, expected behavior, actual output, pass/fail, exact error, responsible owner, defect/retest status, workflow export version, model and prompt version, per-test latency and model usage/cost when available. The target is **12/12 demonstrated passes after remediation**; a drafted diagram or prompt does not count as an execution pass.

| Test cases | Required evidence |
|---|---|
| **T1/T2/T9 – Planning** | Source-grounded full ABCDE plan; clarification instead of invented scopes/timelines for missing-input cases |
| **T3/T4/T7 – Risks** | Project-specific risk matrix and three actual registered risks with disclosed selection method (risk register is unranked); no invented risks for empty input |
| **T5/T6/T10/T12 – Status & drafts** | Sprint 3 filtered actual board; honest no-data response; exact 25-row count **5 Done / 3 In Progress / 1 Blocked / 16 To Do**; Sprint 2-only stakeholder email **draft** |
| **T8/T11 – Milestones** | T024 Security review of AI infrastructure correctly BLOCKED; relative milestones strictly from timeline; calendar comparisons only after approved project start-date/project-week anchor, otherwise clarification |

**Mandatory safeguard:** T2/T4/T6/T9 must not silently inherit ABCDE data. Sprint 2 email must exclude Sprint 3 and later work. On-demand milestone windows need an explicit reference date; data freshness/actual due dates determine claims.

**Quality dimensions:** correctness, source traceability, incomplete-input handling, safe refusal/clarification, schema adherence, task-ID reconciliation, failed-branch behavior, agent independence, response time, measured model calls/token spend, and reproducibility. Proposed targets in §3 are subject to approval and measurement.

### Phase B – Production verification, not implied by capstone completion

All **P01–P15** in TDD v2.2 §27 require evidence in production-like staging, including: identity and cross-project denial; malicious file and prompt attacks; stale-source fail-safe; scheduled checks; duplicate-alert and notifier-outage behavior; immutable approval; credential/log redaction; restore/rollback; rate limits; incident paging; and **T1–T12 regression**. SRE/Security/CTO delegate must sign release evidence.

**Proposed service objectives (not achieved SLAs):** 99.9% monthly request availability, routine report-generation p95 ≤30 seconds under defined load excluding reviewer waiting, ≥99% successful monitored checks, recovery RPO ≤1 hour / RTO ≤4 hours where the chosen n8n plan and architecture can support them. Alert notification target: within 15 minutes **of successful detection**, not necessarily within 15 minutes of real-world condition onset; source refresh cadence must be specified.

## 11. Proactive milestone alert lifecycle and human controls

Alert states: `DETECTED → NOTIFIED → ACKNOWLEDGED → ACTION_ASSIGNED → MONITORING → RESOLVED` with `ESCALATED` when the approved escalation policy is triggered. Store event timestamps, source version, assigned owner, severity, acknowledgement, action and history. A deduplication key must prevent duplicate unchanged alerts; state changes may generate new notifications.

**Separation of approval responsibilities:** A system may issue an internal evidence-backed risk notification without prior report approval so warnings are not delayed. PM/TPM approval is mandatory before publishing a formal stakeholder report, changing committed dates, or initiating any high-impact corrective action. Reject/revise flows return to draft and preserve audit history.

## 12. Communications and stakeholder engagement

| Audience | Communication | Proposed cadence |
|---|---|---|
| Executive sponsor | Outcomes, budget, milestones, major risks, go/no-go decisions | At phase gates; otherwise monthly |
| Delivery team | Backlog, defects, blockers, decision log | Weekly |
| Architecture / security reviewers | ADRs, interface changes, controls and test findings | At design reviews and changes |
| Pilot PMs/TPMs | Training, feedback collection, usage guidance | At onboarding and during pilot |
| Affected project owners | Evidence-backed risk notifications and escalations | On qualifying changes and follow-up checks |

Formal client-facing communications require an authorized reviewer and approved distribution channel.

## 13. Rollout, adoption and benefits realization

1. **Controlled POC:** Demonstrate approved data, n8n specialist sub-workflows, T1–T12 and repeatable GitHub artifacts.
2. **Production readiness:** Implement and verify identity, isolation, ingestion, monitoring, approval, reliability and recovery.
3. **Limited pilot:** Enroll 10 PMs/TPMs with authorized projects and clear support channels.
4. **Measure benefits:** Compare actual plan/report preparation times, correction effort, detection latency, alert precision, false positives, user adoption and cost.
5. **Scale decision:** Expand only after sponsor confirms business value and platform owners accept operations/security responsibilities.

No automatic enterprise rollout is authorized by this charter.

## 14. Change control, baseline management and decisions outstanding

| Change / decision | Current status | Approval required |
|---|---|---|
| **ADR-014 – n8n-only implementation choice** | **Proposed as v2.2 source-validated design baseline**; replaces Langflow-first and hybrid in reference designs | Architecture authority / sponsor (as delegated); G3/G6 evidence |
| **Charter v1.2 source-data rebaseline** | This document proposed; retains business goals, two-phase program, governance and baseline tests | Sponsor / Product Owner |
| **PRD v1.3 source-aligned baseline** | FR scope unchanged; any Langflow-specific prescriptions require a reviewed platform addendum | Product Owner / Architect |
| **CR-001 / CR-PROD-001** | FR-019–FR-026 proactive monitoring and lifecycle proposed | PRD v2.0 / sponsor funding and policy authorization |
| **CR-002 production portal / controls** | Authenticated selector, identity, project isolation and persistent approval proposed | Architecture/Security, sponsor funding |
| **n8n licensing/hosting/model selection** | Trial/platform choices and approved model version pending | Platform/Security/Finance |
| **MIRA-001 and acceptance rules** | Actual CSV fields and offline counts verified; n8n workflow results still unexecuted | Data owner / QA / Architect |
| **Monitoring policies** | Data freshness, thresholds, source schedule, notification channel, owner, acknowledgement/escalation pending | Product Owner / Delivery Ops / Security |
| **Pilot resources and SLAs** | Ten-user pilot proposed, not scheduled or funded | Sponsor / SRE / Finance |

**Change-control protocol:** Capture business request and impact (scope, cost, security, data, schedule and testing); review with architect/QA; obtain required approvals; update charter/PRD/TDD/Architecture and impacted test mapping; commit via reviewed GitHub PR; baseline release manifest; retest before promotion. Keep v1.0 Charter and v2.0 technical documents available historically; do not overwrite evidence with unapproved claims.

### CR-DATA-001 — Canonical source-data reconciliation and authorization impact

**Problem discovery (2026-10-25):** The original capstone assignment prose implies `3 Done / 3 In Progress / 1 Blocked / 18 To Do` for a 25-task board, and that mix was repeated as a provisional test count; it is **not supported** by the uploaded CSV. The inspected source gives **25 unique tasks = 5 Done + 3 In Progress + 1 Blocked + 16 To Do**. This corrects an acceptance fixture; it does **not** modify Nexora's original business problem, ABCDE's AI-adoption scope, or the capstone's T1–T12 prompts.

**Decisions requested:** (1) PM/TPM and QA approve source-derived T5/T10/T12 expected records; (2) architecture/QA agree that T7 has no input-defined risk rank, so output must disclose an illustrative selection or await an approved policy; (3) accept that T11 cannot compute upcoming *calendar* milestones from Weeks 1–24 without a calendar anchor; (4) approve treating `sample_task_board.csv` as a historical fixture with explicit `as_of_date` for deadline interpretation. These are **open until signed** and must be reflected in QA evidence.

**Authoritative sources:** `project_description.txt`, `project_timeline.csv`, `project_risks.csv`, `sample_task_board.csv`, `eval_mira_inputs.txt` in the supplied archive. Supporting duplicate TXT/PDF/DOCX are references, not independent alternate datasets. GitHub branch: `docs/mira-source-rebaseline-2026-10-25`; existing signed/previous versions retained in history.

**No scope/budget expansion:** Four specialists, Router/Dispatcher, source grounding, prompt engineering, evaluation/observability and mandatory on-demand milestone extension remain Phase A. Scheduled proactive monitoring, production portal/SSO, live Jira/Trello, encrypted multi-project store and ten-user pilot remain a future Phase B change request, not capstone obligations. No production approval, executed test pass, budget authorization or realized outcome is implied by this charter amendment.

## 15. Architectural baseline rationale – ADR-014: n8n-only vs. Langflow-only vs. hybrid

**Decision context:** As the charter evolved from a capstone agent demo into a production-target project-governance system, the design acquired source ingestion, exact counts, scheduled monitoring, authorized integrations, report approval, alert state and escalation responsibilities. The earlier Langflow-first design addressed AI workflows but required additional business automation components; hybrid added two runtimes and a new service boundary.

| Evaluated option | Strength for Mira | Trade-off | Charter disposition |
|---|---|---|---|
| **Langflow-only** | Rich visual AI graph and specialist agent composition | Requires additional implementation design for enterprise business integrations, schedules and durable approval/alert state | Not chosen for baseline |
| **n8n + Langflow hybrid** | Dedicated AI platform separated from enterprise workflow automation | Authenticated n8n→Langflow API, cross-platform retries, two hosting/logging surfaces, added operational cost | Retained as contingency if evidence warrants |
| **n8n-only** | One orchestrator supports source integration, exact processing, AI workflows, scheduled checks and human-review handoffs | Must prove no-code invariants, specialist quality, license/hosting features and recoverability | **Selected proposed implementation baseline**, approval pending |

**Design and TPM rationale:** Select the least complex architecture that meets business requirements and is defensible through evidence. Eliminate the unnecessary n8n→Langflow HTTP boundary and Make.com scheduling assumption; route explicit requests via `Switch`/`IF`, call only necessary AI specialists, perform status arithmetic deterministically, and retain report approval/audit separation. Fewer platforms may lower hosting and integration risk; **no automatic token-cost advantage** is claimed. Compare measured LLM calls, token usage, retries, report acceptance and cost per approved artifact.

**Hard conditions before final ratification:** G3 exact source processing passes; G4 proves four reusable specialists; G5 shows controlled routing and draft review; G6 achieves and records T1–T12 results; G7 demonstrates safe export/re-import/rollback; hosting edition and vendor model are approved. A failed condition triggers a documented architecture review, which may benchmark a hybrid design against the same source fixtures. No project-specific adoption or deployment claim is permitted until evidence exists.

**Baseline impact summary:**

| Element | Charter v1.0 / TDD v2.0 | This proposed baseline |
|---|---|---|
| Agent platform | Langflow Router/Run Flow specialists | n8n `Switch`/`Execute Sub-workflow`; four specialist n8n workflows |
| Scheduling | Make.com or extra scheduler in production design | n8n Schedule Trigger/source events under CR-001 |
| Workflow artifact | `flows/` Langflow JSON | `n8n/` n8n JSON; no manual Git upload implies deployment |
| Operational traces | LangWatch principal AI trace assumption | n8n execution/evaluation evidence by default; optional vetted external telemetry |
| Model | GPT-4o-mini historically proposed | Supported, approved version-pinned OpenAI model to be selected and benchmarked |
| Requirements and gates | Original PRD v1.1 T1–T12, proposed production objectives | **All preserved**; P01–P15 and FR-019–FR-026 remain separately gated |

**Decision state:** Proposed design baseline only; ADR-014 and project charter still need named approval. Business scope, expected benefits and acceptance obligations remain unchanged.

## 16. Charter approval and sign-off

| Approval role | Named approver | Decision / evidence required | Status / date |
|---|---|---|---|
| Executive sponsor / CTO delegate | TBD | Program capstone authority, phase boundaries, and budget assumptions | Pending / — |
| Product Owner / AI PM/TPM | TBD | PRD outcomes, business acceptance and deliverable baseline | Pending / — |
| Enterprise Architect / AI lead | TBD | ADR-014 and n8n technical feasibility criteria | Pending / — |
| Data Owner / QA Evaluation Lead | TBD | Source truth, T1–T12, MIRA-001 assertions | Pending / — |
| Security / Privacy Lead | TBD | Data policy and independent Phase B release sign-off | Pending / — |
| Platform / SRE Owner | TBD | Hosting, backups, on-call and Phase B operations | Pending / — |

**Proposed authorization now:** Continue controlled Phase A discovery and capstone implementation with approved assignment/synthetic data. **No production release, real-client data ingestion, autonomous external stakeholder publication, 10-user pilot or funding commitment** is granted by a proposed charter or ADR alone.

**Phase B authorization:** Requires separate written sponsor approval of PRD v2.0 / CR-PROD-001, approved budget, legal/security/privacy/operations plans, named owners, verified P01–P15 and final go/no-go. Approval of Phase A is not approval of production.

## 17. Document control, preserved baselines and revision history

**v1.2 (2026-10-25), proposed:** Corrects source-based acceptance to `5/3/1/16`, adds exact Sprint 2/3 and T024 fixture details, discloses missing risk ranks and calendar start date, and records CR-DATA-001 decisions. **Retains unchanged** original business problem, product goals, no-code-first n8n implementation proposal, four specialist agents, T1–T12 and the separate Phase B approval gate.



**Current document:** `docs/Project_Charter.md` **v1.2**. It aligns to:

- `docs/PRD.md` – proposed **PRD v1.3**, preserving v1.1 business problems, FR-001–FR-018 and T1–T12; source-validated data acceptance. **PRD v2.0** still requires authorization for proposed production additions.
- `docs/Technical_Design_Document.md` – **v2.2**, n8n-only architecture, ADR-014, implementation gates G1–G10 and production acceptance P01–P15.
- `docs/Architecture.md` – **v2.2**, revised Mermaid architecture, source processing, monitoring, approval and evaluation diagrams.
- `n8n/` – actual credential-reviewed n8n workflow exports **once built**, not to be represented as already created.
- `testing/` – task-board validation evidence, T1–T12 evaluation ledger, release gate record and P01–P15 proof **when available**.
- `docs/adr/` or a clearly linked section in the TDD – **ADR-014** architecture trade-off and future reassessment conditions.

| Version | Date | Change | Status |
|---|---|---|---|
| **v1.0** | 2026-10-25 | Initial two-phase charter, PRD v1.1 business goals, Langflow-first capability mapping, human approval and production gates | Historical baseline |
| **v1.1** | 2026-10-25 | Rebased implementation to n8n-only; added ADR-014 comparison, n8n deliverables, aligned G1–G10, normalized evaluation requirements, cost/API considerations and revised risks; **did not remove T1–T12 or production approval gates** | Historical proposed revision |
| **v1.2** | 2026-10-25 | Source data rebaseline (T10 = 5/3/1/16, T024, Sprint 2/3), T7/T11 evidence gaps, explicit connected-observability and Q1–Q4 submission controls | Proposed, approval pending |

**Integrity rule:** Keep prior signed revisions and GitHub history. New version baselines are proposals until approved; document status cannot be inferred from a download or commit.
