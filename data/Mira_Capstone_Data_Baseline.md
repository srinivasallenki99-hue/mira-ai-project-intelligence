# Mira — Capstone Source Baseline and Change Record

**Status:** Proposed data reconciliation; technical design artifacts not yet approved or executed.  
**Canonical archive:** `Resources-20261025T215745Z-1-001.zip`  
**Change:** CR-DATA-001 (source-data baseline reconciliation), cross-referenced by PRD v1.3, TDD v2.2, Architecture v2.2 and Charter v1.2.  
**Platform:** n8n-only design proposed under ADR-014. Original capstone requirements and prompts remain unchanged.

## Verified source facts (offline inspection; not n8n test results)

- Description: ABCDE logistics/supply-chain AI adoption, use cases forecasting, routing, inventory, customer support; intended business goals 15% lower delivery delays and 10% lower operating costs.
- Timeline: 8 phases spanning Weeks 1–24; no start date, milestone calendar dates, dependency links or milestone IDs.
- Risks: R01–R10; narrative `impact` and `mitigation_strategy`; no priority/severity/likelihood score or ranking.
- Task board: `task_id,task_name,status,assignee,priority,sprint,due_date,labels,description` with **25 distinct IDs** and **Done 5, In Progress 3, Blocked 1, To Do 16**.
- T024: `Security review of AI infrastructure` / `Blocked` / Sprint 5 / `2026-05-26`; description says awaiting security team availability.
- Sprint 2: T004 Done, T005 Done, T006 In Progress.
- Sprint 3: T007 In Progress, T008 To Do, T009 To Do, T025 In Progress.
- Evaluation: original prompts T1–T12 exactly as supplied; T2/T4/T6/T9 are intentionally insufficient-data tests.

## Capstone narrative discrepancy and scoring requirements

The original assignment's explanatory *About the sample data* text says `3 Done`, `3 In Progress`, `1 Blocked` and the rest `To Do`; its CSV actually contains `5 Done`, `3 In Progress`, `1 Blocked`, `16 To Do`. T10 states counts **must match actual data**. Record the source discrepancy transparently, keep original inputs unchanged, and seek instructor/PM review before the G6 test-pass decision.

The assignment additionally requires Q1 ideation, Q2 a working multi-agent build plus a connected observability/evaluation tool, T1–T12 executions, a cost-per-plan calculation and at least 3 metrics, Q3 program charter, and Q4 reflection/trace findings/privacy/ten-user rollout proposal. GitHub JSON exports, README, architecture and canvas/output/trace screenshots are essential evidence.

## Controlled acceptance decisions

| Scenario | Required pass behavior | Decision / limitation |
|---|---|---|
| T5 | Exactly four Sprint 3 task IDs, accurate statuses | Confirmed against source |
| T7 | Actual registered risk IDs; no invented risk/score | Formal ranking absent; require disclosed illustrative selection or approved policy |
| T8 | Show T024 blocked; compare due dates using explicit `as_of_date` | Do not equate blocked task with confirmed milestone slip |
| T10 | Full-board `5 / 3 / 1 / 16` equals 25 | Source-verified input, runtime result pending |
| T11 | Actual week-based milestones only | Calendar window requires `project_start_date` or authorized week-to-date mapping; otherwise ask for clarification |
| T12 | Sprint 2 draft contains only T004–T006 | No external send without PM/TPM approval |

## Gate evidence

- **G2 — Source readiness:** data profiled; product owner / QA acceptance of ambiguity policies pending.
- **G3 — MIRA-001:** not yet executed; n8n must prove schemas, 25 records, exact counts, blocker and sprint filters, and error paths.
- **G6 — T1–T12:** not executed; store individual outputs, model/token/latency traces and pass/fail evidence.
- **G7 — Capstone delivery:** GitHub-reviewed workflow exports, documentation and reproduced test evidence required.
- **G8–G10 / P01–P15:** separate proposed production phase; not capstone acceptance gates.

## Change record

| Artifact | Prior | Revised | Business scope |
|---|---|---|---|
| PRD | v1.2 | v1.3 | Unchanged |
| Technical Design | v2.1 | v2.2 | Unchanged |
| Architecture | v2.1 | v2.2 | Unchanged |
| Project Charter | v1.1 | v1.2 | Unchanged |

**Approval:** Proposed, pending Product Owner, Architecture Reviewer and QA acceptance; no production sponsorship/authorization inferred.
