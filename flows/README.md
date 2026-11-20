# MIRA — AI-Powered Project Intelligence Assistant

**TPM capstone | n8n + OpenAI + Langfuse | 15 sanitized workflow exports**

MIRA converts approved project snapshots into draft plans, risks, status reports, milestone intelligence and stakeholder updates. The project demonstrates a conversational routing interface, reusable specialist workflows, source validation, evaluation, token telemetry and a human-review path.

## Workflow inventory

The `flows/` folder includes **15 exported definitions**. They are sanitized for portfolio or instructor review, not a turnkey or production deployment. See [`docs/WORKFLOW_INVENTORY.md`](docs/WORKFLOW_INVENTORY.md) for individual files, node counts and connector status.

## Capstone evidence and limitations

- A recorded prior evaluation regression (#5814) reported 12/12 overall test verdicts as passing. This is bounded automated acceptance, not independent proof of complete factual accuracy or production security.
- T2/T4/T6/T9 are designed as pre-specialist clarification paths; routing can still use model tokens.
- Generation token use and cost evidence includes collection gaps and an anomalous recorded zero estimated cost for T10; do not interpret the prior total as complete expenditure.
- SharePoint, Jira, Trello and Confluence retrieval nodes in `MIRA_Data_Upload` are **disabled**. They are not verified live source integrations.
- `Mira_10_TokenUsageCollector` distinguishes submission to Langfuse from confirmed ingestion.
- Model-reviewed and approval-path reports do not establish enterprise authorization or production-grade audit guarantees.

## Setup: importing the sanitized workflows

1. Import the JSON files into a **separate, authorized test n8n instance**.
2. Reconnect credentials for OpenAI, Google services, Langfuse and the n8n REST API where used. This archive intentionally contains no exported credential bindings.
3. Create your instance's n8n Data Tables (project data, project inventory, draft reports, evaluations, review records as relevant), and replace `REMAP_DATA_TABLE_ID` using the relevant table names in node descriptions.
4. Rebind every `REMAP_WORKFLOW_ID` via the n8n workflow selector. Import alone does not restore these links.
5. Set the `MIRA_Data_Upload` Form Trigger path and the instance URL in `Mira_10_TokenUsageCollector`; check environment URLs in the remaining previously sanitized workflows.
6. Supply approved or synthetic project files and source snapshot identifiers. Reconnect optional integrations only after permitted access and independent verification.
7. Run controlled ingestion, a project-data lookup, positive and negative chat cases, T1–T12 evaluations, telemetry verification, and approval/report smoke tests before activating routes.

Workflow node names, connection graphs, and Code-node logic are preserved from the provided exports. Credentials, resource IDs, the original instance address, and active webhook route identifiers are intentionally removed; **some workflow fields will require configuration before the imports can execute**.

## Documentation links (place the following documents in your repository)

- [`docs/Architecture.md`](docs/Architecture.md) — seven architecture diagrams, as-built and production-target separated.
- [`docs/Technical_Design_Document.md`](docs/Technical_Design_Document.md) — implementation contracts and governance.
- [`docs/PRD.md`](docs/PRD.md) — requirements FR-001–FR-018.
- [`docs/Project_Charter.md`](docs/Project_Charter.md) — sponsor/governance roadmap.
- [`testing/`](testing/) — actual test ledger, traces, evidence and cost notes; upload separately.

## Security notice

These are reviewed export templates, **not credential-bearing backups**. Before making any GitHub repository public, inspect the archive and all older commits, embedded Code-node strings, project fixtures, screenshots, and linked documents for secrets, names, private resource identifiers, data ownership constraints, and approval URLs. A fresh secret scan and manual review are required. A clean JSON file does not sanitize the repository's history.

No license is included. Do not imply reuse rights beyond permissions from the repository owner.
