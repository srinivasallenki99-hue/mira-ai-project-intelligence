# MIRA — AI-Powered Project Intelligence Assistant

## Project Charter v1.3 — As-Built Capstone Baseline / Proposed Production Program

| Document control | Detail |
|---|---|
| **Prepared / updated** | November 19, 2026 |
| **Audience** | Executive sponsor, CTO, TPM / PM evaluators, engineering, data, security and QA leads |
| **Business context** | Nexora Pvt. Ltd. — ABCDE Ltd. AI Adoption Project (instructional/synthetic capstone scenario) |
| **Charter owner** | AI PM / TPM; individual and formal delegate to be confirmed |
| **Executive sponsor** | Nexora CTO (proposed in scenario; formal sign-off not evidenced) |
| **Scope** | Phase A: implemented and evaluated capstone prototype. Phase B: separately proposed enterprise pilot/production target |
| **Technology decision** | ADR-014: n8n as the orchestration platform; OpenAI, n8n Data Tables, Langfuse, Google Workspace and GitHub as supporting services |
| **Requirements baseline** | PRD v1.3; original FR-001–FR-018 and T1–T12 preserved; FR-019–FR-026 / CR-PROD-001 are production extensions |
| **Companion documents** | [Architecture.md](Architecture.md) v2.3 (seven Mermaid diagrams); [Technical_Design_Document.md](Technical_Design_Document.md) v2.3; capstone evaluation workbook |
| **Previous baseline** | Charter v1.2, October 25, 2026; retain as history |
| **Authorization state** | Capstone implementation/evaluation evidenced; **no formal enterprise funding, sponsor approval, or production release is asserted** |

> **Read this first.** This charter separates **delivered capstone capabilities**, **recorded test evidence**, **planned outcome measures**, and **production-only proposals**. Workflow exports and approved sample reports do not independently establish authorization to process real customer data or publish unattended stakeholder communications.

### Executive decision brief

**Problem:** PMs/TPMs spend roughly **3–4 hours** developing project plans and **1–2 hours** assembling weekly status reports in the case study. Inconsistent risks, limited PM capacity and reactive milestone visibility compound delivery risk. These are **case-study baselines**, not labor savings measured by MIRA.

**Delivered for the capstone:** An n8n-led project intelligence prototype with **15 exported workflows**, conversational intake, project selection and versioned snapshots, **five specialist agents**, source-backed plans/risks/status/milestones/stakeholder drafts, validation and evaluation workflows, Langfuse tracing and token collection, and a human review/document-delivery path.

**Quality evidence:** Regression **#5814**, executed **November 18, 2026**, recorded **12/12 overall passes** across prescribed T1–T12 scenarios. Vague requests T2/T4/T6/T9 stop before specialist execution; this does **not** imply zero model usage by the conversational routing layer. Grounding is limited to the harness's checks; external source-fact verification and production performance are not certified.

**Financial evidence:** The batch recorded **97,724 generation tokens** and approximately **$0.03974 in summed estimated model costs**. The detailed T1 plan recorded **13,479 tokens** and about **$0.00731 estimated model cost**. These figures exclude noncaptured model calls and platform/hosting/provider/operating charges; a zero recorded cost on T10 despite token usage warrants reconciliation.

**Executive decision requested:** Accept the as-built capstone as a *submission baseline* subject to evidence and GitHub sanitization review. **Do not authorize Phase B**—multi-tenant data access, production alerting, enterprise-grade approvals, SLAs, or a ten-user pilot—without a separate business case, risk review, budget, security assessment, and signed go/no-go.

### Outcome and evidence at a glance

| Workstream | Demonstrated / recorded | Still to prove before production |
|---|---|---|
| Delivery intelligence | Five specialist agent exports; grounded draft examples | Adoption, business value and independent source verification |
| Data correctness | Source-snapshot controls; 25-task fixture reconciles to 5/3/1/16 | Fresh live source sync; tenant-level isolation |
| Evaluation | T1–T12, 12/12 overall evaluation passes in #5814 | Broader edge cases, sustained reliability and independent QA sign-off |
| Human oversight | ReviewDispatcher, review records and example approved report artifacts | Trusted reviewer identity, immutable server-enforced approval and audit |
| Economics | Measured-generation token counts; estimated model-cost ledger | End-to-end cost per accepted report, full model-call accounting, labor time study |
| Proactive operations | On-demand milestone/blocker analysis | Scheduled monitoring, durable alert lifecycle and operational SLOs |

---
## 1. Executive summary and authorization requested

MIRA helps program leaders convert an **approved project evidence snapshot** into repeatable, reviewable project plans, risk assessments, sprint and project status reports, milestone intelligence, and stakeholder communication drafts. It is initially demonstrated on the ABCDE Ltd. AI Adoption Project for Nexora.

**As-built architecture.** An n8n Chat Interface qualifies requests; the Project Selector identifies relevant ready snapshots; an Orchestrator retrieves project evidence and routes work to five specialist subworkflows: **Planner, Risk Assessor, Status Reporter, Milestone Tracker, and Stakeholder Update**. n8n Data Tables hold snapshot and project records; validators reconcile source facts. Evaluation Harness, LLM-as-Judge, Langfuse exporter and TokenUsageCollector produce independent evaluation and operational evidence. ReviewDispatcher supports human-reviewed artifact generation, including Google Docs/PDF delivery in the capstone workflow.

**Why n8n:** ADR-014 selects one primary orchestration runtime rather than a Langflow-first or n8n+Langflow hybrid stack. This reduces orchestration boundaries but does not inherently lower model token pricing or remove external-provider/security dependencies. The implemented solution uses both visual n8n nodes and **JavaScript Code nodes**; it is *low-code*, not exclusively no-code.

**Authorization distinction:** Existing technical functionality and completed capstone regression tests are documented evidence; they are **not** a signed enterprise charter or business production authorization. A separate Phase B go/no-go is required for funding, real-client data, production identity/authorization, live connectors, schedule-based monitoring, and enterprise operation.

## 2. Business problem and opportunity

| Business problem in the capstone | Current baseline | Intended improvement |
|---|---|---|
| Manual project-plan creation | 3–4 hours per project | Grounded plan drafts with less preparation and review effort |
| Inconsistent risk assessments | No consistent matrix | Standardized risk register views and mitigations |
| Weekly status reporting overhead | 1–2 hours per report | Accurate filtered counts and repeatable reports |
| Late discovery of blocked or slipping milestones | Primarily reactive | On-demand alerts in capstone; scheduled detect–notify–acknowledge–escalate in proposed production phase |
| Inconsistent project documents | Formats vary | Governed plan, risk, report, and update templates |
| Multiple projects with limited PM capacity | Few PMs manage concurrent work | Reusable workflows; project-scoped selection in production portal |

**Value hypothesis (not yet measured):** The system may allow PMs/TPMs to spend less time compiling project artifacts and more time managing risks, decisions, dependencies, and stakeholders. Benefits are hypotheses until measured against manual baselines during evaluation and pilot.

## 3. Vision and measurable objectives

**Vision:** Deliver trustworthy and reviewable project intelligence that helps TPMs and PMs make faster, evidence-informed decisions while transparently distinguishing **source facts, unknowns, and AI-generated recommendations**.

| Objective / KPI | Acceptance or proposed target | Evidence or current status |
|---|---|---|
| Baseline acceptance | T1–T12 overall passes | **12/12 recorded** in regression #5814 on 2026-11-18 |
| Data reconciliation | Counts reconcile to approved source rows | Sprint 3 and project-wide results present in recorded outputs; requires ongoing regression |
| Grounding / trustworthiness | Zero unsupported source claims in accepted test scenarios | Harness recorded no hallucinations in batch; **not independently verified against every source fact** |
| Response time | Proposed routine p95 <30 seconds | **Target, not certified**; sample latencies vary by scenario |
| Model cost visibility | Tokens, model, estimates and cost per accepted artifact | Token collector and workbook; complete-call cost accounting remains a gap |
| Planning effort | Target ≤30 minutes PM review+preparation vs 3–4 hours manual | Pilot time study **not performed** |
| Reporting effort | Target ≤15 minutes PM review+preparation vs 1–2 hours manual | Pilot time study **not performed** |
| Milestone detection | Identify seed blocker T024 and disclose unanchored calendar uncertainty | On-demand proof in T8/T11; proactive alerts **not deployed** |
| Stakeholder governance | No external publication without authorized review | Capstone approval path shown; enterprise enforcement **not certified** |
| Adoption / satisfaction | Proposed controlled pilot of 10 PMs/TPMs with feedback and acceptance tracking | **Phase B only; not authorized** |

Business-goal figures such as ABCDE's **15% delivery-delay reduction** and **10% operating-cost reduction** belong to the simulated client AI adoption program. They are **not** MIRA performance gains. Forecast benefits, targets and SLAs require sponsor approval and measurement before being represented as achieved.

## 4. Scope and phased deliverables

### Phase A — Implemented capstone / controlled prototype (PRD v1.3)

**Delivered design and workflow exports:**

- Approved instructional sources (`project_description.txt`, `project_timeline.csv`, `project_risks.csv`, `sample_task_board.csv`, and `eval_mira_inputs.txt`), plus approved reference documents.
- `MIRA_Data_Upload`, source extraction/validation, versioned project snapshots and `MIRA_Get_Project_Data` retrieval backed by n8n Data Tables.
- `Mira_10_Chat_Interface`, `Mira_Project Selector` and `Mira_00_Orchestrator`; contextual qualification, guarded snapshot selection and specialist dispatch.
- Five specialist workflows: Planner, Risk Assessor, Status Reporter, Milestone Tracker, and Stakeholder Update.
- Source-grounded draft plans, risk registers, sprint-status summaries, project-wide counts, blocker identification, relative milestone reasoning and audience-oriented stakeholder drafts.
- `Mira_99_Evaluation_Harness`, LLM-as-Judge, Langfuse trace exporter, TokenUsageCollector and recorded regression/quality/cost evidence.
- `Mira_12_ReviewDispatcher` with reviewer interaction, approval-status tracking and a Google Workspace document/PDF path; capstone examples exist but enterprise authorization has not been proven.

**Recorded evidence:** Test run #5814 reported 12/12 overall passes. Chat Output History separately demonstrates formatted reports and short-circuit clarification for underspecified T2/T4/T6/T9 cases, although chatbot wording is not identical to every harness acceptance question. The exported workflow inventory totals **15**. A workflow export indicates configuration, not proof that every path was executed successfully.

**Source data contract and MIRA-001:** The canonical 25-task CSV contains **5 Done, 3 In Progress, 1 Blocked and 16 To Do**. T024 is `Security review of AI infrastructure`, `Blocked`, due 2026-05-26. Sprint 3 contains four tasks—T007/T025 In Progress, T008/T009 To Do. No business rule assumes every future project has 25 tasks. The assignment's narrative incorrectly suggests 3 Done; **CR-DATA-001** preserves this discrepancy with the CSV as the operational fixture pending owner agreement.

**Scope exclusions / limitations:** No production end-user SSO or enforced multi-tenant RBAC; no certified immutable audit gate; no guaranteed live source freshness; no production SLO; no unrestricted autonomous writes or external sends. Optional SharePoint, Jira, Trello and Confluence intake paths are not treated as live when disabled in the exported Data Upload workflow. Reports are based on approved snapshots rather than automatically verified live tasks.

**GitHub capstone deliverables:** Sanitized n8n workflow JSONs (all 15), README, updated architecture/TDD/charter, Q1 ideation, Q2 1–2-page build summary/cost and evaluation, Q4 reflection, T1–T12 input/output ledger and evaluation workbook, sanitized n8n/chat/Langfuse screenshots, and slide deck. Actual file presence and link targets must be checked before claiming final delivery.

### Phase B — Proposed production engineering / controlled pilot (change-controlled; not authorized)

**Potential scope, only after approval:** Authenticated portal; tenant/project policy enforcement; approved project registry and source refresh; read-only Jira/Trello synchronization; managed versioned storage; malware screening and governance; signed/verifiable approvals and durable audit; scheduled/event-triggered milestone checks; deduplicated alert/outbox lifecycle with owner acknowledgement and escalation; operational monitoring, backup/recovery, quotas, staged releases and P01–P15 verification. A **10-PM/TPM pilot** is a proposed adoption experiment, not an authorized deployment.

**Explicit boundary:** FR-019–FR-026 / CR-001 / CR-PROD-001 and any production budget are *not* accepted by inclusion in architecture diagrams or this charter. Material changes require fresh owner approvals and release evidence.

## 5. Solution approach and architecture

**As-built references:** [Architecture.md](Architecture.md) v2.3 (seven Mermaid diagrams, with delivered and proposed flows distinguished) and [Technical_Design_Document.md](Technical_Design_Document.md) v2.3. ADR-014 explains n8n-only selection.

| Capability | Phase A as-built baseline | Phase B enterprise target |
|---|---|---|
| Experience and qualification | Conversational Chat Interface; intent, snapshot and context checks; clarification branch | Authenticated product UI and identity-aware access |
| Project and source selection | Project Selector locates relevant ready snapshot; n8n Data Tables; Get Project Data | Verified project-membership policy *before* lookup and scoping |
| Ingestion | File upload, parsing, normalization, snapshot records; optional integrations disabled where unconfigured | Malware screening, secure connectors, freshness/checkpoint policy |
| Orchestration | n8n Orchestrator and reusable subworkflow calls | Controlled concurrency, failure handling and release promotion |
| Specialists | **Five**: Plan / Risk / Status / Milestone / Stakeholder Update | Versioned prompts, models and governed change control |
| Fact reconciliation | Snapshot-level task IDs/counts, sprint scopes, citation references, explicit unknowns | Continuously reconciled live-source assertions and anomaly monitoring |
| Evaluation and monitoring | Eval Harness, Judge, trace exporter, TokenUsageCollector, n8n execution history | Production SLOs, independent quality sampling, security audit and alerting |
| Review and artifacts | ReviewDispatcher, reviewer interactions, draft/approved PDF examples | Enforced reviewer identity, immutable approvals, publication checks and audit trail |
| Repository | Reviewed/sanitized manual JSON exports in GitHub | Approved promotion pipelines and environment separation |

**Logical request sequence:** `Chat request → classify/qualify → discover approved snapshot → retrieve stored source evidence → Orchestrator → required specialist(s) → validate/reconcile → formatted DRAFT response`. The **clarification branch exits earlier** when a request is insufficient (T2/T4/T6/T9), avoiding specialist generation; conversation routing may still cost tokens. Evaluation and approval are *separate* paths, not prerequisites for every chat reply.

**Trust boundary:** n8n editor/workspace permissions do not implement business-user project authorization. Uploads, API content and model outputs are untrusted data. Neither prompt text nor an approval email alone can establish enterprise data access or immutable publication permission. Use synthetic data in the capstone and approved business controls for production.

**Technology accuracy:** The delivered platform is n8n-orchestrated **low-code** (including JavaScript Code nodes). Do not claim a purely no-code build. The objective is deterministic source processing and auditable separation between automated drafts and human decisions.

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

**Decision framework (proposed; no sign-offs evidenced):** Product Owner may prioritize within an approved phase; Technical Architect approves design within organizational delegated authority; sponsor approves material cost, production scope, and business exceptions. Security/privacy owners must approve real-client data use and production access controls. No person may self-approve a report where separation of duties is required by policy.

**Meeting cadence (proposed):** Weekly delivery review; fortnightly architecture and risk review; phase-gate steering meeting. ADR-014 and n8n G1–G7 readiness require named reviewers. Maintain a decision log, RAID log, issue backlog, and change-request register.

## 7. Delivery plan, milestones and decision gates

**Gate policy:** C0 and G1–G10 remain traceable to TDD v2.3. A gate can have completed technical evidence without an approved sponsor/security sign-off; do not mark formal authorization complete by inference. P01–P15 remain production-only tests.

| Gate | Deliverable / evidence | Evidence state on 2026-11-19 | Decision owner |
|---|---|---|---|
| **C0 — Charter / ADR** | Charter boundaries, owners, ADR-014 and trial-data policy | n8n implemented; **formal sponsor sign-off pending** | Sponsor / CTO delegate |
| **G1 — Platform** | n8n workflows, OpenAI integration, import/export configuration | **Exported workflows available**; restore test not independently verified | Platform / Architect |
| **G2 — Source readiness** | Canonical CSV headers, 25-task fixture, risk IDs and relative-week semantics | **Input profile documented**; source-owner approval pending | Data owner / QA |
| **G3 — Deterministic source checks** | Counts, task IDs, sprint filter, invalid-data handling | **Successful recorded outputs** for count/sprint scenarios; exhaustive negative ingestion tests not confirmed | Architect / QA |
| **G4 — Specialists** | Five executable/reusable specialist exports and contracts | **Five workflow exports present**; individual evidence depth varies | Tech lead / QA |
| **G5 — Routing and review** | No-data gating; review path; no unauthorized publication | Chat and ReviewDispatcher configured; enterprise auth control not established | TPM / Architect |
| **G6 — Test / evaluation** | 12 baseline test inputs and outputs, Judge, trace/usage evidence | **12/12 overall passed** in run #5814; evaluation caveats retained | QA / Evaluation |
| **G7 — Submission release** | Redacted JSON, docs, evidence, screenshots, link, reproduction | **Assembling / review required**; do not claim portal submission completed | TPM / Architect |
| **G8 — Production scope** | Sponsor-funded CR-PROD-001, FR-019–FR-026, policies | **Not approved** | Sponsor / Product Owner / Security |
| **G9 — Proactive-alert POC** | Fresh sync, schedule, durable dedup, delivery/acknowledgement | **Proposed only** | SRE / Delivery Ops |
| **G10 — Production controls** | P01–P15, tenancy, approval security, recovery and operating targets | **Not tested/approved** | Security / SRE / CTO |
| **Pilot / scale** | Ten authorized PMs/TPMs, training, value study, support, go/no-go | **Proposed only** | Sponsor / Product Owner |

**Delivery progression (historical):** Requirements and solution baselines → n8n implementation and workflow exports → pinned source snapshots → 12-case evaluation and iterative fixes → architecture/TDD/charter as-built update → publication of reviewed capstone package. The October planning weeks in v1.2 are *not* represented as measured delivery durations.

**TPM controls:** Maintain a RAID and decision register, workflow inventory/dependency map, evidence links, versioned prompt/model assumptions, defects and open questions. GitHub commits do **not** deploy n8n automatically; import/rebinding and smoke testing remain necessary.

## 8. Budget, resources, economics and dependencies

**Funding status:** No approved enterprise program budget or named staffed delivery capacity is evidenced. Capstone traces demonstrate partial **model-cost estimation**, not full operating expenses. The business case for automation savings remains hypothetical until a review-inclusive time study.

### Recorded capstone usage and cost evidence

| Measure | Run #5814 — November 18, 2026 | Interpretation |
|---|---:|---|
| Baseline scenarios | 12 | All overall verdicts passed under harness criteria |
| Captured input generation tokens | 84,492 | From cases with recorded specialist usage |
| Captured output generation tokens | 13,232 | From cases with recorded specialist usage |
| **Captured generation tokens** | **97,724** | Does not include all possible conversational/evaluator calls |
| **Sum of estimated model-cost fields** | **$0.03974425** | Partial recorded model estimate; **not** total batch spend |
| T1 detailed plan | **13,479 tokens; $0.00730725 estimate** | Suitable illustrative per-plan cost, with provider/rate assumptions |
| T2, T4, T6, T9 | No specialist generation usage collected | Clarification path avoids specialist call; routing cost may remain |
| T10 | 15,859 tokens; **$0.00 recorded** | Cost field anomaly; reconcile before aggregating actual spend |

**TPM economic model:** For each accepted report type, calculate `(input_tokens × input_rate + output_tokens × output_rate) / 1,000,000` using dated provider prices and model identity, then add all agent/evaluator/routing calls, n8n hosting/executions, storage, external services, retries and support. Compare **cost per approved artifact** and **PM preparation + review hours** with manual baselines. Report illustrative volume scenarios separately from measured figures.

**Resources/dependencies:** Approved synthetic instructional files; n8n account/edition and workflow IDs; OpenAI credentials; Data Table definitions; Google Workspace and Langfuse connections; review/test participants; repository sanitization; future enterprise portal, IdP, project registry, authorized connectors, notifications, operations and privacy policies. No Langflow or Make.com orchestration service is required by the as-built configuration.

**Staffing assumption:** A capstone developer may implement and test multiple roles; a production release must separately name security, QA, operations, approval and sponsor decision owners.

## 9. Risk, assumptions, issues and dependencies (RAID)

| Risk ID / concern | Business or technical impact | Mitigation, gate and owner |
|---|---|---|
| R01 – Data-validation regression in low-code processing | Wrong counts, IDs, dates or incomplete joins | **G3 MIRA-001**: retain automated reconciliation and add malformed/duplicate negative fixtures; Architect/QA |
| R02 – Workflow branch loses project context | Mis-scoped report or mixed-project data | Explicit `project_id`, trusted authorization in Phase B, negative tests P02; Security/Data owner |
| R03 – LLM invents task facts or planned dates | Misleading status/risk decisions | Deterministic facts, data-source IDs, schema assertions, missing-data tests and reviewer; QA |
| R04 – Upload/API data stale, malformed or malicious | Missed alerts, prompt injection, privacy incident | Validation, source timestamps, quarantine, read-only access and P03/P04/P09; Data/Security |
| R05 – n8n forms/workspace are mistaken for an enterprise portal | Client data exposure | Synthetic capstone data only; SSO + trusted project RBAC in Phase B, P01/P02; Security |
| R06 – Single n8n runtime fails or hits plan limits | Business/AI automation outage and lost checks | Hosting/edition due diligence, retry/outbox/backups, scale and recovery tests; SRE |
| R07 – Alert retries create duplicate notifications | Alert fatigue and repeated escalation | Durable alert lifecycle, idempotency keys, outbox/dead-letter, P05–P07; Ops/SRE |
| R08 – Human approval bypass or report tampering | Unauthorized stakeholder communication | Draft-only Phase A, durable role-aware approval before publication in Phase B, P08; Product/Security |
| R09 – Model/provider version or pricing changes | Quality regressions or unpredictable usage costs | Model pinning, regression runs, budget caps and provider review; Architect/Finance |
| R10 – Incomplete AI usage/grounding telemetry | Weak evidence for instructor or release decision | G6 ledger; reconcile uncollected calls and T10 $0 field; independent source review; QA |
| R11 – n8n JSON export leaks credentials/payloads | Exposure through GitHub | Credential/redaction review, least privilege, secret scanning, P10; Security |
| R12 – Feature and scope inflation | Delivery delay and unclear approvals | Strict G8 production authorization, RAID/change log, PRD v2.0; Sponsor/PM |

**Assumptions:** Phase A handles approved instructional/synthetic datasets; real client data, production user identity and integrations are **not** available by implication. Missing dependencies or field mappings are marked **unknown**, not inferred. Workflow features, pricing, security capabilities and execution limits must be validated in the actual n8n edition and hosting plan.

**Open issues / decisions:** (a) obtain source-owner agreement on CR-DATA-001 and T7/T11 policies; (b) confirm negative ingestion and cross-project tests beyond the 12-case harness; (c) reconcile T10 cost and independent source-grounding warnings; (d) review GitHub exports for sensitive credentials/IDs; (e) obtain ADR-014/C0 and any Phase B sponsor/security approvals.

**Current implementation-specific risks:** (1) a `validation_status=PARTIAL` report may legitimately pass an *overall* harness scenario but still require human follow-up; (2) chat clarification responses can be less specific than the official expected prompts; (3) snapshot content is historical rather than live; and (4) cost fields can be missing or zero despite recorded tokens. These are disclosed evaluation/operations limitations, not grounds to reinterpret passing tests as production certification.

## 10. Quality, acceptance and release gates

### Phase A — Observed capstone evaluation and residual verification

The prescribed **T1–T12** acceptance dataset was replayed in evaluation run **#5814** on **November 18, 2026**. The recorded field `overall_eval_pass` is `true` on all 12 cases. The evaluator's positive result is specific to the recorded inputs, validation logic and Judge version; some tests document source-evidence limitations or partial specialist validation.

| Baseline tests | Acceptance evidence / caveat |
|---|---|
| **T1/T2/T9 — Plan** | Grounded eight-phase plan on approved timeline; vague plan paths request more information rather than fabricate a full plan |
| **T3/T4/T7 — Risk** | Source-backed categorized risk IDs and a disclosed qualitative top-three selection; unspecified projects trigger clarification |
| **T5/T6/T10/T12 — Status/communication** | Exact Sprint 3 four-task subset; no-data refusal; full-board 25-task reconciliation; scoped Sprint 2 stakeholder draft |
| **T8/T11 — Milestones** | T024 blocked task identified; relative timeline and missing calendar-anchor limitations disclosed |

**Separate chat history:** T2/T4/T6/T9 have **no specialist run ID** because they are handled by chat-level clarification; that is intentional. The live chat responses are often shorter than the harness's explicit rubric questions (T2 should ask for scope/timeline/team size, T9 for scope/goals/deliverables). Include this gap as a targeted UX improvement rather than retroactively editing test evidence.

**Observed caveats:** No independent full-source truth verification is established by the Judge scores alone; not all request/model/evaluation token usage is captured. Some report fields carry `PARTIAL` validation. Example PDF approvals illustrate the review workflow but do not prove a secure immutable enterprise approval gate. Actual p95 and business time savings are not claimed from these twelve runs.

**Release evidence before G7 close:** Sanitized export inventory (15 workflows), full baseline input/output and eval history, representative Langfuse trace and reviewer screenshots, cost assumptions and anomaly note, architecture/TDD/charter links, Q1/Q2/Q3/Q4 deliverables, slide deck and GitHub public link required by the capstone. The security review must precede making a repository public.

### Phase B — Production verification (not inferred from capstone evidence)

All **P01–P15** remain required in appropriate staging before production authorization: SSO/tenant isolation; malicious file, prompt and replay tests; stale-source fail-closed behavior; scheduled monitor and deduplication tests; notifier resilience; server-enforced approval identity; secret/redaction checks; rollback/restore; quotas; incident response; full baseline regression. Security/SRE/CTO delegates must review the evidence.

**Proposed (not achieved) operational targets:** 99.9% availability, routine report-generation p95 ≤30 s under defined load, successful scheduled scans ≥99%, RPO ≤1 hour / RTO ≤4 hours if the platform supports them, and an alert sent within 15 minutes **after successful detection**. Define data refresh cadence before promising real-world time-to-detect.

## 11. Proactive milestone alert lifecycle and human controls

Alert states: `DETECTED → NOTIFIED → ACKNOWLEDGED → ACTION_ASSIGNED → MONITORING → RESOLVED` with `ESCALATED` when the approved escalation policy is triggered. Store event timestamps, source version, assigned owner, severity, acknowledgement, action and history. A deduplication key must prevent duplicate unchanged alerts; state changes may generate new notifications.

**Capstone status:** ReviewDispatcher, reviewer interactions and example approved report artifacts evidence a workflow-level review path. They are not proof of independent enterprise reviewer authentication, immutable report/version hashing, or server-enforced authorization. The proposed alert lifecycle below remains Phase B only.

**Separation of approval responsibilities:** A system may issue an internal evidence-backed risk notification without prior report approval so warnings are not delayed. PM/TPM approval is mandatory before publishing a formal stakeholder report, changing committed dates, or initiating any high-impact corrective action. Reject/revise flows return to draft and preserve audit history.

## 12. Communications and stakeholder engagement

| Audience | Communication | Proposed cadence |
|---|---|---|
| Executive sponsor | Outcomes, budget, milestones, major risks, go/no-go decisions | At phase gates; otherwise monthly |
| Delivery team | Backlog, defects, blockers, decision log | Weekly |
| Architecture / security reviewers | ADRs, interface changes, controls and test findings | At design reviews and changes |
| Pilot PMs/TPMs | Training, feedback collection, usage guidance | At onboarding and during pilot |
| Affected project owners | Evidence-backed risk notifications and escalations | On qualifying changes and follow-up checks |

Formal client-facing communications require an authorized reviewer and approved distribution channel. The capstone's Gmail/Docs/PDF review path should be presented as an implemented prototype, not an approved organization-wide communications policy.

## 13. Rollout, adoption and benefits realization

1. **Controlled capstone (implemented):** Demonstrate approved source data, five specialist sub-workflows, T1–T12 recorded evaluation, reviewer example artifacts, and sanitized GitHub exports.
2. **Production readiness:** Implement and verify identity, isolation, ingestion, monitoring, approval, reliability and recovery.
3. **Limited pilot:** Enroll 10 PMs/TPMs with authorized projects and clear support channels.
4. **Measure benefits:** Compare actual plan/report preparation times, correction effort, detection latency, alert precision, false positives, user adoption and cost.
5. **Scale decision:** Expand only after sponsor confirms business value and platform owners accept operations/security responsibilities.

No automatic enterprise rollout is authorized by this charter.

## 14. Change control, baseline management and decisions outstanding

| Change / decision | Current disposition | Required authority / evidence |
|---|---|---|
| **ADR-014 — n8n-only implementation** | **Implemented capstone choice**; architecture rationale recorded | Formal sponsor/architecture ratification still pending |
| **Charter v1.3** | As-built update; preserves original Q3 scope, boundaries, governance and roadmap | Charter owner review; sponsor sign-off not evidenced |
| **Architecture/TDD v2.3** | Updated as-built configuration and seven architecture views | Technical and QA review of deployed versions against exports |
| **PRD v1.3** | Baseline FR-001–FR-018 / T1–T12 retained | Product owner approval of any changed acceptance mapping |
| **CR-DATA-001** | CSV-backed task count 25 = 5/3/1/16 recorded; prose differs | Data owner / QA to accept CSV precedence |
| **Cost ledger reconciliation** | T10 has 15,859 tokens yet $0.00 recorded cost | Evaluation owner / finance assumptions before cost claim |
| **CR-001 / CR-PROD-001** | FR-019–FR-026 proactive monitoring remains proposed | PRD v2.0, sponsor funding and policies |
| **Production portal / approvals** | Authenticated portal, tenant RBAC and durable approval target only | Security/architecture/funding approval and P01–P15 |
| **Pilot and SLOs** | Ten-user pilot and service targets not authorized | Sponsor/SRE/Finance go/no-go |

**Change-control protocol:** Record proposed changes and impacts on scope, cost, data, security, schedule and tests; obtain owner decisions; modify PRD/charter/architecture/TDD with consistent versions; review sanitized GitHub commits and workflow manifests; replay relevant tests; retain prior tagged/archived baselines.

### CR-DATA-001 — Source-data discrepancy and temporal/risk acceptance

The assignment narrative indicates **3 Done / 3 In Progress / 1 Blocked / 18 To Do**, but the supplied canonical task-board CSV yields **5 Done / 3 In Progress / 1 Blocked / 16 To Do** across 25 task IDs. The as-built full-board output uses the CSV counts. The original assignment and source files remain unmodified; a product/data-owner acceptance is still an open governance action.

**Additional source-policy decisions:** (1) the risk register has no numeric likelihood, probability or authoritative rank, so T7's top-three is a **qualitative, declared selection**, not a numeric source rank; (2) `project_timeline.csv` contains weeks 1–24 without an approved calendar start date, so T11 must not fabricate absolute upcoming milestone dates; (3) static task-board due dates are historical; `as_of_date` must be explicit for any meaningful deadline comparison. The recorded tests/outputs, rather than revised test prompts, remain the objective evidence.

## 15. Architectural baseline rationale — ADR-014: n8n-only vs. Langflow-only vs. hybrid

**Decision context:** MIRA must connect file/data operations, specialist generation, exact data checks, evaluation, user clarifications and review/notification workflows. Separating these across Langflow/Make/n8n would introduce an additional authenticated runtime boundary and more release and trace-correlation complexity.

| Option | Strength | Main TPM/program trade-off | Disposition |
|---|---|---|---|
| Langflow-only | Strong visual AI/agent graph construction | Extra design for business integrations and durable review | Historical candidate; not selected |
| n8n + Langflow hybrid | Specialized AI runtime plus business workflow automation | Two runtimes, additional retries/security/deployment, separate traces | Contingency if requirements demand it |
| **n8n-only** | Single orchestrator for chat, source ops, agents, evaluation and review | Low-code complexity, n8n edition limits, external credentials and production security still needed | **Implemented for capstone** |

**Observed result:** The selected n8n architecture has workflow exports and recorded T1–T12 evaluation results. Its success in the capstone supports the implementation choice for this use case; it is **not** a general proof that n8n is cheaper, safer, more scalable or always preferable to a hybrid stack.

**Conditions for formal adoption:** Verify clean import and environment rebinding, retained source validity, risk/clarification controls, full cost instrumentation, operational failure recovery and the selected hosting edition. Security and sponsor approval remain necessary for any real-client production release.

**Scope impact:** Original requirements and Q1–Q4 assignment obligations stay intact. FR-019–FR-026 and P01–P15 production controls remain governed by CR-PROD-001 rather than inherited by the capstone.

## 16. Charter approval and sign-off

| Approval role | Named approver | Requested evidence / decision | Status |
|---|---|---|---|
| Executive sponsor / CTO delegate | TBD | Accept charter scope, value hypothesis, funding and phase boundaries | **Pending formal approval** |
| Product Owner / AI PM/TPM | TBD | Confirm business requirements, acceptance policy and pilot goals | **Pending formal approval** |
| Enterprise Architect / AI lead | TBD | Ratify ADR-014 and as-built architecture/TDD alignment | **Pending formal approval** |
| Data Owner / QA Evaluation Lead | TBD | Sign off CR-DATA-001, evaluation evidence and coverage limits | **Pending formal approval** |
| Security / Privacy | TBD | Review data handling, public repo redaction, provider/privacy risks | **Pending formal approval** |
| Platform / SRE | TBD | Verify licensing, recovery, operations and production gate plan | **Pending formal approval** |

**Submission disposition:** The as-built capstone is suitable for instructor/evaluator review when the relevant files and evidence are attached. Its technical acceptance metrics **do not equate to any signatory having approved a production system**.

**Phase B authorization:** Separate PRD v2.0 / CR-PROD-001, owned budget, legal/privacy/security and operations review, P01–P15 verification, and a recorded go/no-go remain mandatory before a ten-user pilot or live operation.

## 17. Document control, preserved baselines and revision history

| Version | Date | Key change | Disposition |
|---|---|---|---|
| v1.0 | October 25, 2026 | Historical charter and Langflow-first baseline; original business/governance case | Archived historical baseline |
| v1.1 | October 25, 2026 | Proposed n8n-only ADR-014 and G1–G10; retained T1–T12 and proposed production scope | Historical proposal |
| v1.2 | October 25, 2026 | Source-profile rebaseline, 25-task 5/3/1/16 and CR-DATA-001 | Historical proposed baseline |
| **v1.3** | **November 19, 2026** | **As-built 15-workflow baseline, five specialist agents, chat clarification, versioned snapshot, Eval Harness/Judge/Langfuse/TokenCollector, reviewer pathway, #5814 evidence, cost caveats and release boundaries** | **Capstone submission documentation; formal organization approval pending** |

**Repository placement and evidence links (verify actual files before publishing):**

- `docs/Project_Charter.md` — this document; Q3 Program Charter.
- `docs/Architecture.md` — seven refreshed Mermaid diagrams, as-built/future-state separation.
- `docs/Technical_Design_Document.md` — implementation contracts and detailed production controls.
- `testing/` — the baseline workbook, evaluation history and Chat Output History; do not remove warnings or alter original test inputs.
- `flows/` — all 15 separately sanitized n8n JSON exports and dependency/rebinding manifest.
- `screenshots/` — redacted workflow canvas, chat, evaluation, Langfuse and approval evidence.
- `docs/` / `presentation/` — Q1 ideation, Q2 summary/cost, Q4 reflection and slide deck where actually present.

**Integrity rule:** Do not overwrite the historical v1.2 document without a retained Git history or archive. Do not represent a GitHub commit as an n8n deployment, a redacted export as immediately runnable, or an instructor-accepted capstone as a production authorization. Ensure README links point to real files in the repository.
