# Mira – Solution Architecture (n8n Single-Platform Baseline)

**Version:** 2.2 – Source-Validated n8n-only Capstone / Production Target (separate)  
**Date:** October 25, 2026  
**Status:** Proposed implementation baseline; **not yet implemented or production certified**  
**Rebased from:** Original `Architecture.md` v2.0 and `Technical_Design_Document.md` v2.0  
**Requirements:** PRD v1.3 capstone functions (original v1.1 requirements preserved); separately proposed PRD v2.0 / CR-PROD-001 production extensions  
**Technical design:** [Technical_Design_Document.md](Technical_Design_Document.md) v2.2  
**Platform ADR:** ADR-014 – n8n-only selection (Langflow and hybrid retained as evaluated alternatives)  
**Primary implementation:** n8n workflows + approved OpenAI model + governed project data source + GitHub  

> **Reading guide:** Diagrams **P1–P2** are the **target-state production design** and include components not yet built. Diagrams **1–5** show the capstone logical workflow, data/approval handoffs, proactive extension, and T1–T12 evidence path. The v2.0 seven-diagram coverage is preserved while replacing Langflow/Make orchestration with n8n. This change does not approve production FR-019–FR-026 or claim a working system.

## Architecture decision summary — Why n8n instead of Langflow or hybrid?

| Candidate | Advantage | Mira-specific trade-off | Result |
|---|---|---|---|
| Langflow-only | AI-first graph development and agent tooling | Additional design effort for business integrations, scheduled workflows, approvals and state management | Not selected |
| Hybrid n8n + Langflow | Dedicated AI specialist platform plus business automation | Two runtimes; n8n-to-Langflow authentication, retries, failure paths, cross-platform logs and hosting; no automatic LLM token advantage | Contingency only |
| **n8n-only** | Single orchestration runtime for data, AI agents, integrations, schedules and notification gates | Must prove exact no-code processing, agent results and commercial/hosting controls | **Selected for capstone proof and production-target design** |

**Expected benefit, not a measured claim:** simpler dependency graph and API surface; fewer operating platforms; deterministic source processing before AI; capacity to avoid redundant router calls. **Token cost depends on prompt size, calls, model, output and retries—not on platform branding.** Measured cost/accuracy/latency and platform edition features must be validated under G1–G7. Approval of ADR-014 remains pending.

## Source semantics and grounded agent contracts (approved input profile)

| Evidence domain | Canonical field contract | Capstone constraint |
|---|---|---|
| Description | `project_description.txt` | Four AI domains: forecasting, routes, inventory, customer support; 15% delivery-delay and 10% operating-cost aims belong to ABCDE's program, not Mira measured outcomes |
| Timeline | `phase,phase_name,start_week,end_week,key_activities,milestones_deliverables` | 8 phases / Weeks 1–24; **no calendar start date, milestone due dates or dependencies** |
| Risks | `risk_id,category,risk_challenge,impact,mitigation_strategy` | 10 registered risk IDs; **no numeric ranking**; T7 needs explicit selection criterion or clearly labeled illustrative risks |
| Tasks | `task_id,task_name,status,assignee,priority,sprint,due_date,labels,description` | 25 source rows; four exact statuses; T024 Blocked; Sprint 2 and Sprint 3 fixtures below |
| Evaluation | `eval_mira_inputs.txt` | Original prompts T1–T12 must remain unchanged; vague tests T2/T4/T6/T9 receive no automatic ABCDE context |

**Golden fixture:** full board Done 5, In Progress 3, Blocked 1, To Do 16; Sprint 2 = T004/T005 Done and T006 In Progress; Sprint 3 = T007/T025 In Progress and T008/T009 To Do; T024 = `Security review of AI infrastructure`, `Blocked`, due `2026-05-26`.

**Milestone evaluation:** T8 uses a supplied `as_of_date` against dated task records (not evidence of real-time delivery). T11 reads relative project Weeks 1–24 and requires an approved `project_start_date`/project-week mapping before calculating a two-week calendar window. Without that anchor, return clarification rather than fabricated upcoming calendar milestones. `AT_RISK` impact must not be inferred solely from a blocked task without an actual dependency.

**Assignment discrepancy:** The narrative says 3 Done, 3 In Progress, 1 Blocked and remaining To Do; its supplied task board instead has **5 / 3 / 1 / 16**. The CSV is the basis for the T10 *actual data* assertion; capture the discrepancy as CR-DATA-001 and obtain review approval. No changes are made to the original assignment or source files.

**Supporting sources:** The duplicated TXT, timeline PDF and risk DOCX are **reference sources**; CSV/TXT canonicals take precedence. No silent merging of altered risk text or invented milestone dates.

## Diagram P1 — Production deployment and trust boundaries (target state)

```mermaid
flowchart TB
  U["PM / TPM / Reviewer"] --> PORTAL["Authenticated Mira Portal<br/>Project selection | Upload | Review"]
  IDP["Enterprise IdP<br/>OIDC / SSO / MFA"] --> PORTAL
  PORTAL --> EDGE["API Gateway / WAF<br/>TLS | Rate limits | File size"]
  EDGE --> AUTH["Trusted application authorization<br/>Project membership and RBAC"]
  AUTH <--> REG[("Project registry and memberships")]
  AUTH --> INTAKE["Governed file / connector intake<br/>Scan | Schema check | Project scope"]
  FILE["User file upload"] --> INTAKE
  CONNECT["Jira / Trello<br/>Approved read-only integration"] --> INTAKE
  INTAKE --> OBJ[("Versioned encrypted source files")]
  INTAKE --> DB[("Validated project data<br/>Reports | Approvals | Alerts")]
  AUTH --> EXEC["Secured n8n workflows<br/>Business and AI orchestration"]
  EXEC <--> DB
  EXEC --> DISPATCH["n8n Router<br/>Switch / IF / Execute Sub-workflow"]
  DISPATCH --> P["Planner"]
  DISPATCH --> R["Risk Assessor"]
  DISPATCH --> S["Status Reporter"]
  DISPATCH --> M["Milestone Tracker"]
  P --> VALID["Source and schema assurance"]
  R --> VALID
  S --> VALID
  M --> VALID
  VALID --> DB
  DB --> GATE["Persisted authorization-aware<br/>approval and publication gate"]
  GATE --> PUB["Approved distribution / export"]
  TIMER["n8n Schedule Trigger<br/>or verified source event"] --> MON["n8n Monitoring workflow<br/>Freshness | Rules | Dedup"]
  MON <--> DB
  MON --> NOTICE["Notification provider / outbox<br/>Internal PM/TPM alert"]
  NOTICE --> U
  EXEC -.-> LLM["OpenAI approved pinned model"]
  EXEC -.-> OBS["n8n execution logs + evals<br/>External ops telemetry if configured"]
  MON -.-> OBS
  GATE -.-> AUD[("Independent security and decision audit")]
  AUTH -.-> AUD
  GH["GitHub reviewed n8n JSON<br/>Prompts | Tests | Release manifests"] -.-> EXEC
```

**Boundary note:** n8n workspace/project permissions govern **workflow editors**, not end-user tenant data. The production portal/data service must authorize each business project lookup. Queue/worker mode, high availability, enterprise Git source control and OIDC/SSO depend on the licensed n8n edition and deployment. Native workflow execution history is not an immutable audit register. P1 is **not a claim of deployed controls**.

## Diagram P2 — Production intake, report approval and milestone alert lifecycle

```mermaid
flowchart TD
  U["Authenticated PM / TPM"] --> PROJECT["Choose project and verify membership"]
  PROJECT --> SRC{"Approved source?"}
  SRC -->|Upload| UP["Scan + parse TXT / CSV / approved PDF"]
  SRC -->|Connector| API["Jira / Trello read-only sync"]
  UP --> CHECK["Validate and version snapshot"]
  API --> CHECK
  CHECK --> OK{"Complete, consistent and fresh?"}
  OK -->|No| ERR["Reject / quarantine / report data quality issue"]
  OK -->|Yes| SNAP[("Immutable project-scoped snapshot")]
  SNAP --> REQUEST["User requests plan / risk / status / tracking"]
  REQUEST --> ROUTER["n8n Router + Execute Sub-workflow"]
  ROUTER --> FACT["Rules and specialist narrative<br/>Deterministic facts first"]
  FACT --> VALID["Reconcile source refs and response schema"]
  VALID --> PASS{"Passed?"}
  PASS -->|No| FIX["Return validation issue or corrected new draft"]
  PASS -->|Yes| DRAFT[("Persist report DRAFT with hash")]
  DRAFT --> HUMAN{"Authorized PM/TPM decision"}
  HUMAN -->|Reject / request changes| FIX
  HUMAN -->|Approve| LOCK["Persist approval identity<br/>Snapshot and report hash"]
  LOCK --> PUB["Recheck gate and publish"]
  SNAP --> SCAN["Scheduled / event scan<br/>Rules only for every run"]
  SCAN --> CHANGE{"New or changed valid alert?"}
  CHANGE -->|No| RUNLOG["Log scan; no redundant AI call"]
  CHANGE -->|Yes| REGISTER[("Idempotent alert record + outbox")]
  REGISTER --> NOTIFY["Notify assigned PM without prior report approval"]
  NOTIFY --> ACK{"Acknowledged within policy?"}
  ACK -->|Yes| ACTION["Assign mitigation and recheck"]
  ACK -->|No| ESC["Escalate by approved policy"]
  ACTION --> SCAN
  ESC --> SCAN
```

**Publication vs. detection:** report sending and baseline-changing actions require verified authorization; evidence-backed internal notices do not wait for report approval. Unchanged or stale snapshots do not prove new project developments. Approval must be persisted and enforced at trusted application/data boundary—not only in a UI button or an unsecured n8n form.

---

## Diagram 1 — Capstone logical n8n architecture and four specialist agents

```mermaid
flowchart TB
  U["PM / TPM / Tester"] --> FORM["Mira input<br/>n8n Form Trigger or test webhook"]
  FORM --> SOURCE["Approved TXT / CSV upload<br/>Fixed ABCDE project in capstone"]
  SOURCE --> EXTRACT["Extract From File + Edit Fields<br/>Parse and normalize"]
  EXTRACT --> QUALITY{"Schema, project ID, task IDs,<br/>status and dates valid?"}
  QUALITY -->|No| FAIL["VALIDATION_ERROR / request corrections"]
  QUALITY -->|Yes| SNAP["Versioned project snapshot + provenance"]
  FORM --> REQUEST["Request ID + user intent + scope"]
  SNAP --> ROUTE["n8n Router / Dispatcher<br/>Switch / IF based on intent"]
  REQUEST --> ROUTE
  ROUTE --> SUFF{"Sufficient evidence?"}
  SUFF -->|No| ASK["INSUFFICIENT_DATA<br/>Clarifying questions; no AI invention"]
  SUFF -->|Yes| PICK{"Choose specialist sub-workflow"}
  PICK --> P["Mira_01_Planner"]
  PICK --> R["Mira_02_Risk_Assessor"]
  PICK --> S["Mira_03_Status_Reporter"]
  PICK --> M["Mira_04_Milestone_Tracker"]
  SNAP --> CALC["Deterministic rules<br/>Filter | Summarize | Date and Time"]
  CALC --> S
  CALC --> M
  SNAP --> P
  SNAP --> R
  P --> CHECK["Evidence and output assurance"]
  R --> CHECK
  S --> CHECK
  M --> CHECK
  CHECK --> DRAFT["Status/result<br/>Evidence | Warnings | Draft"]
  DRAFT --> HITL{"PM/TPM review"}
  HITL -->|Approved| REPORT["Approved export / authorized share"]
  HITL -->|Needs revision| REV["New draft + revalidate"]
  REV --> CHECK
  HITL -->|Rejected| HOLD["Remain unpublished"]
  LLM["Approved OpenAI model"] -.-> P
  LLM -.-> R
  LLM -.-> S
  LLM -.-> M
  OBS["n8n executions + test ledger<br/>Model usage where available"] -.-> ROUTE
  OBS -.-> CHECK
  GIT["GitHub<br/>Exports / ADR / TDD / T1–T12"] -.-> ROUTE
```

**Routing strategy:** deterministic routing for known intents avoids an extra model token charge. An optional AI classifier handles genuinely ambiguous requests only; it must not retrieve ABCDE files automatically during negative tests T2/T4/T6/T9. Agent separation is by reusable workflow and contract, not necessarily multiple interacting autonomous LLMs.

## Diagram 2 — MIRA-001 source ingestion, validated aggregation and draft report

```mermaid
flowchart TD
  BEGIN["n8n Form Trigger: project_id + task_board CSV + optional sprint"] --> BIN["Extract From File: CSV"]
  BIN --> MAP["Exact source fields: task_id, task_name, status, sprint, due_date"]
  MAP --> CHECK{"Schema, dates, statuses and unique IDs valid?"}
  CHECK -->|No| REJECT["Validation error: field and record details; no LLM call"]
  CHECK -->|Yes| RAW["Validated rows: arbitrary valid project size"]
  RAW --> CT["Count all valid task IDs"]
  RAW --> AGG["Summarize by source status"]
  RAW --> BLK["Find Blocked rows; preserve source T024 title"]
  RAW --> SPRINT["Filter requested sprint before summary"]
  SPRINT --> SPRINTCOUNT["Count sprint-scoped source statuses"]
  CT --> RECON["Reconcile group totals with valid source count"]
  AGG --> RECON
  BLK --> RECON
  SPRINTCOUNT --> RECON
  RECON --> FIXTURE{"MIRA-001 test fixture asserted?"}
  FIXTURE -->|Yes| TEST["Assert 25 and 5/3/1/16; T024; Sprint 2/3 golden records"]
  TEST --> PASS{"Assertions passed?"}
  PASS -->|No| REJECT
  PASS -->|Yes| FACTS["Validated source facts with snapshot metadata"]
  FIXTURE -->|No| FACTS
  FACTS --> STATUS["n8n Status Reporter sub-workflow: AI narrative only"]
  STATUS --> VERIFY["Output validation: no invented records or cross-sprint leakage"]
  VERIFY --> DRAFT["DRAFT report"]
  DRAFT --> HUMAN{"PM/TPM review"}
  HUMAN -->|Approve| SHARE["Authorized manual share or export"]
  HUMAN -->|Revise or reject| HOLD["Hold; no automatic external send"]
```

**Key distinction:** The exact **25 / 5 / 3 / 1 / 16** check is a capstone **test assertion**, not a reusable business rule. The task board is a historical static snapshot, not a live report. n8n execution evidence is pending; no claim that these checks are already configured or deployed.

### Capstone MVP vs. production boundary

| Component | Capstone | Production target |
|---|---|---|
| Input surface | n8n Form Trigger; approved synthetic/sample data | Authenticated portal and governed API |
| Business project selection | Fixed `ABCDE-AI-001` | Authorized multi-project registry + data-scoped RBAC |
| Source data | Original task/timeline/risk files | Approved read-only Jira/Trello with scheduled refresh |
| Rule execution | n8n visual nodes, G3 proof | Governed durable data/rule services if needed |
| Approval | Documented PM/TPM manual review | Secure persistent workflow with authorization |
| Monitoring | On-demand Tracker | Approved scheduled/event detection, registry, dedup and escalation |
| Source control | Manual n8n JSON export, PR | Approved promotion CI/CD according to licensed edition |

## Diagram 3 — Proactive milestone monitoring and alert lifecycle (proposed CR-001)

```mermaid
flowchart TB
  SCHEDULE["n8n Schedule Trigger<br/>approved project-local policy"] --> IMPORT["Read-only Jira/Trello sync or fresh validated snapshot"]
  EVENT["Verified source event"] --> IMPORT
  IMPORT --> FRESH{"Source valid, complete and fresh?"}
  FRESH -->|No| DQ["Data quality alert<br/>No unsupported green status"]
  FRESH -->|Yes| RULE["Deterministic rule engine<br/>Blocked | Overdue | Due-soon | Dependencies"]
  RULE --> CHANGE{"New / materially changed alert?"}
  CHANGE -->|No| LOG["Log scan and suppress duplicates<br/>Skip AI call"]
  CHANGE -->|Yes| WRITE["Persist alert fingerprint and owner"]
  WRITE --> NARR["Optional Milestone Tracker<br/>Grounded AI explanation"]
  NARR --> OUTBOX["Durable notification intent / outbox"]
  OUTBOX --> SEND["Approved email / Teams / Slack node"]
  SEND --> ACK{"PM/TPM acknowledgement?"}
  ACK -->|Yes| ACTION["Mitigation owner / due date"]
  ACK -->|No / SLA breach| ESC["Escalation to configured owner"]
  ACTION --> NEXT["Recheck next fresh run"]
  ESC --> NEXT
  NEXT --> RULE
  DQ --> OPS["Ops monitor source freshness"]
  LOG --> OPS
  GITHUB["Versioned rulebook / n8n JSON / evidence"] -.-> RULE
```

**State:** `DETECTED → NOTIFIED → ACKNOWLEDGED → ACTION_ASSIGNED → MONITORING → RESOLVED`, with `ESCALATED` when policy dictates. Dedup fingerprint keyed by tenant/project/source-item/rule and relevant status transition; source freshness/owner mapping must be approved. Rule checks need not incur tokens; use OpenAI only for new or changed alerts when a narrative adds value. **FR-019–FR-026 are not capstone P0 features until authorized.**

## Diagram 4 — Human-in-the-loop draft review and publish gate

```mermaid
flowchart TD
  A["Validated project-scoped facts"] --> B["Specialist generates report draft"]
  B --> C["Verify schema / task IDs / counts / dates"]
  C --> D{"Assurance passed?"}
  D -->|No| FAIL["Validation error / no distribution"]
  D -->|Yes| STORE["Persist immutable draft version, hash and snapshot"]
  STORE --> REVIEW["PM/TPM authenticated review<br/>n8n Wait / approved interaction"]
  REVIEW --> DEC{"Reviewer decision"}
  DEC -->|Approve| AUTH["Verify role, draft hash, freshness and reviewer identity"]
  AUTH --> GATE{"Publishing preconditions met?"}
  GATE -->|No| HOLD["Hold + audit denial"]
  GATE -->|Yes| PUB["Record approval + authorized distribution"]
  DEC -->|Revise| FIX["Capture reviewer comments; create new draft"]
  FIX --> C
  DEC -->|Reject / hold| HOLD
```

**Capstone implementation caution:** manual documented review is acceptable; no automatic publish gate should be claimed until built. For production, n8n Wait does not independently authenticate business users, authorize project membership or create immutable approvals; a trusted portal/data service and store enforce those controls. Internal issue alerts remain outside the stakeholder-report approval gate.

## Diagram 5 — T1–T12 regression and evaluation evidence

**Observability acceptance:** The capstone requires *connecting* the workflow to an evaluation/observability tool and capturing actual trace/screenshots. n8n execution logs may accompany this evidence but do not, by themselves, establish a verified external or integrated evaluation-tool connection. Select and validate a compatible n8n Evaluation feature or dedicated tool at G1/G6; the data-flow diagram represents a **design**, not proof of integration.



```mermaid
flowchart TD
  CASES["12 original capstone test prompts"] --> ISOLATE["Pin dataset snapshot / control empty context"]
  ISOLATE --> CLASS{"Route by expected intent"}
  CLASS -->|T1, T2, T9| PLAN["n8n Planner sub-workflow"]
  CLASS -->|T3, T4, T7| RISK["n8n Risk Assessor sub-workflow"]
  CLASS -->|T5, T6, T10, T12| STATUS["n8n Status Reporter sub-workflow"]
  CLASS -->|T8, T11| MILE["n8n Milestone Tracker sub-workflow"]
  PLAN --> ASSERT["Facts, sufficiency and output schema assertions"]
  RISK --> ASSERT
  STATUS --> ASSERT
  MILE --> ASSERT
  ASSERT --> EXACT["Exact IDs, status counts, date windows,<br/>sprint isolation and no invented tasks"]
  EXACT --> PASS{"All assertions satisfied?"}
  PASS -->|Yes| YES["PASS with execution ID, source/model/workflow version"]
  PASS -->|No| NO["FAIL / defect / remediation"]
  NO --> RERUN["Replay pinned fixture"]
  RERUN --> ASSERT
  YES --> EVID["GitHub T1–T12 ledger<br/>n8n executions / approved evaluations"]
```

### Traceability for all twelve baseline cases

| Test | Flow / capability | Required input and isolation | Pass criteria | Failure guard |
|---|---|---|---|---|
| **T1** – Detailed ABCDE project plan | Planner | project_description.txt + project_timeline.csv | Use actual objectives, six-month duration, phases, activities, deliverables and approved milestones; label unsupported proposals | No invented goals, phase names or dates |
| **T2** – “We want to build a chatbot” | Planner | Only one-sentence user request; do not silently inject ABCDE context | INSUFFICIENT_DATA; request scope, timeline and team size | No full implementation plan |
| **T3** – ABCDE risk assessment | Risk | Project description + project_risks.csv | Categorized logistics/AI-relevant risks with documented impacts and mitigations; source IDs | No unrelated generic risks presented as source facts |
| **T4** – “New project starting soon” | Risk | Only vague request; no project risk file specified | INSUFFICIENT_DATA; ask for project details | No invented project-specific risk register |
| **T5** – Sprint 3 weekly status | Status | sample_task_board.csv filtered exactly to Sprint 3 | Real task names and status groups; accurate Sprint 3 counts | No cross-sprint leakage or invented tasks |
| **T6** – “Things are going fine” | Status | No task board provided for that request | Explicitly state no task data; request board/sprint | No fabricated progress, tasks or percentages |
| **T7** – Top 3 ABCDE risks | Risk | project_risks.csv | Return three actual risk IDs with explicit illustrative selection or separately approved proposed ranking; source has no rank | No newly fabricated risks; do not claim unsupported ranking certainty |
| **T8** – Blocked/at-risk tasks | Tracker | sample_task_board.csv + as-of date | Identify T024 Security review of AI infrastructure as BLOCKED; check other deadlines against actual statuses | Do not invent due dates, dependencies or blocker cause |
| **T9** – Two-week project, no further detail | Planner | Only two-week duration | INSUFFICIENT_DATA; ask for scope, goals and deliverables | No detailed plan based on duration alone |
| **T10** – Count current tasks | Status | Full sample_task_board.csv, no sprint filtering | 25 total: 5 Done, 3 In Progress, 16 To Do, 1 Blocked; verify against CSV | Counts reconcile; no unknown statuses silently coerced |
| **T11** – Upcoming milestones in 2 weeks | Tracker | project_timeline.csv + explicit as-of date | Actual relative milestone names; calendar window only with approved project anchor | No invented milestones; empty truthful result if none |
| **T12** – Sprint 2 stakeholder email | Status / email mode | Only Sprint 2 tasks from sample_task_board.csv | Professional draft with Sprint 2-only facts, issues and next actions when documented | No Sprint 3+ tasks; no automatic sending |

**Status:** All twelve cases are **addressed in the design**, but none should be claimed as executed or passed until original project files and recorded output evidence are available. Negative tests T2/T4/T6/T9 require isolated no-data context. T10 expects 25 tasks (5 Done, 3 In Progress, 1 Blocked, 16 To Do), to be verified from the original CSV. T11 needs an approved calendar start-date or project-week anchor as well as a reference period; without that anchor it requests clarification. T12 is a draft requiring approval before distribution. Proactive-monitoring CR-001 tests are **additional**, not replacements for these tests.


## Architecture change log and baseline retention

**v2.2 (2026-10-25):** Source-validated capstone rebaseline. Corrected MIRA-001 status fixture `5/3/1/16`, eliminated fixed 25-row assertion from generic processing, updated exact task/blocker names, introduced Sprint 2/3 golden records, clarified unranked risks and timeline Weeks 1–24 without calendar anchor, and aligned to PRD v1.3 / TDD v2.2 / Charter v1.2. Retains all **seven earlier diagrams**, the Router/Dispatcher and four specialists, and the explicit **unapproved Phase B** boundary.



| Version | Change | Preserved |
|---|---|---|
| 1.3–1.5 | Source input, four logical agent roles, human approval, monitoring and explicit T1–T12 path | Capstone baseline |
| **2.0** | Added P1/P2 production data/control plane, persistent approvals/alerts, high availability/security gates | Seven diagram views + T1–T12 + P01–P15 |
| **2.1 (2026-10-25)** | **Replaced Langflow/Make target with n8n-only orchestration and n8n specialists; added ADR-014 rationale, MIRA-001 exact-data implementation map, platform cost/integration risks and no-code feasibility gates** | **All seven diagram views, four agent responsibilities, FR-001–018, proposed FR-019–026, T1–T12, P01–P15** |

## Platform, cost and API integration assumptions

- **Single orchestrator:** n8n `Switch` + `Execute Sub-workflow` means no n8n→Langflow API hop and no Make.com scheduler; however OpenAI, Jira/Trello, storage and notification APIs still require authentication, retries and availability controls.
- **Token economics:** deterministic counts and calendar filters consume no LLM tokens; route known intents without LLM classification; call only necessary specialist(s); avoid AI on unchanged monitoring scans. The same OpenAI model with the same input/output token counts has **the same LLM bill** whether invoked from Langflow, n8n or hybrid. Record real token usage, retries, n8n workflow-execution charges, infrastructure, storage and provider message fees separately.
- **No-code feasibility:** n8n visual `Extract From File`, `Summarize`, `Filter`, `IF`, `Date & Time`, `Merge` and schema mappings are candidates, not automatic proof of uniqueness, date or type correctness. Do not conceal a Code node or spreadsheet calculation while claiming zero code. G3 must pass against the actual 25-row fixture.
- **Edition caveat:** n8n internal source control/environments and certain enterprise security/storage capabilities require eligible editions/configurations. GitHub manual exports are the capstone approach; do not represent a file commit as a deployed workflow.
- **Data governance:** n8n Form Trigger is a POC input surface, **not** a complete authenticated project portal. Use synthetic/approved data in development and verified per-project authorization for production.
- **Observability:** Capstone Q2 explicitly requires a connected evaluation/observability tool and trace screenshots; validate n8n Evaluations or a dedicated supported provider. n8n execution history alone does not satisfy a claim of completed evaluation-tool integration. LangWatch/Langfuse are options subject to compatibility and privacy checks; no integration is claimed as already deployed.
- **Release policy:** CR-001/proactive monitoring and P01–P15 production gates stay pending. System availability, isolation, approvals and notification delivery must be tested; none is inherited automatically from n8n.

### Official implementation reference links

- [n8n Summarize node](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.summarize/)
- [n8n Schedule Trigger](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.scheduletrigger/)
- [n8n AI evaluations](https://docs.n8n.io/advanced-ai/evaluations/overview/)
- [n8n executions](https://docs.n8n.io/workflows/executions/all-executions/)
- [n8n source-control plan and setup](https://docs.n8n.io/source-control-environments/create-environments/)
- [n8n security audit](https://docs.n8n.io/hosting/securing/security-audit/)

**Design disposition:** Selected proposed n8n-only implementation baseline; historical v2.0 Langflow/hybrid design remains archived. Dataset inspection has been completed offline; actual n8n workflow configuration, T1–T12 executions, connected observability, production security and business approval are not yet evidenced.
