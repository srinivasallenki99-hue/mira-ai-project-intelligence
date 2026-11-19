# MIRA — Executive Architecture Overview

**AI-Powered Project Intelligence Assistant | Nexora → ABCDE Ltd. AI Adoption Program**  
**Status:** Implemented n8n capstone prototype; **not production certified**  
**Architecture:** n8n + OpenAI + Langfuse | **Baseline:** v2.3, November 19, 2026

## The program problem

PMs and TPMs must produce plans, risk assessments, task-status views and stakeholder communications from inconsistent project artifacts. This manual process can introduce reporting delays, inconsistent formats and undocumented assumptions. MIRA addresses these problems by turning an approved, versioned project snapshot into **reviewable and source-traceable project intelligence drafts**.

## Solution at a glance

| Business question | Architectural answer |
|---|---|
| **How does a user start?** | An n8n conversational interface resolves intent, project selection and evidence sufficiency. |
| **How are facts protected?** | Ingestion normalizes source files into n8n Data Tables; the Orchestrator retrieves an explicitly selected snapshot; validators reconcile schema, task counts and evidence. |
| **What expertise is available?** | Five specialists: Planner, Risk Assessor, Status Reporter, Milestone Tracker and Stakeholder Update. |
| **What happens when the request is vague?** | Clarification gate returns questions without invoking specialist-report workflows (demonstrated in T2/T4/T6/T9). |
| **How are results assessed?** | T1–T12 Evaluation Harness, LLM-as-Judge, n8n execution evidence, Langfuse telemetry and token/cost estimates. |
| **Who approves publication?** | Gmail-based review in `Mira_12_ReviewDispatcher`; approved reports can be rendered as PDFs and distributed. |

## Evidence for evaluators

- **15 exported n8n workflows**, including five specialists, data upload/retrieval, project discovery, chat, orchestration, evaluation, tracing and approval.
- **12/12 overall evaluation passes** in dated baseline run **#5814**, November 18, 2026. Passes are harness verdicts, not an independent accuracy certification.
- **T1 cost reference:** 13,479 collected generation tokens and approximately **$0.00731** of recorded estimated model usage for that example; total operating costs may be higher.
- **Source-data reconciliation:** Full reference board contains 25 tasks — 5 Done, 3 In Progress, 16 To Do, 1 Blocked; `T024` is the blocked security-review task.
- **Human governance:** Report review/approval and PDF-delivery nodes exist in the exported workflow; enterprise identity and immutable approval controls are not proven.

## TPM decisions and next gates

**Retain n8n-only orchestration** for the scored capstone: fewer platform handoffs and integrated workflow automation, while acknowledging that model cost is driven by actual usage. **Publish the GitHub evidence responsibly**: remove instance/credential IDs and private execution data; include the architecture, sanitized workflow exports, baseline workbook and selected trace screenshots.

For production, approve a separate roadmap for tenant-scoped access control, verified reviewer identity, durable audit, real-time data freshness, operational alert deduplication, SLOs, incident recovery and deployment governance. The capstone does not claim those controls are in service.

**Technical reference:** [MIRA Solution Architecture v2.3 — As-Built](MIRA_Solution_Architecture_v2.3_Executive_As_Built.md) — seven refreshed diagrams, 15-workflow inventory, source contracts, T1–T12 traceability, evaluation/cost evidence and a separate future-state production design.
