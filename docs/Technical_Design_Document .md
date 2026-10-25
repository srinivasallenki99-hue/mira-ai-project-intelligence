# Mira – Technical Design Document (TDD)

**Version:** 2.0 – Production Target  
**Status:** Production Target Design – NOT production certified; security and reliability gates pending  
**Date:** October 25, 2026  
**Project:** Mira – AI-Powered Project Intelligence Assistant  
**Client Program:** ABCDE Ltd. AI Adoption Project  
**Delivery Organization:** Nexora Pvt. Ltd.  
**Document Owner:** AI PM / TPM  
**Architecture Authority:** Enterprise Technical Architect / CTO  
**Requirements Baseline:** Mira PRD v1.1; **production change baseline:** PRD v2.0 / CR-PROD-001 approval required  
**Production Scope:** Approved-on-release proactive monitoring FR-019–FR-026 plus identity, tenancy, audit, deployment, resilience and security controls  
**Implementation Preference:** No-code application configuration where appropriate; managed infrastructure/security integration permitted  
**Platforms:** Authenticated application UI; Langflow; OpenAI (version-pinned production-approved model); LangWatch; GitHub; secure API gateway; managed relational database/object storage; managed scheduler/notification provider  
**Architecture Diagrams:** [Architecture.md](Architecture.md)

> **Production design change:** This version preserves the existing Mira business functions and all T1–T12 baseline evaluations. It *supersedes the MVP-only exclusions* through a production change request. Scheduled monitoring, alert ownership, identity, isolation, audit and resilience are mandatory **for a production release**, but remain unimplemented until validated. PRD v1.1 alone does not authorize these expanded requirements.

## 1. Executive technical summary

Mira is a no-code-first, multi-agent project intelligence assistant for Nexora PMs/TPMs. It turns approved project descriptions, timeline, risk, and task-board data into plans, risk analyses, accurate status reports and milestone intelligence. It supports stakeholder-ready draft communications, with mandatory human review before external distribution.

The architecture uses a **Router/Dispatcher** with four independently testable Langflow specialist flows (Planner, Risk Assessor, Status Reporter and Milestone Tracker). An AI model creates narratives and recommendations; deterministic processing computes exact counts, filters and date conditions. Project sources remain authoritative. LangWatch evaluates LLM behavior, and GitHub versions flow JSON, documents and test evidence.

**Production target:** Fresh project data is checked on a schedule or supported source event, rule-based milestone threats are identified, the Milestone Tracker explains evidence and recommended action, a deduplicated alert is sent to an assigned PM/TPM, and acknowledgement, mitigation and escalation are tracked. This closes the **detect → notify → acknowledge → act → recheck** loop, but does not guarantee avoidance of all missed milestones.

## 2. Business context, drivers and outcomes

ABCDE's six-month AI adoption program spans demand forecasting, route optimization, inventory management and customer support. Nexora faces manual planning (3–4 hours), manual weekly reporting (1–2 hours), inconsistent risks, late awareness of milestones, lack of standardized governance and too few PMs across multiple projects.

| Driver | Baseline problem | Mira outcome | Verification |
|---|---|---|---|
| BD-01 | Planning takes 3–4 hours | Grounded phase/milestone plan | T1, T2, T9; user time study |
| BD-02 | Inconsistent risk matrices | Source-backed categorized register | T3, T4, T7 |
| BD-03 | Reporting takes 1–2 hours | Exact sprint/project status and report | T5, T6, T10, T12; time study |
| BD-04 | Missed milestones detected late | On-demand tracking (baseline); proactive monitoring (CR-001) | T8/T11; new synthetic alert-lifecycle tests |
| BD-05 | Unstandardized outputs | Versioned templates and fields | Output schema tests |
| BD-06 | Limited PM capacity | Reusable specialist flows, optional project selector | Measured adoption and multi-project isolation tests |

**Proposed acceptance targets** (not achieved results): 12/12 capstone scenarios after remediation, 100% valid task-count reconciliation, zero unsupported source facts in accepted test outputs, recorded traces, per-plan model cost, and routine request p95 under 30 seconds subject to measurement. The extension proposes source-freshness, notification and acknowledgement service measures; these require policy approval.

## 3. Scope and release boundaries

### 3.1 Preserved business capabilities (PRD v1.1)

- One authoritative ABCDE project dataset, with an explicit logical `project_id`.
- User-initiated file upload and ingestion of project description and CSVs.
- Chat request routing to four specialist workflows.
- Plan, risks, sprint status, milestone alerts on request and stakeholder email **drafts**.
- Deterministic task counts and time-window calculations, source grounding and controlled missing-data results.
- Manual PM/TPM review, Markdown/JSON output, LangWatch evaluation and GitHub JSON export.

### 3.2 Production enhancement: CR-001 proactive monitoring (release-gated)

- Scheduled checks using **Make.com or an approved no-code scheduler**, optionally source event triggers.
- Fresh authorized read-only Jira/Trello import or scheduled approved task-board snapshot refresh.
- Persisted deduplicated alert register, ownership, acknowledgement, mitigation and escalation.
- Email/Slack/Teams internal notifications; approved external report distribution only.
- Alert-lifecycle metrics and synthetic state-transition test fixtures.

### 3.3 Production-required vs. explicitly deferred

**Required for production:** authenticated user-facing project selector; SSO/OIDC at access tier; project-scoped authorization; persistent relational and object storage; tested backups and restore; high availability appropriate to agreed SLO; deployment pipeline and rollback; audit trails; scheduled monitoring; notification/escalation; read-only Jira/Trello connectors if enabled; incident response.

**Deferred unless separately approved:** autonomous Jira/Trello writes, autonomous external customer communications, model fine-tuning, complex RAG, ERP/logistics execution. Bubble, Make and Google Sheets are *not* mandated production components; choose governed, supported services after enterprise review. No-code does not imply zero operations.

## 4. Architecture principles and boundaries

1. **Authoritative data first:** no invented approved task status, milestone, risk or date.
2. **Deterministic computation:** filters, task counts, due-date evaluation and alert de-duplication are not performed by unconstrained LLM reasoning.
3. **Separation of duties:** model recommends and explains; PM approves commitments, report release and outward-facing changes.
4. **Project isolation:** every retrieved record matches selected `project_id`, including future multi-project pilots.
5. **Fail visibly:** invalid, absent, conflicting or stale data yields an explicit exception/warning, not false confidence.
6. **Evidence over presentation:** validated record references and source version accompany each accepted report.
7. **No secret exposure:** API credentials remain in supported secret/configuration settings and out of repository and traces where possible.
8. **Traceable lifecycle:** log both AI evaluation and non-LLM monitoring, notification and approval state.

### 4.1 Trust boundaries

User/UI → Langflow; file and optional Jira/Trello → ingestion; Langflow → OpenAI; Langflow → LangWatch; Make → authenticated Langflow endpoint; internal notifier → email/Slack/Teams; developer → GitHub. Real client data may cross third-party boundaries only after contractual/privacy approval. A Make cloud job **cannot use a local-only Langflow Desktop localhost endpoint**; a secured reachable deployment or supported bridge is necessary.

## 5. Logical and physical component design

See [Architecture.md](Architecture.md) for diagrams 1–4.

| Layer | Logical purpose | No-code implementation | Status |
|---|---|---|---|
| Experience | Select context, upload, request, preview | Langflow Playground/file configuration for fixed ABCDE; portal optional | MVP, UI details to validate |
| Request router | Intent dispatch, clarification | Langflow Agent + four Run Flow tools | Proposed |
| Planner | Project plan with real phases/milestones | Langflow specialist flow + Read File + prompt/schema | MVP |
| Risk Assessor | Registered risks and mitigations | Specialist flow + risk CSV | MVP |
| Status Reporter | Exact status and stakeholder drafts | Structured records + deterministic aggregation + LLM | MVP, aggregation proof pending |
| Milestone Tracker | Blocker and due-date checks | Timeline/task records + deterministic rules + LLM explanation | MVP |
| Project data services | Read, normalize, version and scope | Read File and visual data components; sheet fallback if necessary | Feasibility pending |
| Response assurance | Schema, record checks, warnings | Structured Output + explicit source comparisons/manual checks | MVP, enforcement proof pending |
| Human review | Approve/request changes/hold | Persistent approval workflow with reviewer identity, decision history and release gate | MVP procedural control |
| Monitoring scheduler | Periodic/event-driven execution | Governed managed scheduler or reviewed no-code provider | Production required |
| Alert registry | Durable alerts, ownership and lifecycle | Managed relational store; evaluated no-code access layer | Production required |
| Notification | Internal PM/TPM alerts | Approved notification provider and durable outbox | Production required |
| Observability | Model traces and quality | LangWatch, compatibility verified | MVP |
| Source control | Documents, exports and evidence | GitHub browser uploads and PR review | MVP |

### 5.1 Langflow flows

- `Mira_00_Orchestrator`: Chat Input → Agent with tool instructions → four Run Flow tools → Chat Output.
- `Mira_01_Planner`: description + timeline → sufficiency checks → generation → format/source validation.
- `Mira_02_Risk_Assessor`: scoped risk records → selection/ranking → risk output/source checks.
- `Mira_03_Status_Reporter`: task-board filter/aggregate → status narrative or stakeholder draft.
- `Mira_04_Milestone_Tracker`: blocked/overdue/upcoming evaluations → evidence-backed analysis.

Use explicit field/tool contracts for each Run Flow. The model's choice of tool is probabilistic; T1–T12 and additional routing cases must verify actual invocation. Multi-intent requests may be decomposed into supported calls or clarified. Human pause inside a nested subflow must **not** be assumed; MVP review occurs after output in the parent/UI/manual process.

### 5.2 Request contract

`request_id`, `project_id`, `user_message`, optional `intent`, `sprint`, `as_of_date`, `reporting_start`, `reporting_end`, `output_mode`, `source_version`. These are a logical contract; actual Langflow run API payload fields must be mapped and tested.

### 5.3 Response contract

`request_id`, `project_id`, `agent_name`, `result_status`, `response_data`, `source_references`, `missing_information`, `warnings`, `execution_metadata`. Allowed result statuses: `SUCCESS`, `INSUFFICIENT_DATA`, `VALIDATION_ERROR`, `UNSUPPORTED_REQUEST`, `SERVICE_ERROR`.

For traceability include workflow version, model identity and source snapshot ID when available. Result marked SUCCESS must pass required checks, not merely produce valid-looking JSON.

## 6. Project-data architecture and ingestion

The initial dataset is: `project_description.txt`, `project_timeline.csv` (8 phases), `project_risks.csv` (10 risks), `sample_task_board.csv` (25 tasks), `eval_mira_inputs.txt` (12 tests). Physical column names, dates and cross-record relationships **remain unknown until actual CSV inspection**; do not invent them.

| Logical entity | Canonical fields (where actually supplied) | Authority |
|---|---|---|
| Project | project_id, name, goals, scope, duration | Project description |
| Task | task_id, name, status, sprint, due_date, assignee | Task board |
| Risk | risk_id, category, description, impact, mitigation, priority if present | Risk register |
| Milestone | id if present, phase, name, date, documented dependency if present | Timeline |

### 6.1 Input workflow

User selects fixed ABCDE (MVP) → uploads approved files → parse TXT/CSV → verify required fields, unique IDs, valid statuses and dates → record project/source/version/import timestamp → make valid project-scoped records available to specialist flows. Read-only Jira/Trello is a future alternative source, not an additional unvalidated direct connection to the LLM.

Project Selector in a future portal requires a registry, authorization and project-scoped data lookups; a dropdown alone does not enforce isolation. GitHub commits do not auto-refresh files already loaded in Langflow.

### 6.2 Data quality and provenance

Check missing files, malformed data, duplicate IDs, unknown statuses, invalid dates, inconsistent schema, and missing task-to-milestone relationships. Preserve source IDs. Raise freshness warnings for old/unrefreshed snapshots. A report may be correct *for a snapshot* but must not claim real-time status. For formal runs pin dataset ID/version and as-of timestamp.

## 7. Deterministic rules, ranking and grounded generation

### 7.1 Status Reporter rules

Filter by requested `project_id` and sprint before counting. The expected **full board** totals from the assignment are 25: Done 3, In Progress 3, Blocked 1, To Do 18. These are expected checks, not observed actual CSV output. Unknown statuses must fail validation; don't map them silently to To Do. Completion percent is calculated from the same filtered denominator.

### 7.2 Milestone evaluation rules (MVP)

- `BLOCKED`: explicit task status BLOCKED.
- `OVERDUE`: incomplete task with due date **before** `as_of_date`.
- `UPCOMING`: milestone within an agreed interval; proposed inclusive `[as_of_date, as_of_date + 14 calendar days]` for T11.
- `AT_RISK`: only when documented dependency or delivery evidence supports concern. Merely due soon is **watch**, not confirmed delay.
- Missing dependencies or due dates produce an evidence limitation, not an invented relationship.
- T024 (`Security review`) must be identified when the actual dataset is loaded.

### 7.3 Risk ranking

Use source risk priority/impact fields when available. When ranking is absent, document a transparent provisional policy, label it as derived, or request a prioritization basis. T7's top three must reference actual `project_risks.csv` records.

### 7.4 Output assurance

Use separate controls for **schema validity** (required fields/types) and **factual validity** (record IDs, milestones, counts, source matches). Langflow Structured Output alone does not establish grounding. If automated semantic checks are not possible no-code, record manual assertions and disclose that limitation. LLM-proposed mitigations and activities are labeled recommendations.

## 8. Specialist output specifications

| Flow | Required output | Baseline evaluation |
|---|---|---|
| Planner | Objectives, scope, phases, activities, deliverables, milestones, dates, source refs, assumptions/questions | T1, T2, T9 |
| Risk | Actual risk IDs, categories, impacts, mitigations, stated ranking source and recommendations | T3, T4, T7 |
| Status | Filtered exact counts/tasks, blockers, upcoming items, evidence-based health or unknown | T5, T6, T10 |
| Status email mode | Draft only, subject/reporting period, achievements, blockers, next actions, strictly requested sprint | T12 |
| Tracker | Source task/milestone IDs, dates, alerts, reasons, recommended next steps, evidence gaps | T8, T11 |

Insufficient input requests should ask for missing scope/timeline/team for vague plans; missing project context for vague risks; and task data for unsupported status requests. No invented source metrics.

## 9. Human-in-the-loop report approval

The parent/user interaction presents generated output as `DRAFT` with evidence and warnings. PM/TPM then chooses **Approve**, **Request Changes**, or **Reject/Hold**. In MVP, this is a **manual documented process** (review and authorized sharing); do not claim a persistent approval button or automatically enforced release barrier until built. In an extended portal persist `report_id`, version, reviewer, decision, timestamp and comments; revisions create new draft versions and re-enter validation.

**Approval boundary:** Evidence-backed **internal alerts should be delivered immediately** after validation; approval is required for committed mitigation decisions, changing baseline dates and stakeholder-facing reports—not for sending a warning to its owner.

## 10. Production proactive monitoring and escalation (CR-001 acceptance required)

### 10.1 Business objective and scope control

PRD v1.1 explicitly required on-demand milestone tracking. Scheduled background monitoring, active notification and escalation go beyond this baseline. Submit CR-001 for PM/TPM and sponsor approval. If approved, assign priority and funding separately from capstone P0 tests. **Do not claim that the workflow prevents 100% of missed milestones.**

### 10.2 Monitoring design

A no-code scheduler (Make.com or approved equivalent) runs a configured scenario on approved days/times, or responds to supported Jira/Trello events. It obtains a **fresh** authorized source or approved updated snapshot, checks data freshness/schema, applies deterministic blocked/overdue/due-soon/dependency rules, invokes Langflow Milestone Tracker for narrative, upserts alert records, and notifies owners of new or materially changed conditions.

A repeated scan of the same uploaded static CSV will not reveal new project developments. A production-like pilot requires an agreed refresh SLA and data-source owner. Make must call a reachable authenticated Langflow API URL; local Desktop `localhost` alone is not reachable from its cloud service.

### 10.3 Proposed alert rule table

| ID | Condition | Output | Policy status |
|---|---|---|---|
| MR-01 | Explicit BLOCKED task | High issue alert | Proposed |
| MR-02 | Incomplete task after due date | High overdue alert | Proposed |
| MR-03 | Milestone ≤7 days away with documented blocked prerequisite | High milestone-at-risk alert | Proposed |
| MR-04 | Milestone ≤14 days away with documented incomplete prerequisite | Medium watch / investigate | Proposed |
| MR-05 | Incomplete task due in ≤3 days | Low watch only | Proposed |
| MR-06 | Source stale, missing or invalid | Data-quality warning; no false forecast | Proposed |
| MR-07 | Critical alert not acknowledged/mitigated by threshold | Escalate to configured owner | Proposed |

Thresholds and levels must be approved; no such values are supplied by the capstone.

### 10.4 Alert registry and states

Minimum fields: `alert_id`, `project_id`, `source_item_id`, `alert_type`, `severity`, `source_version`, `detected_at`, `last_seen_at`, `assigned_owner`, `notification_status`, `acknowledged_at`, `mitigation_owner`, `mitigation_due`, `status`, `escalated_at`, and relevant source evidence. Alert uniqueness key: `(project_id, source_item_id, alert_type)`; an alert can be updated or reopened on meaningful change.

States: `DETECTED → NOTIFIED → ACKNOWLEDGED → ACTION_ASSIGNED → MONITORING → RESOLVED`; optional transition to `ESCALATED` when thresholds lapse. Distinguish delivery failure from user nonacknowledgement. Do not spam unchanged alerts at each schedule run.

### 10.5 Internal notifications and approval

Internal PM/TPM notification is automatic after validated detection. Human acknowledges and owns the action. PM/TPM must approve external/customer reports or modified commitments. Notifications should link to the specific project item and include evidence, as-of timestamp, severity and next action.

### 10.6 Proposed operational targets and evidence

Targets subject to business approval: ≥99% scheduled run completion, 100% detection of seeded baseline blockers, ≤15 minutes to internal notification after a successful monitoring run, ≤4 business hours for critical acknowledgement. Track sources refreshed on time, false-positive rate, missed issues, duplicates suppressed, delivery success, escalation correctness and median time to acknowledgement. These are design targets, not guarantees or results.

## 11. Security, privacy, reliability and governance

- Protect model, LangWatch, Jira/Trello, Make and notifier credentials with their supported secret settings; never commit secrets or embed in report content.
- Treat all uploaded/connected project text as **untrusted data**, not instructions to override system prompts or permissions. Limit external tool privileges.
- Scope records by project; authorize connectors; begin with read-only project/task access. Verify permitted processing with OpenAI/LangWatch/Make and minimize trace contents.
- Return controlled statuses for absent/malformed files, invalid sprint, unavailable model or tools, unknown status, and missing dependencies.
- Bound model retries and scheduler retries; use idempotent alert keys, delivery retry/backoff and duplicate prevention.
- Preserve audit evidence for alert acknowledgement, report approval and escalation where a persistent store exists.
- Incident mode: source freshness unknown or connector outage → notify data owner, mark alert evaluation as degraded and do not assert project health as green.

## 12. Observability, evaluation and success measures

LangWatch traces model/tool invocations, model identity, latency, prompt version, tokens, cost and evaluation where supported. **Separately** instrument monitoring-run status, source refresh timestamps, detection events, alert-upsert result, notification delivery, acknowledgement and escalation. An LLM trace alone does not prove the scheduler ran or email arrived.

Execute capstone **T1–T12** and record test ID, workflow/model/dataset version, input, expected/actual output, pass/fail, trace ref, defect, owner and remediation. New CR-001 tests should include: status transition to BLOCKED, due-soon watch, stale source, duplicate suppression, notification failure/retry, unacknowledged escalation, mitigation resolution and approval of external updates. Clearly label test-data snapshots as synthetic if created for extension testing.

Economic evaluation: per-plan and per-report model cost from recorded input/output tokens and current model pricing; separately account for scheduler, connector, storage and messaging charges. Business validation includes actual review-inclusive plan/report preparation times and PM confidence, not merely successful text generation.

## 13. Implementation plan, dependencies and gates

| Gate | Demonstration required | Blocking status |
|---|---|---|
| G1 Environment | Langflow, model connection, flow export/import | Capstone |
| G2 Data readiness | Actual file schemas, 25 tasks, risk and timeline fields inspected | Capstone |
| G3 Deterministic processing | Sprint filter, exact status counts, T024, date window with no unverified LLM arithmetic | Capstone — critical |
| G4 Specialist flows | All four flows return grounded, structured results | Capstone |
| G5 Routing / review | Tool dispatch works; manual approval process documented | Capstone |
| G6 Eval/observability | LangWatch trace, T1–T12, cost/quality findings | Capstone |
| G7 Release | Validated JSON in GitHub, diagrams, screenshots, README and rollback smoke test | Capstone |
| G8 Change approval | Approve FR-019–FR-026, thresholds, ownership and data refresh agreement | CR-001 only |
| G9 Monitoring POC | Scheduler → fresh data → rule → Langflow → registry → internal alert | CR-001 only |
| G10 Operations pilot | Dedup, acknowledgement, escalation, audit and security tests | CR-001 only |

### 13.1 No-code feasibility decision

Use Langflow native Read File / structured data and configured components if they demonstrably implement exact operations. Otherwise seek approval for a **no-code spreadsheet or automation layer** rather than pretend the LLM can provide deterministic calculations. The architecture is conditional until G3 proves that mechanism.

### 13.2 Reproduction and rollback

Version `flows/Mira_00_Orchestrator.json` through `flows/Mira_04_Milestone_Tracker.json`, prompt configs, input test fixtures (only approved public/synthetic data), schema mapping and runbook. Export from Langflow, inspect for secrets, commit via feature branch/PR. GitHub commits **do not synchronize** deployed Langflow automatically. Restore last accepted JSON, reconnect environment-specific credentials/files and rerun smoke tests.

## 14. Requirements traceability

| PRD ID | Architecture realization | Evidence |
|---|---|---|
| FR-001 | UI file ingestion + data validation | Dataset-readiness tests |
| FR-002 | Orchestrator/Run Flow tool contract | Routing tests |
| FR-003/004 | Planner + insufficient detail | T1, T2, T9 |
| FR-005/006 | Risk Assessor + source/ranking rule | T3, T4, T7 |
| FR-007/008 | Status Reporter + exact aggregation | T5, T6, T10 |
| FR-009/010 | On-demand Tracker + date/dependency logic | T8, T11 |
| FR-011 | Status Reporter stakeholder draft | T12 |
| FR-012/013 | Schema + source validation and explicit failure | Cross-cutting |
| FR-014/015 | LangWatch and baseline test report | All T1–T12 |
| FR-016 | Markdown/JSON output | Contract checks |
| FR-017 | PM/TPM manual review before sharing | Approval evidence |
| FR-018 | Authorized Jira/Trello connector | Future, not baseline |
| **Proposed FR-019–026** | Scheduled monitor, rule engine, notification, registry, escalation and lifecycle evidence | **CR-001 tests only after approval** |

## 15. Architecture decision records

| ADR | Decision | State |
|---|---|---|
| ADR-001 | Langflow visual specialist flows | Baseline selected |
| ADR-002 | Router/Dispatcher via Run Flow | Baseline selected; quality test pending |
| ADR-003 | GPT-4o-mini initially | Baseline selected |
| ADR-004 | Direct file retrieval; no vector DB for tiny dataset | Baseline selected |
| ADR-005 | Deterministic operations required; no-code mechanism | Mandatory; feasibility pending |
| ADR-006 | LangWatch for LLM observability | Integration pending |
| ADR-007 | GitHub JSON export-based source control | Selected; manual MVP |
| ADR-008 | Human approval of report release | Manual MVP; automation optional |
| ADR-009 | Project-scoped authoritative data snapshot | Baseline selected |
| ADR-010 | On-demand milestone detection | Baseline selected |
| **ADR-011** | **Make.com or equivalent scheduler, with authenticated hosted Langflow integration** | **Proposed CR-001** |
| **ADR-012** | **Alert registry and lifecycle** | **Proposed CR-001** |
| **ADR-013** | **Internal alerts do not wait for approval; external reports do** | **Proposed policy** |

## 16. Risks, open decisions and sign-off

| ID | Critical open issue / risk | Owner / resolution |
|---|---|---|
| OT-01 | Actual CSV schema, priority fields and dependencies unknown | PM/data owner; inspect originals |
| OT-02 | No-code exact count and date rules unproven | Technical owner; G3 spike |
| OT-03 | Source-to-output factual checker may require manual evidence | QA/architect; specify enforceable checks |
| OT-04 | LangWatch/runtime compatibility and PII content | Technical owner/security; integration test |
| OT-05 | Cloud scheduler cannot access local-only Langflow | Technical owner; secure endpoint strategy |
| OT-06 | CSV snapshot may be stale | PM/data owner; refresh SLA / Jira/Trello alternative |
| OT-07 | Alert policy, owner mapping and escalation timing not approved | PM/sponsor; CR-001 decision |
| OT-08 | Notification failure, duplicates or privacy leakage | Technical/security; lifecycle test |
| OT-09 | True UI approval and persistent audit not yet implemented | Product owner; manual gate until built |
| OT-10 | Multi-project portal and RBAC scope could expand effort | PM/CTO; defer pending business case |

**Architecture disposition:** Conditionally approved for **baseline capstone proof of concept**. **CR-001 remains proposed**, not part of approved PRD v1.1 or delivered product. Gate G3 (exact no-code calculations) and fresh-data strategy are critical before promising reliable alerting. Nothing in this TDD guarantees that every future late milestone will be detected or prevented.

### Architecture review / sign-off

| Role | Review responsibility | Status |
|---|---|---|
| Product Manager | Goals, scope, operational thresholds, KPIs | Pending |
| Technical Program Manager | Dependencies, gates, risk and schedule | Pending |
| Enterprise Architect / CTO | No-code feasibility, security, integration and observability | Conditional / pending evidence |
| QA / Evaluation Owner | Baseline assertions, synthetic monitoring tests | Pending |

## 17. Explicit T1–T12 baseline test coverage and acceptance assertions

The architecture **covers the required test scenarios by design**, not by demonstrated execution. A test is **PASS** only after the actual supplied files are loaded (where applicable), the expected behavior is observed, and the result is recorded. Some vague-input tests must deliberately run **without** default ABCDE data injected into their context.

| Test | Flow / capability | Required input and isolation | Pass criteria | Failure guard |
|---|---|---|---|---|
| **T1** – Detailed ABCDE project plan | Planner | project_description.txt + project_timeline.csv | Use actual objectives, six-month duration, phases, activities, deliverables and approved milestones; label unsupported proposals | No invented goals, phase names or dates |
| **T2** – “We want to build a chatbot” | Planner | Only one-sentence user request; do not silently inject ABCDE context | INSUFFICIENT_DATA; request scope, timeline and team size | No full implementation plan |
| **T3** – ABCDE risk assessment | Risk | Project description + project_risks.csv | Categorized logistics/AI-relevant risks with documented impacts and mitigations; source IDs | No unrelated generic risks presented as source facts |
| **T4** – “New project starting soon” | Risk | Only vague request; no project risk file specified | INSUFFICIENT_DATA; ask for project details | No invented project-specific risk register |
| **T5** – Sprint 3 weekly status | Status | sample_task_board.csv filtered exactly to Sprint 3 | Real task names and status groups; accurate Sprint 3 counts | No cross-sprint leakage or invented tasks |
| **T6** – “Things are going fine” | Status | No task board provided for that request | Explicitly state no task data; request board/sprint | No fabricated progress, tasks or percentages |
| **T7** – Top 3 ABCDE risks | Risk | project_risks.csv | Return three actual risk records with IDs and transparent source ranking or declared derived priority | No newly fabricated risks; do not claim unsupported ranking certainty |
| **T8** – Blocked/at-risk tasks | Tracker | sample_task_board.csv + as-of date | Identify T024 Security review as BLOCKED; check other deadlines against actual statuses | Do not invent due dates, dependencies or blocker cause |
| **T9** – Two-week project, no further detail | Planner | Only two-week duration | INSUFFICIENT_DATA; ask for scope, goals and deliverables | No detailed plan based on duration alone |
| **T10** – Count current tasks | Status | Full sample_task_board.csv, no sprint filtering | 25 total: 3 Done, 3 In Progress, 18 To Do, 1 Blocked; verify against CSV | Counts reconcile; no unknown statuses silently coerced |
| **T11** – Upcoming milestones in 2 weeks | Tracker | project_timeline.csv + explicit as-of date | Actual milestone names and dates within inclusive [as-of, as-of + 14 days] | No invented milestones; empty truthful result if none |
| **T12** – Sprint 2 stakeholder email | Status / email mode | Only Sprint 2 tasks from sample_task_board.csv | Professional draft with Sprint 2-only facts, issues and next actions when documented | No Sprint 3+ tasks; no automatic sending |

### 17.1 Test execution controls

- **Dataset fixtures:** Preserve the original five supplied files as immutable baseline inputs. Do not substitute demonstration examples for test evidence. The uploaded assignment text lists the files but does not contain their complete CSV records.
- **No-data isolation:** For T2, T4, T6 and T9, suppress automatic use of ABCDE files unless explicitly referenced by the user. Otherwise a vague request could incorrectly appear sufficiently grounded.
- **Deterministic assertions:** T5 and T10 require programmatic/no-code exact aggregations. T8 and T11 require explicit as-of-date logic. A plausible-looking LLM answer is not enough.
- **Risk priority ambiguity:** If `project_risks.csv` provides no authoritative ranking, define and document a transparent derived ranking with a fixed tie-breaker before accepting T7. Never assert that an arbitrary top three are source-ranked.
- **Temporal fixture:** T11 must use a specified as-of date that matches the test fixture; a changing system date will make the test nonreproducible.
- **Source provenance:** Preserve identifiers and source names for tasks, risks, timeline rows and planning claims; flag any unavailable field.
- **Approval separation:** T12 tests a **draft**, not sending. PM approval occurs before distribution and is independently evidenced.
- **Execution ledger:** Log test ID, prompt, data version, flow/prompt/model version, actual output, checks passed, failed assertions, trace link, defect and reviewer decision.

### 17.2 Coverage and exit criteria

| Measure | Expected for capstone | Current state |
|---|---|---|
| Scenarios specified | 12/12 mapped | Design coverage only |
| Tests run | 12 executions recorded | Not yet evidenced |
| Passed | Target 12/12 | Unknown until run |
| Core capabilities | Planner, Risk, Status | Designed; build pending |
| Extended capability | Milestone Tracker | Designed; build pending |
| Additional proactive monitoring | CR-001-specific tests | Separate, not a substitute for T1–T12 |

**Gate G6 cannot pass** without a baseline run ledger, actual test outputs and validation/observability evidence. An unrun test must be recorded as **NOT RUN**, never PASS.

## 18. Version change log

| Version | Preserved | Added / clarified |
|---|---|---|
| 1.2 | Router, four specialists, grounding, contracts, security, observability, release gating | Existing architectural baseline |
| 1.3 | All v1.2 capability and governance areas | CR-001 proactive monitoring; source freshness; rules; alert registry, notifications, escalation; approval boundary; new tests and cost/security controls |
| 1.4 | All v1.3 design content | Explicit twelve-case acceptance matrix, no-data isolation, temporal fixtures, execution ledger, and coverage status |

**Next artifacts:** `docs/Architecture.md` (v1.5), change request `docs/CR-001_Proactive_Milestone_Monitoring.md` (recommended), and no-code component POC evidence.


---

## 19. Production reference deployment architecture (normative for release)

**Logical deployment:** PM/TPM browser → authenticated web portal (project selector, file upload, reports and approval) → gateway/WAF → authorization + project registry → ingestion service/object store → validated project database → durable job queue → privately deployed Langflow workers → LLM provider. A monitoring scheduler and connector workers periodically import approved task data; deterministic rule service evaluates task/milestone conditions; notification worker dispatches events from a durable outbox. Audit events are stored independently of model traces. See [Architecture.md](Architecture.md) for updated physical and sequence diagrams.

- **Identity:** enterprise IdP using OIDC/SSO, MFA enforcement, authenticated sessions; service-to-service identity separate from human identities. Production app must authorize each `project_id` lookup and attachment access, not trust an unvalidated project ID supplied by the browser. Langflow built-in auth is **not** a substitute for app RBAC: Langflow's RBAC requires a registered authorization plugin. Run Langflow behind private ingress, not directly exposed to internet. <https://docs.langflow.org/authentication-overview>
- **Application services:** project registry; RBAC enforcement; upload validation; workflow execution facade; persisted report version/approval workflow; alert lifecycle API.
- **Storage:** encrypted object storage for uploaded files with malware scanning and quarantine; managed relational database for users, project memberships, source manifests, tasks, milestones, risks, job state, approvals and alerts; encrypted immutable/controlled audit sink. Object and database backups with restore verification.
- **Execution:** queue-based jobs with idempotency keys, bounded concurrency, retries/dead-letter queue, timeouts and circuit breakers; pinned Langflow flows/models/prompts and private execution endpoints.
- **Integrations:** Jira/Trello only using least-privilege OAuth/read scopes; enforce per-project mapping, webhook signature validation where supported, incremental sync and reconciliation. Prefer approved providers over unreviewed automation accounts.
- **Network:** TLS on all hops; secrets manager, key rotation; outbound LLM/observability egress allowlist; secure ingress with rate limits and file-size limits.

### 19.1 Environment separation

Use isolated **development, staging and production** environments with separate credentials, identities, databases, storage, queues, alert channels and telemetry. Only synthetic/sanitized data in development. Infrastructure changes are reviewed by platform/security owners. GitHub is source control, **not** the runtime database or the file-storage backend.

### 19.2 Production data path and projects

`Select project → authorize membership → upload/import → scan → schema validate → persist versioned snapshot → make snapshot immutable for job → execute specialist → reconcile source facts → save draft → reviewer decision → approved export`.

Each record and blob carries `tenant_id`, `project_id`, `source_snapshot_id`, `ingested_at`, and provenance reference. All queries must be scoped using verified identity claims and authorization. Multi-project isolation tests must include attempts to alter project IDs, filenames and blob identifiers.

### 19.3 No-code boundary

Langflow remains the **no-code agent designer**, but a production deployment can legitimately require managed gateway, identity, object storage, database, queue and deterministic processors configured through approved managed platforms. If a capability cannot be enforced reliably without code, architectural integrity takes precedence over a strict no-code constraint; seek formal approval rather than simulating security/data correctness in prompts.

## 20. Production data model and event contracts

| Entity | Required fields | Data controls |
|---|---|---|
| `Project` | tenant_id, project_id, name, owner, status | Unique ID; membership-scoped access |
| `ProjectMembership` | tenant_id, project_id, user_id, role | RBAC policy, least privilege |
| `SourceSnapshot` | snapshot_id, project_id, source_type, version/hash, ingested_at, freshness_status | Immutable approved version for each run |
| `Task` | project_id, task_id, sprint, status, due_at, assignee, snapshot_id | Unique per project/snapshot; deterministic normalized states |
| `Milestone` | project_id, milestone_id, due_at, linked_dependencies, snapshot_id | Dependency optional and explicitly known/unknown |
| `Report` | report_id, project_id, source_snapshot_id, flow_version, draft_hash, state | Revision tracking; approved report immutable |
| `Approval` | approval_id, report_id, reviewer_id, decision, reason, timestamp | Durable audit; separation of duties |
| `Alert` | alert_id, project_id, source_ref, rule_version, severity, state, owner, first_seen, last_seen | Unique fingerprint + state transitions |
| `DeliveryEvent` | event_id, alert_id/report_id, channel, recipient, delivery_status, retry_count | Idempotent dispatch and delivery evidence |

**Example event semantics:** `DataSnapshotValidated`, `ReportDrafted`, `ReportApproved`, `ReportRejected`, `RiskDetected`, `AlertAcknowledged`, `AlertEscalated`, `AlertResolved`, `NotificationDelivered`, `DataSyncFailed`. Every event includes unique ID, tenant/project, event time, actor/service and correlation ID. Payloads minimize PII and confidential report content.

**Source conflict policy:** connectors/snapshot files have documented precedence; a conflict blocks automatic status publication until reconciled or explicitly disclosed. User uploads are not trusted instructions. Reject cross-project joins and unapproved file types. Define clock timezone as UTC storage plus project-local display, and use calendar days for defined business rules unless the policy explicitly states business days.

## 21. Production-grade approval and reporting workflow

States: `DRAFT → VALIDATING → PENDING_APPROVAL → APPROVED → PUBLISHED`, or `VALIDATION_FAILED`, `REJECTED → REVISED_DRAFT`, `EXPIRED`.

- Creator cannot self-approve high-impact reports where separation of duties is required; approval policy is configurable by audience/classification.
- Each approval captures immutable report content hash, source snapshot, reviewer identity, decision, comment and timestamp; edits invalidate previous approval.
- Reports exceeding a freshness threshold require refresh and re-review before publishing.
- Publishing/export endpoint enforces `APPROVED` and verified project membership on the **server**; hiding a UI button is insufficient.
- Alerts about internal blockers can notify responsible PMs automatically without preapproval; schedule changes and external publication still require authorized approval.

## 22. Proactive monitoring service design

**Triggers:** scheduler (proposed every business day, adjustable per project) and verified connector change events. Detect stale connectors, missed schedules and invalid snapshots as first-class alerts. Evaluate all projects with scoped credentials, not a single combined corpus.

**Pipeline:** retrieve/checkpoint source → validate schema/freshness → stage snapshot atomically → compute exact rule matches → deduplicate `(tenant, project, source_ref, rule, relevant_state)` → persist alert before dispatch → enqueue outbox notification → record provider receipt → monitor acknowledgement → escalate based on configured business-hours SLA → close only with evidence or authorized override.

**Controls:** idempotent processing; at-least-once event delivery with deduplication; backoff and dead-letter queues; schedule-miss alarms; notification opt-outs only where policy permits; owner fallback for unassigned issues; alarm-fatigue suppression; no false claim of guaranteed prevention. Proposed operational targets from CR-001 (99% scheduled checks, 15-minute post-run notification, 4-hour critical acknowledgement) must be approved and separately measured. Protect contacts and notification destinations.

## 23. Security, privacy and abuse-resilience controls

- Threat model: prompt injection via uploaded documents, connector API data poisoning, malicious file upload, cross-project/tenant access, SSRF through tools, agent privilege escalation, exposed webhook/API keys, report approval bypass, replayed events, secrets in LangWatch traces, notification abuse.
- Controls: file type/size allowlist, malware scan, upload quarantine, sanitized extraction, no arbitrary network-capable tools, allowlisted hosts, egress proxy, minimum tool permissions, input/output size ceilings, sensitive-data redaction, policy-based retention and delete flows, encrypted transport/storage, credential rotation, log access controls and audit monitoring.
- Auth: corporate SSO/OIDC at application tier, MFA, role assignment, scoped service identities; Langflow backend behind authenticated private gateway. Disable auto-login, set explicit secret key, retain webhook authentication, and avoid a shared superuser key for end-user execution. <https://docs.langflow.org/api-keys-and-authentication>
- Compliance: legal/security owner approves data classification, provider DPAs, regions, retention, deletion and incident reporting **before** real customer data ingestion. No blanket claim of GDPR/SOC 2 compliance is made by this design.
- Human-generated and AI-generated outputs are marked; audit includes model/flow version and source snapshot. Red-team prompts and malicious-upload fixtures are release-gating tests.

## 24. Reliability, scalability and recovery

**Proposed initial service objectives** (business approval required): request availability 99.9% monthly; interactive status report p95 ≤30 seconds under agreed normal load; scheduled monitor successful-run ≥99%; 100% high-severity alert persistence before sending; data backup RPO ≤1 hour; restore RTO ≤4 hours. Latency excludes user approval and external provider outages, which are separately measured.

- Multiple stateless app/worker replicas where supported; load balancing/health checks; managed relational database and queue with redundancy. Pin supported versions; test startup/health endpoints and graceful shutdown.
- Rate limits, per-tenant concurrency quotas, queue backpressure, LLM budget and circuit breaker prevent traffic spikes/cost runaway.
- Timeouts, bounded retries, DLQ and replay must preserve idempotency and never double-publish a report.
- Nightly backups plus point-in-time recovery where supported; quarterly restore drills; documented restore ownership, dependency order and failover exercises.
- LLM failure returns controlled error/draft pending rather than fabricated results; notification outage preserves outbox for later delivery.

## 25. Observability, audits and operations

Separate **LangWatch LLM traces** from **system metrics and security audit**. Instrument gateway latency, ingestion failures, snapshot age, queue depth, Langflow failure rates, tool routing, schema validation, alert dispatch latency, acknowledgement age, approval backlog, token cost and incident counts. Use correlation IDs end-to-end. Dashboards and paging thresholds require designated on-call owners.

LangWatch compatibility must be confirmed for the chosen runtime: documented default Langflow Docker images use Python 3.14 while the LangWatch package requires Python below 3.14; a compatible vetted image/runtime is necessary. <https://docs.langflow.org/integrations-langwatch>

Operational runbooks: missing scheduled run; stale Jira/Trello feed; wrong-project data exposure; provider outage; model output regression; surge in hallucinations; failed report approval; duplicate alert storm; credential compromise; backup restore. Define severity, response ownership and escalation contacts.

## 26. CI/CD, release engineering and governance

- Flow JSON, prompt versions, configs, tests and schema contracts committed to GitHub (no secrets). Build artifacts are immutable and versioned; deployment manifest references hashes.
- Pull request reviews, branch protection, required checks, artifact/secret scanning, environment approval, signed/pinned dependencies and automated deployment using authorized pipelines; not browser upload to production.
- Staging integration tests replay the 12 baseline cases and additional security/operational tests. Production promotion requires named owner approval, canary/limited rollout, health observation and rollback trigger thresholds.
- Separate config from code/flow exports; secrets managed per environment. Rollback covers flows **and** schema compatibility, prompt versions, data migrations and notification rule versions.
- Architectural decisions and exceptions tracked with owner, due date and expiration. Release review should include privacy, infosec, SRE/operations, PM/TPM and executive sponsor.

## 27. Production verification matrix (adds to T1–T12)

| ID | Production acceptance test | Required evidence |
|---|---|---|
| P01 | OIDC/SSO login, MFA and sign-out/session expiry | Identity/gateway test and screenshots |
| P02 | Unauthorized user cannot access another project via ID/blob changes | Negative authorization tests and audit entries |
| P03 | Oversize/malicious/malformed file rejected or quarantined | Upload security and error logs |
| P04 | Stale/missing Jira/Trello data blocks unsupported real-time claim | Freshness test and warning |
| P05 | Scheduled and event-driven checks detect injected blocker | Rules and trace evidence |
| P06 | Duplicate trigger never duplicates alert or external notification | Replay/idempotency test |
| P07 | Notification provider outage retries without losing alert | Outbox/DLQ evidence |
| P08 | Unauthorized/changed report cannot bypass approval | API negative tests and immutable approval log |
| P09 | Prompt-injection attempt cannot extract secrets or trigger tools | Adversarial evaluation evidence |
| P10 | Secrets absent from JSON, logs and telemetry exports | Secret scan and redaction sample |
| P11 | Restore backup meets approved RPO/RTO | Timed recovery drill report |
| P12 | Production rollback restores approved working version | Staging rollback drill |
| P13 | Per-project quota prevents resource exhaustion | Load and rate-limit test |
| P14 | Incident alarm reaches on-call with documented runbook | Incident exercise |
| P15 | Full T1–T12 baseline still passes against pinned source snapshots | Regressions and LangWatch traces |

No production acceptance may be inferred from design documentation alone. All P-series gates must be exercised in a production-like staging environment and signed off.

## 28. Production responsibility and sign-off (RACI summary)

| Domain | Accountable owner | Required approval/evidence |
|---|---|---|
| Product and business KPIs | Product Owner / Nexora sponsor | Revised PRD v2.0 and policy thresholds |
| Identity, app authorization | Security + application owner | Threat model, test P01/P02 |
| Data lifecycle/retention | Client data owner + privacy | Classification, provider approvals, deletion test |
| Orchestration and output grounding | AI technical lead | T1–T12, grounded-eval results |
| Monitoring and alert policy | Delivery operations owner | Approved rulebook, P05–P07 |
| Reliability / incident operations | SRE/platform owner | SLOs, on-call, P11–P14 |
| Release approval | CTO delegate + Product Owner | Staging evidence and formal go/no-go |

**Architecture review disposition:** Production-target design documented; **NOT approved for production deployment** until PRD extension is authorized, selected platform implementation is proven, controls P01–P15 pass, and operational owners sign off.

## 29. Version change log and retained design

- **v1.1–v1.4:** Established the four Langflow specialists, shared data concepts, human review, proactive monitoring proposal, and T1–T12 baseline matrix.
- **v2.0:** Retains all prior functional flows and baseline tests, promotes approved-on-release monitoring to the production target, and adds secured project selector, enterprise identity/authorization, persistent state, secure data ingestion, distributed execution, high availability/recovery, audited approval, CI/CD, on-call operations and P01–P15 production gates.
- Earlier MVP descriptions in Sections 1–18 serve as **functional baseline and historical context** where they refer to MVP/CR-001; Sections 19–28 govern production implementation if a legacy statement conflicts. Before external distribution, reconcile any remaining historical wording in a future consolidated publication.
