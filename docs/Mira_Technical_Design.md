# Mira – Technical Design Document (TDD)

**Version:** 2.2 – n8n Source-Validated Design (supersedes platform choice in v2.0)  
**Status:** Architecture baseline proposal – NOT implemented or production certified; security, data and reliability gates pending  
**Date:** October 25, 2026  
**Project:** Mira – AI-Powered Project Intelligence Assistant  
**Client Program:** ABCDE Ltd. AI Adoption Project  
**Delivery Organization:** Nexora Pvt. Ltd.  
**Document Owner:** AI PM / TPM  
**Architecture Authority:** Enterprise Technical Architect / CTO  
**Requirements Baseline:** Mira PRD v1.3 (retains original v1.1 FR-001–FR-018 and T1–T12); **production change baseline:** PRD v2.0 / CR-PROD-001 approval required  
**Production Scope:** Future-only proposal: proactive monitoring FR-019–FR-026, identity, tenancy, audit, deployment, resilience and security controls; **not approved by the capstone**  
**Implementation Preference:** No-code application configuration where appropriate; managed infrastructure/security integration permitted  
**Platforms:** n8n (single business + AI orchestrator); OpenAI (version-pinned approved model); GitHub; approved project data storage; production-only portal/gateway, identity, persistence and notification systems  
**Architecture Diagrams:** [Architecture.md](Architecture.md)  
**Architecture Decision:** ADR-014 (n8n-only selection; prior Langflow/hybrid options superseded as implementation baseline)

> **Baseline and approval boundary:** v2.1 is a platform architecture revision to the existing TDD v2.0, **not** proof of implementation and **not** automatic approval of production scope. PRD v1.1, the four logical specialists, T1–T12, and P01–P15 are preserved. Proactive monitoring FR-019–FR-026, identity, project isolation, audited approval and resilience still require PRD v2.0 / CR-PROD-001 authorization and release evidence. The unmodified v2.0 originals remain the historical baseline.

## 1. Executive technical summary

Mira is a no-code-first project intelligence assistant for Nexora PMs/TPMs using approved project descriptions, timelines, risks and task-board data to generate source-grounded plans, risk analyses, sprint/status reports and milestone intelligence. Stakeholder-facing communications remain **DRAFT** until an authorized PM/TPM approves them.

**Revised execution architecture:** one **n8n** platform orchestrates ingestion, deterministic validation/counts/dates, request routing, four reusable specialist workflows (Planner, Risk Assessor, Status Reporter and Milestone Tracker), approved-model calls, output assurance, human-review handoffs and notifications. n8n's top-level Router uses rule-based `Switch`/`IF` where intent is explicit; an optional bounded model classification step may clarify ambiguous text. Specialists are invoked using **Execute Sub-workflow**, with strict input/output contracts. This is a logical multi-agent design; do not claim model-to-model autonomous delegation unless implemented and tested.

This revision removes the extra n8n↔Langflow service boundary and Make.com scheduling dependency. n8n execution history and native evaluation capabilities provide the **baseline** observability path, supplemented by model usage capture and external monitoring if needed. LangWatch becomes an optional assessed add-on, **not** a mandatory or assumed integration. GitHub stores reviewed workflow JSON, prompts, schemas, architecture and test evidence; it is not a database or automatic deployment mechanism.

**Production target:** on a governed schedule or verified source event, ingest fresh source data, evaluate deterministic milestone conditions, produce grounded explanations selectively, deduplicate and persist alerts, notify owners without waiting for report approval, and track acknowledgement, mitigation and escalation. This is a **proposed release feature** (FR-019–FR-026), not an executed implementation or guarantee that every delay will be prevented.

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

### 3.1 Preserved capabilities from PRD v1.1

- One ABCDE project baseline using explicit `project_id`; project selection is fixed for the capstone.
- User-supplied approved project files: `project_description.txt`, `project_timeline.csv`, `project_risks.csv`, `sample_task_board.csv`; twelve evaluation prompts in `eval_mira_inputs.txt`.
- Router/Dispatcher to **four independent n8n specialist workflows**; grounded plan, risk, status, milestone and Sprint 2 stakeholder **draft** outputs.
- No LLM-derived task totals or invented dates. Source filtering, counts and due-date conditions must be deterministic and verified.
- Insufficient-data protections for T2, T4, T6 and T9; manual human review and no auto-publication for T12.
- n8n execution/evaluation evidence, recorded model usage and cost where available, and export of credential-reviewed workflow JSON to GitHub.

### 3.2 Production enhancement: CR-001 proactive monitoring (release-gated)

- **n8n Schedule Trigger** and optional approved Jira/Trello event triggers; no separate Make.com scheduler needed.
- Authorized read-only source refresh, freshness checks, duplicate-alert prevention and persisted state.
- Alert owner, acknowledgement, mitigation, escalation, email/Teams/Slack delivery, audit and recovery.
- Scheduled rule processing is independent of LLM availability; use the Milestone Tracker only for optional grounded explanation.

### 3.3 Production-required and deferred elements

**Production target controls:** governed authentication/SSO at the application tier, tenant/project RBAC enforced before data retrieval, approved portal/project registry, versioned/encrypted snapshots, malware scanning, durable report/approval and alert stores, tested recovery, role separation, secure integration credentials, operational monitoring, staged deployment and approved SLAs. n8n workspace/project permissions alone do **not** enforce end-user application project authorization.

**Deferred unless separately approved:** autonomous writes to Jira/Trello, autonomous external communication, ERP execution, model fine-tuning, complex vector RAG, customer data in public demo forms, and a multi-project portal in the capstone. A production-grade user portal is not automatically supplied by n8n Form Trigger. No-code-first means minimal bespoke application code, **not** exemption from security engineering.

## 4. Architecture principles and trust boundaries

1. Source facts remain authoritative; LLM outputs are grounded and labeled as drafts/recommendations.
2. **Deterministic-before-generative:** n8n node logic or approved data-service queries calculate counts, sprint filters, exact IDs and due-date comparisons before AI narrative calls.
3. **One orchestration owner:** n8n coordinates cross-system business and AI execution; no parallel Langflow/Make service mesh.
4. **No unnecessary calls:** deterministic `Switch` routes clear requests; model-based classification is an exception, not a default extra token charge.
5. Strict `tenant_id`/`project_id` checks at the **trusted access/data boundary**, not merely via prompts or user-supplied form values.
6. Prompt-injection resistance: task data/files/API responses are untrusted source content; neither source text nor agent output grants tool authority.
7. Human-in-the-loop approval before stakeholder publication and baseline changes; internal safety/risk alerts are independently dispatchable.
8. Traceable and reversible: source snapshot, workflow export hash, model/prompt version, report decision and alert state.
9. Fail closed on invalid/unknown/stale inputs; degrade visibly on AI/provider outages.
10. Protect credentials and minimum necessary data; never place secrets or confidential snapshots in public repos or logs.

### 4.1 Trust boundaries

Capstone: tester → n8n Form Trigger/test webhook (synthetic data only) → n8n validation/workflows → approved OpenAI endpoint; developer → GitHub exports. **Do not expose an unauthenticated form with client data.** Production: authenticated portal/IdP → verified project authorization → secure n8n API/trigger → governed storage and read-only integrations → model provider → reviewed publication/notification services. n8n internal project RBAC controls editing/sharing for workflow builders, not row-level access for end-user projects. Third-party model/data/telemetry transfers require explicit privacy and contract approval.

## 5. Logical component and workflow design

See [Architecture.md](Architecture.md) for **P1–P2 and Diagrams 1–5** (seven Mermaid views), fully revised for n8n.

| Layer | Responsibility | Proposed n8n implementation | Status |
|---|---|---|---|
| Capstone input | Project ID + CSV upload | **n8n Form Trigger** / manual trigger; approved sample data only | To build |
| Source extraction | Decode approved files | **Extract From File**, data mapping / `Edit Fields` | Schema inspection pending |
| Deterministic quality | Validate IDs/status/date, aggregate totals | **IF**, **Filter**, **Summarize**, **Remove Duplicates** or approved data-service queries | Critical MIRA-001 spike |
| Request Router | Intent and evidence sufficiency | **Switch**/`IF`; optional constrained classifier for ambiguity | To build |
| Planner | Grounded timeline/plan | **Execute Sub-workflow** → prompts + approved OpenAI model | To build |
| Risk Assessor | Risk identification/ranking | Specialist sub-workflow + validated risk records | To build |
| Status Reporter | Counts and stakeholder draft | Deterministic summary → AI narrative (only if necessary) | First agent |
| Milestone Tracker | Blocked/overdue/upcoming and reason | Rule evaluation → optional AI explanation | Extended baseline |
| Approval | Draft disposition, publish gate | Capstone manual PM review; production persistent **Wait/approval** workflow plus authorization | Release-gated |
| Proactive monitor | Schedule, dedup, notify | **Schedule Trigger**, approved connectors, state store, notifier | CR-001 only |
| Observability/evals | Execution state, assertions, model usage | n8n Executions + n8n evaluations where available; optional external telemetry | Feasibility gate |
| Source control | Versions and tests | Reviewed n8n JSON exports, prompt/fixture files, GitHub PRs | To build |

### 5.1 Named workflows and orchestration pattern

| ID | n8n workflow | Trigger/invocation | Responsibility |
|---|---|---|---|
| `MIRA-WF-00` | `Mira_00_Orchestrator` | Form/Webhook/Execute Sub-workflow | Authorize request context, route one or more intents, collect output |
| `MIRA-WF-01` | `Mira_01_Planner` | Execute Sub-workflow Trigger | Create grounded plan or missing-data questions |
| `MIRA-WF-02` | `Mira_02_Risk_Assessor` | Execute Sub-workflow Trigger | Source-backed risk assessment and top-three selection |
| `MIRA-WF-03` | `Mira_03_Status_Reporter` | Execute Sub-workflow Trigger | Counts, sprint filter and stakeholder email draft |
| `MIRA-WF-04` | `Mira_04_Milestone_Tracker` | Execute Sub-workflow Trigger | Date/blocker conditions and optional narrative |
| `MIRA-WF-05` | `Mira_05_Data_Validation` | Form/Import/Execute Sub-workflow | Ingest, normalize, reject invalid input and snapshot |
| `MIRA-WF-06` | `Mira_06_Proactive_Alert_Monitor` | Schedule/Jira-Trello approved event | CR-001 alert lifecycle, conditional AI explanation |
| `MIRA-WF-07` | `Mira_07_Evaluation_Runner` | Manual/evaluation trigger | T1–T12 controlled replay and recorded assertions |

**No-code implementation boundary:** Prefer built-in n8n nodes, visual mappings and safe expressions; do **not** silently rely on the Code node or AI arithmetic while claiming entirely no-code. Prove uniqueness/date comparison, correct `Merge` item association, and two-branch status counts against the real schema. If an essential invariant cannot be enforced through approved visual components, raise a design exception and use a reviewed deterministic data service; do not weaken acceptance criteria.

### 5.2 Request contract (logical JSON fields)

`request_id`, `tenant_id` (production), `project_id`, `user_id` (production verified), `user_message`, `intent`, `sprint`, `as_of_date`, `reporting_start`, `reporting_end`, `output_mode`, `source_snapshot_id`, `workflow_version`, `allow_project_context`.

For T2/T4/T6/T9, `allow_project_context=false` and empty/unprovided source snapshot **must** prevent ABCDE fixture injection. n8n's project/workspace selection is **not** the Mira business `project_id`.

### 5.3 Response contract

`request_id`, `project_id`, `agent_name`, `result_status`, `response_data`, `source_references`, `source_snapshot_id`, `missing_information`, `warnings`, `execution_metadata` (workflow/model/prompt version and actual usage if exposed), `review_status`.

Allowed `result_status`: `SUCCESS`, `INSUFFICIENT_DATA`, `VALIDATION_ERROR`, `UNSUPPORTED_REQUEST`, `SERVICE_ERROR`. A valid JSON shape does not prove factual accuracy. `SUCCESS` requires checked evidence; otherwise return failed or pending validation state.

## 6. Project data architecture and ingestion

The **original assignment** names `project_description.txt`, `project_timeline.csv` (eight phases), `project_risks.csv` (ten risks), `sample_task_board.csv` (25 tasks) and `eval_mira_inputs.txt` (12 tests). The source CSV schemas and dates have been profiled and **must be inspected**, not invented.

| Entity | Canonical fields only when source provides them | Source of truth |
|---|---|---|
| Project | project_id, project_name, goals, scope, six-month duration | Project description |
| Task | `task_id, task_name, status, assignee, priority, sprint, due_date, labels, description` | `sample_task_board.csv` |
| Risk | `risk_id, category, risk_challenge, impact, mitigation_strategy` (no likelihood/score/rank) | `project_risks.csv` |
| Timeline phase | `phase, phase_name, start_week, end_week, key_activities, milestones_deliverables`; no due date, milestone ID or dependency mapping | `project_timeline.csv` |

### 6.1 File-based capstone route

`n8n Form Trigger (fixed ABCDE selector + CSV file) → Extract From File → required-column check → normalized field mapping → reject empty/duplicate/unknown values → per-snapshot record counts → preserve task IDs → validated snapshot context → specialist input`. Use n8n item mappings to preserve file metadata, `request_id`, `project_id`, source hash/version and `imported_at` across all branches. Do not count test examples as actual records.

Read-only Jira/Trello is an optional later route into the **same normalized schema**, not a direct unrestricted LLM connector. A business project dropdown does not alone enforce isolation; production auth and project-scoped storage are separate required controls. GitHub commits or n8n export files do **not** automatically update an already running instance.

### 6.2 Quality, freshness and source precedence

Reject unexpected file formats, malformed CSV, missing headers, duplicate task IDs, blank status/IDs, unknown status labels, invalid dates and cross-project joins. Explicitly distinguish absent dependencies from confirmed absence. Keep snapshot `as_of` timestamp and freshness label on reports; no real-time claim for static CSVs. Conflicting upload/API versions block automatic publication pending source-precedence decision. For reproducible tests, pin snapshot IDs and fixed dates.

### 6.3 Verified capstone schemas and test fixtures (source data, not runtime results)

**Actual CSV headers (exact spelling, including underscores):**

```text
sample_task_board.csv: task_id,task_name,status,assignee,priority,sprint,due_date,labels,description
project_timeline.csv: phase,phase_name,start_week,end_week,key_activities,milestones_deliverables
project_risks.csv: risk_id,category,risk_challenge,impact,mitigation_strategy
```

| Fixture / contract | Expected observed source data | Mandatory control |
|---|---|---|
| Board completeness | 25 rows, 25 unique IDs T001–T025 | Assert only in MIRA-001 *test mode*; normal project size is not fixed at 25 |
| Allowed statuses | `Done`, `In Progress`, `Blocked`, `To Do` | Reject unknown labels; no silent fallback |
| Board full counts (T10) | `5 Done / 3 In Progress / 1 Blocked / 16 To Do` | n8n deterministic grouping + sum=25 |
| Sprint 2 (T12) | T004 Done, T005 Done, T006 In Progress | Exact source sprint filter; draft only |
| Sprint 3 (T5) | T007 In Progress, T008 To Do, T009 To Do, T025 In Progress | Exact source sprint filter; zero other sprint rows |
| Explicit blocker (T8) | T024 `Security review of AI infrastructure`, `Blocked`, due `2026-05-26`, Sprint 5 | Preserve exact task name and blocker description |
| Milestones (T11) | 8 phases covering project Weeks 1–24 | Do not create calendar milestones without approved anchor |
| Registered risks (T7) | R01–R10; no numeric scoring/rank | Three actual IDs, declare illustrative/approved ranking method |
| Negative context tests | T2/T4/T6/T9 intentionally omit sufficient data | No default ABCDE fixture injection |

**Reference documents:** `ABCDE Ltd Introduction..txt` duplicates the canonical description; the timeline PDF and risk DOCX provide a human-readable perspective but do not override CSV fields without approved reconciliation. The risk DOCX has expanded wording; do not silently treat its prose as the CSV risk register. Dataset is a static sample with due dates 2026-04-07 through 2026-08-04; it is not a live operational board.

### 6.4 Assignment-prose discrepancy (explicitly preserved)

The capstone's narrative *About the sample data* states `3 Done, 3 In Progress, 1 Blocked, rest To Do`, but actual CSV parsing yields `5 Done, 3 In Progress, 1 Blocked, 16 To Do` across 25 records. **T10 explicitly requires the calculation to match actual source data.** Both statements remain documented; do not alter either uploaded source. CR-DATA-001 records the evidence and requires product-owner/QA agreement to the CSV-backed expected outcome prior to G3/G6 approval.

### 6.5 Data and request contracts for deterministic, no-code processing

A normalized request should include: `request_id`, `project_id` (fixed capstone project), `intent`, `source_snapshot_id`, `allow_project_context` (false for vague-input tests), `sprint` (optional), `as_of_date` (optional but required for deadline comparisons), `project_start_date` or `as_of_project_week` (only if supplied/approved), and `output_mode`. The response should include `result_status`, `facts`, `source_refs`, `warnings`, `as_of_date`, `data_freshness_label` (`STATIC_SNAPSHOT` for the capstone), `proposed_recommendations`, and `review_state` (`DRAFT` until human approved).

**n8n node sequence:** approved form/controlled input → Extract From File (CSV) → verify file, required columns/IDs, valid dates and exact status taxonomy → duplicate check (must prove no suppressed records) → per-source version/snapshot envelope → deterministic Filter/Summarize/Date and Time → specialist sub-workflow via Execute Sub-workflow → source and response assertions → human draft review. Use version-specific n8n UI capability checks and proof-of-execution screenshots before claiming the entire sequence requires no custom code.

**Failure behavior:** malformed CSV, unknown statuses, incomplete records, conflicting source snapshots, or invalid dates produce `VALIDATION_ERROR` with cause; missing sprint data or missing project-week calendar anchor produces `INSUFFICIENT_DATA` or clarification as appropriate; non-approved defaults must never be inserted silently. Any retry must not publish or modify external systems without review.

## 7. Deterministic rules and output assurance

### 7.1 Task aggregation: MIRA-001 critical proof

Use n8n **Filter/Summarize** on validated task IDs, not an LLM prompt. Verify the **source-profiled fixture**: 25 full-board records with **Done 5, In Progress 3, Blocked 1, To Do 16**; **these are offline verified input expectations, not observed n8n execution results**. Use **Count Unique** / duplicate validation and reconcile `sum(group_counts) == total_valid_rows` including blank/unknown-value rejection; avoid accidentally excluding invalid rows in Summarize's grouping options. For Sprint 3/Sprint 2 requests, apply exact sprint filter *before* aggregation and text generation. Calculate any completion percentage with a documented deterministic numerator/denominator.

### 7.2 Milestone and date rules

- `BLOCKED`: source `status` exactly equals `Blocked` (map to internal `BLOCKED` condition only after validation).
- `OVERDUE`: incomplete with valid due date earlier than fixed `as_of_date` in project timezone.
- `UPCOMING`: determine actual source relative milestone weeks first; only produce a calendar `as_of_date` + 14-day window if an approved project start-date/week-to-date mapping exists. Otherwise ask for a calendar anchor or state that the dataset cannot establish dated upcoming milestones.
- `AT_RISK`: actual documented dependency or evidence showing material concern; mere proximity to due date is a watch, not a proven risk.
- Unknown date/dependency/owner: evidence gap, never an invented field.
- T024 `Security review of AI infrastructure` must be found in source with exact status `Blocked` before asserting it is blocked.

### 7.3 Risk ranking

The actual `project_risks.csv` contains **no** `priority`, `likelihood`, `severity`, probability or score field; its `impact` values are narrative text. For T7, return three **real source risk IDs** selected under an **explicitly stated illustrative rule** (e.g., first three register records R01–R03, explicitly *not ranked*) or request a business-approved impact/likelihood policy and then label the computed order as **proposed**. Do not present source order as an objectively ranked top three. A numeric score cannot be extracted from descriptive impact prose without an approved rubric. Record the selected rule in the test fixture and output provenance.

### 7.4 Generation and assurance

Provide model only the minimum validated project records for the intended request. Output required JSON/structured fields where supported, then validate both schema **and source truth** outside the model; label recommendations. Reject unsupported milestone/task/risk IDs, bad counts, cross-sprint content and undocumented assertions. `Edit Fields` output shaping or schema-constrained model output alone does not establish grounding. For controls without practical no-code validation, record a manual assertion and open a gap rather than falsely claiming automated assurance.

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

**Capstone:** n8n saves a generated response as **DRAFT** and presents it for documented manual PM/TPM review. No auto-sending T12 stakeholder emails. Mark manual decisions outside the workflow or in an approved simple tracking record until the approval gate is actually built.

**Production target:** n8n **Wait** or approved human-review interaction suspends a workflow; an authenticated portal/service or approved interactive notification collects decision and reviewer identity. Persist `report_id`, immutable draft version/hash, snapshot version, reviewer, decision, comments and timestamp in a trusted store. Verify authorization in the backing service; a link or `Wait` node alone is **not** secure approval. Reject/modify creates a new unapproved version, reevaluate it, and prevent all publish paths without persisted authorization.

**Alert exception:** automatic evidence-backed internal warnings to assigned PMs do not wait for report approval. Baseline/date changes and stakeholder-facing publication require prior approval.

## 10. Production proactive monitoring and escalation (CR-001 pending)

### 10.1 Business objective and change control

PRD v1.1 requires **on-demand** milestone detection. FR-019–FR-026 introduce scheduled/event-driven detection, notification, acknowledgement and escalation; they are a production extension subject to PRD v2.0 / CR-PROD-001 approval. Do not claim all missed milestones will be prevented.

### 10.2 n8n execution pipeline

Use n8n **Schedule Trigger** with explicitly configured workflow timezone, or authorized Jira/Trello event triggers. Triggered run → fetch fresh authorized read-only data → schema/freshness/tenant-project validation → persist snapshot → deterministic blocked/overdue/upcoming/dependency checks → compare alert fingerprint and prior state in durable register → optionally call `Mira_04_Milestone_Tracker` for concise grounded wording → persist/outbox → notify owner → track acknowledgement and escalation. Do **not** incur AI calls on unchanged/no-alert checks. Static uploaded CSV repeated on a schedule does not detect new status changes.

### 10.3 Proposed rulebook (approval pending)

| ID | Trigger condition | Proposed output |
|---|---|---|
| MR-01 | Task status BLOCKED | High internal alert |
| MR-02 | Incomplete task past actual due date | High overdue alert |
| MR-03 | Milestone ≤7 days away with documented blocked prerequisite | High milestone risk |
| MR-04 | Milestone ≤14 days away with documented incomplete prerequisite | Medium watch |
| MR-05 | Incomplete task due within ≤3 days | Low watch, not asserted missed |
| MR-06 | Stale/failed source sync or missing valid dates | Source-quality alert |
| MR-07 | Critical alert unacknowledged past threshold | Escalation |

### 10.4 Registry and deduplication

Minimum: `alert_id`, `tenant_id`, `project_id`, `source_ref`, `rule_id`, `rule_version`, `severity`, `source_snapshot_id`, `detected_at`, `last_seen_at`, `owner`, `notification_status`, `acknowledged_at`, `mitigation_owner`, `mitigation_due`, `state`, `escalation_level`. Fingerprint `tenant_id + project_id + source_ref + rule_id`; include relevant state/version for meaningful changes, not every polling run. State: `DETECTED → NOTIFIED → ACKNOWLEDGED → ACTION_ASSIGNED → MONITORING → RESOLVED`, with `ESCALATED` when policy threshold elapses.

### 10.5 Notifications and approvals

Use approved provider nodes/email/Teams/Slack with least-privilege credentials. Persist an outbound intent **before** sending and record acceptance/receipt where the provider supports it; a successful n8n HTTP request is not proof the recipient read the notice. Bounded retry/backoff; idempotent sending prevents duplication. Public/stakeholder communications remain approval-gated.

### 10.6 Proposed operational targets

Subject to sponsor approval: ≥99% scheduled-run success, 100% detection of synthetic seeded blockers, ≤15 minutes from successful scan to internal alert dispatch, and ≤4 business hours for critical acknowledgement. Actual performance must be measured, with coverage for missed schedules, stale sources, false alerts, undelivered messages, repeats and resolved conditions.

## 11. Security, privacy, reliability and governance

- n8n credentials for OpenAI, Jira/Trello and notifier are encrypted/managed per approved deployment. **Never export credentials or secrets to GitHub**; scrub all workflow JSON, sample files and execution screenshots.
- Treat uploaded files and connector fields as untrusted; prohibit AI from invoking arbitrary HTTP/write tools based on source text. Explicit tool allowlists and read-only scopes.
- n8n workflow-editor/project permissions are **not** a replacement for business end-user `project_id` authorization; validate membership at trusted portal/data service.
- Disable or protect public forms/webhooks and apply file-size/type controls and malware screening before production ingestion; synthetic demos only on open testing endpoints.
- Verify permitted external processing, retention and cross-border transfer before sending client data to OpenAI or telemetry.
- Persist approval/alert audit in a controlled store; constrain retries, budgets, task timeouts, notification channels, data retention and logging.
- Outage or stale feed returns `DEGRADED`/`INSUFFICIENT_DATA`, never fabricated green status.
- Actual n8n license/edition and hosting options (Cloud vs self-hosted) determine availability of enterprise SSO, source control, queue mode and other controls; verify entitlements before finalizing deployment.

## 12. Observability, evaluation and economics

**Mandatory capstone integration:** Wire a concrete evaluation/observability tool to n8n and retain actual trace/evaluation screenshots for execution of T1–T12 (for example, compatible n8n Evaluation capabilities or a supported dedicated tracing provider). n8n execution history alone supports debugging but does not automatically prove that the required **evaluation/observability tool connection** was completed. Validate availability, version/edition, secure transport and trace redaction at G1/G6; record a failed/blocked requirement if the connection has not been tested. An external telemetry provider does not change the n8n-only orchestration decision.


**Baseline:** use n8n **Executions** (success/failure/step-level details subject to retention and plan), error workflows/notifications, and n8n **Evaluations** for known datasets where available. Preserve a separate GitHub T1–T12 evidence ledger with inputs, source version, expected and actual assertions, defect owner and reviewer. Persist cross-workflow `request_id` / execution IDs because child workflows are independent units. An n8n execution log is not an enterprise security audit or proof of end-user notification delivery.

**Model telemetry:** record approved model ID, prompt version, latency, actual input/output token usage where the node/provider exposes it, retries, LLM calls per request, and provider unit pricing effective at test time. If usage metadata is missing, report estimate separately and do not represent it as measured spend. LangWatch and LangSmith may be assessed as **optional integrations**, with supported-runtime/no-code and privacy gate; do not call them deployed or required.

**Token optimization:** `Switch` first for known intent; scope data to project and requested sprint; deterministic aggregation before LLM; call only the needed specialist; no LLM call on unchanged scheduled scans; cap output length; retry only transient failures. **LLM cost = Σ[(input_tokens × input_price) + (output_tokens × output_price)] / 1,000,000**, plus provider-specific charges. Platform subscription, execution counts, storage, notification, evaluation and support costs are separately budgeted.

**Success measures:** T1–T12 exact evidence, no unsupported source fact in accepted outputs, report/plan effort including PM review, request p50/p95, error/timeout rate, model cost per successful output, alert delivery/acknowledgement and project data freshness. All targets remain proposed until measured.

## 13. Implementation plan, dependencies and release gates

| Gate | Minimum evidence | Scope |
|---|---|---|
| G1 – Platform | Provision n8n Cloud trial/approved host, secured OpenAI credential; export/import smoke test | Capstone |
| G2 – Source data | Inspect real five assignment files, CSV headers and row counts; validate data classification | Capstone |
| G3 – **MIRA-001** | Form CSV → extraction → validation → exact 25, counts 5/3/1/16, T024 and sprint filter, no LLM arithmetic | **Blocking capstone** |
| G4 – Specialists | Four named n8n sub-workflows produce source-grounded contracts and insufficiency statuses | Capstone |
| G5 – Router/HITL | Deterministic dispatch + fallback handling; manual PM/TPM review of drafts | Capstone |
| G6 – Test/eval | Execute T1–T12 with no-data isolation; n8n executions/evaluation results and model usage/cost evidence | Capstone |
| G7 – Delivery | Reviewed JSON exports and docs on GitHub; redacted screenshots and rollback smoke test | Capstone |
| G8 – Scope | Approve FR-019–FR-026, data-refresh SLA, owner mapping, notification policy | Production CR-001 |
| G9 – Alert POC | Schedule Trigger → fresh data → rules → durable dedup → owner notification | Production CR-001 |
| G10 – Operations | Security, approvals, recovery, tenancy isolation, P01–P15, SLO monitoring | Production target |

### 13.1 No-code feasibility proof

`Extract From File`, `Summarize`, `Filter`, `IF`, `Date & Time` and data mapping are candidate nodes, **not proof** that all schema/uniqueness/date invariants work without custom code. Verify branch joins, task completeness and data type conversion on real fixtures; log any built-in node limitation. If a rule cannot be implemented robustly with reviewed visual logic, obtain explicit approval for an external deterministic data service instead of LLM approximation.

### 13.2 Reproduction and rollback

Store `n8n/Mira_00_Orchestrator.json` through `n8n/Mira_07_Evaluation_Runner.json` as individually exported, reviewed workflow definitions (only after actual exports exist). Keep prompt text, schema mappings, release manifest, fixture hashes and runbooks. Do not manufacture placeholder runnable exports. In the capstone, import manually into a clean test workspace, rebind credentials and sources, and replay smoke/T1–T12 tests. n8n enterprise Git-based source control is **plan-dependent**; GitHub files do not automatically synchronize a cloud trial or deploy themselves.

## 14. Requirements traceability (no requirement loss)

| PRD ID | n8n realization | Evidence |
|---|---|---|
| FR-001 | Form Trigger + Extract From File + validation | G2/G3 |
| FR-002 | Switch router + Execute Sub-workflow contracts; bounded ambiguity branch | Routing tests |
| FR-003/004 | Planner workflow + missing-data gates | T1, T2, T9 |
| FR-005/006 | Risk Assessor workflow + grounded ranking | T3, T4, T7 |
| FR-007/008 | Status Reporter + Filter/Summarize | T5, T6, T10 |
| FR-009/010 | Tracker + deterministic as-of/dependency rules | T8, T11 |
| FR-011 | Status Reporter draft output; no auto-send | T12 |
| FR-012/013 | Schema and fact checks; explicit failed/insufficient response | Cross-cutting |
| FR-014/015 | n8n evaluation/execution ledger + approved external evaluation if required | T1–T12 |
| FR-016 | Markdown and JSON output shaping | Contract tests |
| FR-017 | PM/TPM review (manual capstone, durable production) | Approval gate |
| FR-018 | Authorized read-only Jira/Trello | Deferred/optional |
| **FR-019–026 proposed** | Schedule Trigger, fresh sync, deterministic rules, registry, alerts, acknowledgement/escalation | **CR-001 approval + production tests** |

## 15. Architecture decision records and rationale

| ADR | Historical decision | v2.1 disposition |
|---|---|---|
| ADR-001 | Langflow visual specialist flows | **Superseded by ADR-014**; n8n specialist workflows |
| ADR-002 | Router/Dispatcher via Langflow Run Flow | **Superseded**; n8n `Switch` + `Execute Sub-workflow` |
| ADR-003 | GPT-4o-mini initial selection | Keep as historical candidate; use supported, approved pinned model after pricing/quality review |
| ADR-004 | Source-file grounding, no vector DB for small dataset | **Retained** |
| ADR-005 | Deterministic processing, feasibility gate | **Retained; implement in n8n** |
| ADR-006 | LangWatch observability | **Superseded as default**; n8n executions/evals, LangWatch optional |
| ADR-007 | GitHub export-based source control | **Retained**, format changed to n8n workflow JSON |
| ADR-008 | Human review of report publication | **Retained** |
| ADR-009 | Project-scoped versioned source context | **Retained** |
| ADR-010 | On-demand milestone detection | **Retained** |
| ADR-011 | Make.com/hosted Langflow monitoring | **Superseded**; n8n Schedule Trigger and integrated monitoring |
| ADR-012 | Alert register and lifecycle | **Retained, release-gated** |
| ADR-013 | Internal alerts bypass publication approval | **Retained, policy approval pending** |
| **ADR-014** | **Choose n8n-only over Langflow-only or hybrid for Mira implementation baseline** | **Proposed approval; v2.2 source-validated baseline selected** |

### 15.1 ADR-014 — Why n8n-only for Mira (new)

**Context.** Mira needs deterministic CSV counts, read-only Jira/Trello integrations, scheduled monitoring, report approval and four AI capabilities. v2.0's Langflow-centered architecture depended on extra orchestration/notification infrastructure; a proposed hybrid adds n8n↔Langflow calls and two execution/observability surfaces.

| Option | Strength | Primary disadvantage for Mira | Decision |
|---|---|---|---|
| **Langflow-only** | AI-first visual agent composition | More design work for scheduled operations, source connectivity and durable business state | Not selected |
| **n8n + Langflow hybrid** | Clear separation of AI composition and business automation | Additional authenticated API boundary, failure/retry/trace correlation and hosting cost; no inherent token saving | Retain as contingency |
| **n8n-only** | Integrations, Schedule Trigger, human handoff and AI workflow components in one orchestrator | Need to prove agent quality and no-code invariants; production tier/hosting controls vary | **Selected for POC baseline** |

**Business and TPM case.** Choose the **smallest architecture** that satisfies PRD v1.1 and provides a credible path to production: fewer deployable platforms, no n8n↔Langflow HTTP integration, simpler secrets/incident ownership, and less chance of repeated AI router calls. n8n is not automatically more token-efficient: equal prompts and LLM calls cost the same regardless of platform, so actual savings must be measured from call count and context size.

**Interview/architecture-review narrative.** The team reconsidered a prior technology choice when requirements expanded, evaluated three options against delivery risk and nonfunctional requirements, retained all functional acceptance criteria, and chose an architecture it could validate end to end rather than an unnecessarily distributed system. This is an **architectural trade-off**, not proof of production readiness.

**Conditions and exit:** (1) G3 exact data processing passes without LLM arithmetic, (2) four independent n8n specialist workflows and tests pass, (3) T1–T12 execution ledger exists, (4) cost, latency and failure recovery are recorded, (5) security owner approves any production portal/tenant controls, and (6) the n8n edition/hosting supports chosen operations. If failures show unmet AI requirements, reopen ADR-014 and benchmark hybrid with identical fixtures. **Sponsor/CTO approval status: pending.**

**Scope impact:** no PRD v1.1 change; update TDD/Architecture implementation mapping. Scheduled monitoring/portal/security remain change-controlled through PRD v2.0 / CR-001.

## 16. Risks, open decisions and sign-off

| ID | Risk or design uncertainty | Accountable action |
|---|---|---|
| OT-01 | Actual dataset fields/dependencies unknown | Inspect original five files; source owner confirms |
| OT-02 | n8n no-code uniqueness, date and branch-merge logic unproven | Execute G3 technical spike and negative fixtures |
| OT-03 | Factual hallucination despite structured output | Independent assertion checks and manual acceptance gate |
| OT-04 | n8n evaluation/model-usage capture varies with nodes and plan | Integrate and verify G6 telemetry; disclose missing fields |
| OT-05 | Public Form Trigger/webhook could expose customer data | Use synthetic data in capstone; secure portal/gateway for production |
| OT-06 | Static CSV snapshot becomes stale | Freshness policy and approved Jira/Trello sync |
| OT-07 | Alert thresholds, owner mapping and SLA not approved | Sponsor/PM decision via CR-001 |
| OT-08 | Retry-induced duplicate alert or report publication | Durable idempotency + release gate + P06–P08 |
| OT-09 | Persistent authenticated approval not yet implemented | Manual documented review for MVP; production gate |
| OT-10 | n8n Cloud plan/hosting limits on security/CI/CD/queue mode | Validate edition/entitlements and pick approved deployment |
| OT-11 | Project/tenant data leakage across workflows or AI tools | Trusted authorization, scoped queries and tests P02/P09 |

**Design disposition:** n8n-only chosen as the *proposed implementation baseline*, pending ADR-014 approval and G1–G7 evidence. Production controls remain target-state until P01–P15. A design document is not evidence of completed workflows.

### Architecture review / sign-off

| Role | Required review | Status |
|---|---|---|
| Sponsor/Product Owner | Scope and CR-001 priorities | Pending |
| TPM | Delivery gates, cost and dependency ownership | Pending |
| AI Architect/CTO | ADR-014, determinism, quality and integration | Proposed / pending |
| QA/Evaluation owner | T1–T12 traceability and negative fixtures | Pending |
| Security/SRE | Customer data handling, operations and production signoff | Pending |

## 17. Explicit T1–T12 baseline test coverage and acceptance assertions

The architecture **covers the required test scenarios by design**, not by demonstrated execution. A test is **PASS** only after the actual supplied files are loaded (where applicable), the expected behavior is observed, and the result is recorded. Some vague-input tests must deliberately run **without** default ABCDE data injected into their context.

| Test | Flow / capability | Required input and isolation | Pass criteria | Failure guard |
|---|---|---|---|---|
| **T1** – Detailed ABCDE project plan | Planner | project_description.txt + project_timeline.csv | Use actual objectives, six-month duration, phases, activities, deliverables and approved milestones; label unsupported proposals | No invented goals, phase names or dates |
| **T2** – “We want to build a chatbot” | Planner | Only one-sentence user request; do not silently inject ABCDE context | INSUFFICIENT_DATA; request scope, timeline and team size | No full implementation plan |
| **T3** – ABCDE risk assessment | Risk | Project description + project_risks.csv | Categorized logistics/AI-relevant risks with documented impacts and mitigations; source IDs | No unrelated generic risks presented as source facts |
| **T4** – “New project starting soon” | Risk | Only vague request; no project risk file specified | INSUFFICIENT_DATA; ask for project details | No invented project-specific risk register |
| **T5** – Sprint 3 weekly status | Status | sample_task_board.csv filtered exactly to Sprint 3 | T007/T025 In Progress; T008/T009 To Do; exactly four Sprint 3 records | No cross-sprint leakage or invented tasks |
| **T6** – “Things are going fine” | Status | No task board provided for that request | Explicitly state no task data; request board/sprint | No fabricated progress, tasks or percentages |
| **T7** – Top 3 ABCDE risks | Risk | project_risks.csv | Three actual risk IDs; disclose that register has no source rank; use an explicitly illustrative or approved proposed selection method | No newly fabricated risks; do not claim unsupported ranking certainty |
| **T8** – Blocked/at-risk tasks | Tracker | sample_task_board.csv + explicit as-of date | T024 Security review of AI infrastructure source `Blocked`; evaluate dates against historical snapshot | Do not invent due dates, dependencies or blocker cause |
| **T9** – Two-week project, no further detail | Planner | Only two-week duration | INSUFFICIENT_DATA; ask for scope, goals and deliverables | No detailed plan based on duration alone |
| **T10** – Count snapshot tasks | Status | Full sample_task_board.csv, no sprint filtering | 25 total: 5 Done, 3 In Progress, 16 To Do, 1 Blocked, from source-profiling fixture; prove in workflow | Counts reconcile; no unknown statuses silently coerced |
| **T11** – Upcoming milestones in 2 weeks | Tracker | project_timeline.csv + approved project start-date or reference project week (absent in dataset) | Identify week-based candidate milestones only when anchored; otherwise ask for anchor, no invented dates | No invented milestones; empty truthful result if none |
| **T12** – Sprint 2 stakeholder email | Status / email mode | Only Sprint 2 tasks from sample_task_board.csv | Professional draft: T004/T005 Done and T006 In Progress; no Sprint 3+ activity or automated send | No Sprint 3+ tasks; no automatic sending |

### 17.1 Test execution controls

**Coursework evidence checklist (Q1–Q4):** Q1 needs 2–3 use cases, individual pains/agent solutions/inputs/outputs/three success metrics/knowledge base. Q2 needs implementable architecture, working core + selected milestone extension, connected observability, all 12 actual test runs, cost-per-plan and at least three measured evaluation metrics with a 1–2-page build report. Q3 is the governed Mira Program Charter. Q4 is a one-page reflection on observed traces, proposed improvements, privacy and a *proposed* ten-PM/TPM rollout. Export scrubbed n8n JSON to GitHub alongside README, canvas/output/trace screenshots and the repository submission link. Maintain clear implementation evidence versus design proposals.



- **Dataset fixtures:** Preserve the five canonical original supplied files as immutable baseline inputs. Do not substitute demonstration examples for test evidence. The supplied ZIP contains the original files; the schemas and task counts were verified through offline profiling (not through n8n execution).
- **No-data isolation:** For T2, T4, T6 and T9, suppress automatic use of ABCDE files unless explicitly referenced by the user. Otherwise a vague request could incorrectly appear sufficiently grounded.
- **Deterministic assertions:** T5 and T10 require programmatic/no-code exact aggregations. T8 and T11 require explicit as-of-date logic. A plausible-looking LLM answer is not enough.
- **Risk priority ambiguity:** If `project_risks.csv` provides no authoritative ranking, define and document a transparent derived ranking with a fixed tie-breaker before accepting T7. Never assert that an arbitrary top three are source-ranked.
- **Temporal fixture:** T11 needs an authorized project calendar anchor (`project_start_date`) or project-week reference **in addition to any** `as_of_date`; if missing, the correct test behavior is to ask for clarification, not to invent milestones due within the next two calendar weeks.
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

## 18. Historical version change log

| Version | Baseline preserved | Added/clarified |
|---|---|---|
| 1.2–1.4 | Four logical specialists, grounding, HITL, T1–T12 | Source freshness and proactive-monitoring proposal |
| 2.0 | Production-target design, CR-001 control boundaries, seven diagrams, P01–P15 | SSO/tenant isolation, durable storage/audit, recovery and deployment |
| **2.1** | **All PRD v1.1 requirements, T1–T12, CR-001 flags, P01–P15** | **n8n-only selection ADR-014; workflow IDs, token/API cost trade-offs, no-code proof gates, native scheduling, execution/evaluation telemetry and release caveats** |

The v2.0 source documents remain historical baseline artifacts; this document is their **n8n implementation revision**, not a claim that the prior design ran in production.

## 19. Production reference deployment architecture (normative target)

**Proposed deployment:** authenticated user portal → enterprise IdP/SSO → WAF/API gateway → verified project-authorization service → governed upload/connector ingestion and snapshot store → **private/secured n8n workflow execution** with durable job orchestration where licensed/configured → approved OpenAI model → validated report draft/alert → persistent approval or alert lifecycle → authorized publication/notifications. n8n's main workflow invokes specialist n8n sub-workflows; no Langflow, Make.com, or cross-orchestrator HTTP hop. See Architecture P1/P2.

- **Identity:** enforce project membership and role-based access in trusted portal/data service. n8n users/projects control workflow authoring; they **do not alone provide business row-level tenant isolation**. HTTPS, secure trigger endpoints, token/SSO integration and least-privilege service identities are mandatory for production.
- **Application/data services:** project registry, versioned file ingestion, malware scan, object-store snapshots, managed relational or equivalent durable store for tasks, risks, reports, approvals, alerts and delivery events. Business state must not depend solely on workflow execution-history retention.
- **Execution:** n8n workflows for ingestion, routing, specialists, scheduler, approval dispatch, notifier and evaluation. Select a supported managed Cloud or governed self-hosted edition based on licensing, queue/concurrency, external storage, security and support requirements. Do **not** assume self-hosted queue mode, enterprise source control or external secrets are available on every plan.
- **Integration:** approved read-only Jira/Trello credentials/API scopes; validated normalized schema; explicit project mapping; webhook signature verification when available; incremental sync and reconciliation.
- **Network/security:** encrypted ingress/egress, allowlisted provider endpoints, rate and file-size limits, secrets rotation, prompt/tool restrictions, logging redaction and retention policies.

### 19.1 Environment separation

Use distinct development, staging and production environments, credentials, data stores, notifier recipients and execution retention. Synthetic/sanitized inputs in development; production customer data only after security/privacy authorization. Versions and environment changes require controlled promotion and rollback; n8n native Git-backed environments/source control is **Business/Enterprise-plan gated**, while manual JSON import/export is feasible for the capstone.

### 19.2 Project-scoped data path

`Authenticate → authorize project membership → upload/import → scan + schema checks → version snapshot → retrieve scoped evidence → n8n router → specialist sub-workflow → deterministic response assurance → immutable draft → authorized reviewer → publish`; monitor path uses fresh snapshot and alert registry. Every record/blob includes `tenant_id`, `project_id`, `source_snapshot_id`, and provenance. Verify scope on each trusted retrieval, not by changing a dropdown label.

### 19.3 No-code-first boundary

n8n is the single **visual business and AI workflow designer**. Exact task counts, date windows, alert deduplication, and business approvals must be enforceable **outside the LLM**. If a required security or deterministic function cannot be guaranteed through n8n no-code primitives, procure an approved managed component or escalate a documented code exception; no-Code-node preference is subordinate to correctness and security.

## 20. Production data model and event contracts

| Entity | Required fields | Data controls |
|---|---|---|
| `Project` | tenant_id, project_id, name, owner, status | Unique ID; membership-scoped access |
| `ProjectMembership` | tenant_id, project_id, user_id, role | RBAC policy, least privilege |
| `SourceSnapshot` | snapshot_id, project_id, source_type, version/hash, ingested_at, freshness_status | Immutable approved version for each run |
| `Task` | project_id, task_id, sprint, status, due_at, assignee, snapshot_id | Unique per project/snapshot; deterministic normalized states |
| `Milestone` | project_id, milestone_id, due_at, linked_dependencies, snapshot_id | Dependency optional and explicitly known/unknown |
| `Report` | report_id, project_id, source_snapshot_id, workflow_version, draft_hash, state | Revision tracking; approved report immutable |
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

## 23. Security, privacy and abuse-resilience

- Threat model: prompt injection in uploads or connector rows, compromised API credentials, public form/webhook exposure, malicious file types, cross-tenant/project reference tampering, SSRF-capable HTTP tools, unauthorized email/Jira actions, approval-bypass attempts, poisoned snapshots, replay/duplicate events and leaking sensitive text in n8n execution history.
- Controls: file allowlist/size limits, malware quarantine, schema validation, least-privilege credentials, read-only Jira/Trello, outbound host allowlisting, disabled risky/unreviewed/community nodes where required, restricted AI tool access, bounded input/output sizes, data-minimized AI prompts and redacted execution logging.
- Authenticate at application gateway with enterprise IdP/OIDC/MFA as approved, verify business roles against project registry, separate user and service credentials, apply TLS, rotate keys and secure webhooks/forms. n8n editor/project RBAC does not alone provide application-user project isolation.
- Enforce provider data-classification approval, region/retention requirements, deletion policies and security incident response; do not claim blanket regulatory compliance from architecture alone.
- Formal red-team tests include malicious CSV instructions, cross-project ID manipulation, unintended tool invocation, approval link replay, secrets in workflow exports and compromised-notifier simulation.

Reference: https://docs.n8n.io/hosting/securing/security-audit/ and https://docs.n8n.io/workflows/sharing/.

## 24. Reliability, scalability and recovery

**Proposed release SLOs, not achieved:** request availability 99.9% monthly; report p95 ≤30 seconds under measured load; successful monitored runs ≥99%; alert persisted before dispatch 100%; RPO ≤1 hour and RTO ≤4 hours where business and hosting choices permit. Approval delay excluded from report-generation latency; provider outages reported separately.

- Validate n8n Cloud/hosted service sizing, concurrency, execution timeouts and retry behavior with expected load; production queue mode and worker replication only if the selected edition/deployment supports and is configured for them.
- Prevent accidental cost spikes using per-project quotas, capped LLM iterations, output budgets, timeouts, bounded retry/backoff and circuit-breaking on provider failure.
- Durable data store/outbox must survive workflow errors and execution-history pruning. Alert replay and report-publication retries use persisted idempotency keys to avoid duplicates.
- Backups/restore drills must cover n8n workflow definitions/credentials encryption configuration, data store, object snapshots, approvals, alerts and notifier outbox; validate actual achievable RPO/RTO.
- Degraded path: if model is down, exact deterministic counts and failure reason remain available; generated narrative is marked pending, not fabricated.

## 25. Observability, audits and operations

**Workflow observability:** n8n execution history, error workflows, manual/evaluation test results and configured external logs/metrics. Capture parent/child execution IDs, request ID, project and snapshot identifiers, routing branch, validator result, approval wait status, schedule successes/misses, source freshness, notification/outbox/delivery state, LLM call counts, token usage where exposed and end-to-end latency. Apply retention and redaction; n8n execution history is **not** a substitute for immutable approval or security audit.

**Optional AI tracing:** n8n's evaluations can compare known prompts/outputs; LangWatch/LangSmith may be assessed separately only with proven integration, plan support and privacy controls. No Langflow/Python runtime assumption remains in the chosen baseline.

**Runbooks:** webhook/form misuse, stale Jira/Trello imports, project data mismatch, model refusal/provider outage, status-count discrepancy, approval backlog, missed scheduled scan, duplicate alert storm, failure to notify owner, leaked credential and restore rollback. Assign operational owner and paging severity before production.

## 26. CI/CD, release engineering and governance

- Export actual n8n workflow JSON; review and redact credentials, tokens, webhook URLs where sensitive, private source data, sample prompts and pinned execution payloads. Store schemas, prompts, test fixtures (approved synthetic only), ADRs and release manifest in GitHub.
- For capstone: feature branch → reviewed PR → manual import into clean n8n environment → reconnect environment-specific credentials → run G3 and T1–T12 before tagging candidate release. A GitHub commit **is not an automatic n8n deployment**.
- For production: select release orchestration supported by the purchased n8n plan; native n8n Git source-control/environments feature is **Business/Enterprise gated**. If unavailable, use approved external deployment automation with verified exports/imports and separation of duties. Test deployed **published** version, not merely saved editor state.
- Pin workflow IDs/versions, model/prompt versions, data schema, rulebook and notification settings; promote dev → staging → prod through authorized approvals and rollback smoke checks.
- CI evaluation includes T1–T12 and P01–P15 applicable controls, license/security checks, negative project-isolation and prompt-injection tests; no production claim without reviewed evidence.

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
| P15 | Full T1–T12 baseline still passes against pinned source snapshots | Regressions, n8n executions and approved evaluation results |

No production acceptance may be inferred from design documentation alone. All P-series gates must be exercised in a production-like staging environment and signed off.

## 28. Production responsibility and sign-off (RACI summary)

| Domain | Accountable owner | Required approval/evidence |
|---|---|---|
| Product/ROI and CR-001 | Nexora sponsor / Product Owner | PRD v2.0 approval and monitoring policy |
| n8n platform orchestration | AI technical lead / platform team | ADR-014, G1–G7, supported edition |
| Identity + project RBAC | Application security owner | Threat model, P01/P02/P09/P10 |
| Data snapshots, privacy, retention | Source owner + privacy lead | Classification, authorized source sync |
| T1–T12, deterministic checks | QA/evaluation lead | Actual no-code counts, ground-truth ledger |
| Monitoring and escalation | Delivery operations owner | Rulebook, P05–P07, delivery proof |
| Reliability, incident response | SRE / hosting owner | Load, recovery, P11–P14 |
| Release authorization | CTO delegate + Product Owner | Signed P01–P15 evidence and go/no-go |

**Disposition:** n8n-only selected as proposed platform baseline, not proof of production. ADR-014 and PRD extension approvals, G1–G7 data/test evidence, P01–P15 security/operational evidence and named owners are required before rollout.

## 29. Version change log and retained design

**v2.2 (2026-10-25):** Reconciled source ZIP, exact task/risk/timeline CSV schemas, 25-row `5/3/1/16` fixture, complete T024 label and actual source status, Sprint 2/3 record cases, unranked risk rule, missing timeline calendar anchor, snapshot semantics, and explicit negative-test context isolation. Aligned to PRD v1.3, Architecture v2.2 and Charter v1.2. **No capstone scope expansion, agent removal, new FR mandate, or production certification.**



- **v1.1–v1.4:** Four logical specialist capabilities, grounded data controls, HITL, proactive-alert concept and T1–T12 baseline.
- **v2.0:** Production-target portal/auth, versioned persistence, resilient execution, secure ingress/egress, audited approval, CI/CD, alert lifecycle and P01–P15.
- **v2.1 (2026-10-25):** Rebased the existing v2.0 architecture to **n8n-only**: workflow router/sub-workflows, built-in ingestion/deterministic aggregation/scheduling, execution/evaluation strategy, revised risks/cost economics, and new ADR-014 comparing Langflow-only/hybrid/n8n-only. **All original business test cases, proactive-change controls and P01–P15 remain.**

This document replaces the v2.0 **implementation recommendation**, but does not overwrite historical artifacts or silently approve scope. If historical versions are cited, use v2.1 for selected platform design and v2.0 for historical Langflow analysis.

### References (verify in target n8n edition)

- n8n Summarize: https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.summarize/
- n8n Schedule Trigger: https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.scheduletrigger/
- n8n evaluations: https://docs.n8n.io/advanced-ai/evaluations/overview/
- n8n execution history: https://docs.n8n.io/workflows/executions/all-executions/
- n8n enterprise Git source control: https://docs.n8n.io/source-control-environments/create-environments/
- n8n security audit: https://docs.n8n.io/hosting/securing/security-audit/

