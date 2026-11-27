# MIRA — Q1–Q4 Capstone Evidence Guide

**Purpose:** Clear answers organized by the assignment themes visible in the repository's README and baseline data notes. Check these against the exact classroom question wording before submitting.

## Q1 — Ideation / opportunity selection

**Problem:** PMs/TPMs spend material effort reconciling plans, status, risks and stakeholder drafts across imperfect evidence. **Three candidate use cases:** (1) source-grounded weekly status reporting, (2) risk/blocker triage, (3) planning and stakeholder communications. **Selected:** MIRA, a five-capability assistant supported by authorized snapshots, to improve consistency while preserving human review. **Inputs:** project description, timeline, risk register, task board. **Outputs:** five structured reports. **Success criteria:** grounding/unsupported-claim rate, task reconciliation, precision of risk/blocker alerts, plus latency and stakeholder acceptance for pilot. See README §1 and data/Mira_Capstone_Data_Baseline.md.

## Q2 — Multi-agent implementation, observability and evaluation

MIRA uses n8n conversational routing, project discovery, a central Orchestrator and five specialist workflows (plan, risk, status, milestone, stakeholder update). Langfuse and the token collector log traces; the evaluator runs the 12 negative/positive baseline scenarios. Reports are draft-only until human review. **Evidence:** flows/, screenshots/, testing/ evaluation workbook, docs/MIRA_Step7_Architecture_Writeup.pdf. **Results:** Run 5814 historically records 12/12 overall passes; later full Run 6756 records 11/12 overall passes with T1 under review; standalone improved T8 Run 6826 passed. These are distinct runs.

## Q3 — Program Charter and governance

**Vision:** source-referenced project intelligence support for PMs/TPMs. **Scope:** training-project documents in approved snapshots, five specialist draft outputs, evaluation, traceability and review. **Out of scope:** unattended enterprise delivery, automated execution in live Jira/SharePoint/Trello/Confluence, production access-control certification. **Stakeholders:** PM/TPM users, project sponsors, information owners, security/privacy and reviewer/approver. **Phases:** ingestion/source validation → routing/specialists → evaluation and HITL review → controlled demo → proposed pilot. **Risks:** stale/incomplete data, model hallucinations, wrong-scope routing, incomplete usage tracking, sensitive exports. **Mitigations:** clarification gates, source constraints, scoped counts, human approval and access review. Reference the full existing charter: docs/MIRA_Project_Charter_v1.3_As_Built.md.

## Q4 — Reflection, prompt refinement, privacy and next steps

**Worked:** constrained specialist roles, source-cited draft reports, T2/T4/T6/T9 clarification handling, Sprint 3 scoping, T10 reconciliation (25 tasks: 5 Done, 3 In Progress, 16 To Do, 1 Blocked). **Corrections:** explicit no-fabrication rules, improved blocker/milestone distinction, scoped status logic, qualified qualitative risk ranking and transparent date gaps. **Not resolved:** lack of independently audited grounding across every source, T1 later Judge finding, all-in token/cost capture, no authoritative calendar start or task-to-milestone links. **Privacy:** publish sanitized flows and training data; scan `.eml` approval files, screenshots and Git history for private addresses, tokens and links before public release. **10-user pilot:** select 10 authorized PMs/TPMs, train on synthetic project data, record baseline manual versus MIRA drafting+review time, trace quality/latency/cost, monitor error handling, obtain opt-in feedback, expand only after governance gates.

### Submission note

This guide is a **derived summary** of materials visible in the uploaded repository. It does not pretend to quote the exact instructor wording of Q1–Q4, which was not included in the user message.
