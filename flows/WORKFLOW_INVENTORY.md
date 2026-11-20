# MIRA workflow inventory — 15 sanitized exports

| Workflow | GitHub file | Nodes | Disabled nodes |
|---|---|---:|---:|
| `MIRA_Data_Upload` | `flows/MIRA_Data_Upload.json` | 53 | 6 |
| `MIRA_Get_Project_Data` | `flows/MIRA_Get_Project_Data.json` | 4 | 0 |
| `Mira_00_Orchestrator` | `flows/Mira_00_Orchestrator.json` | 31 | 0 |
| `Mira_01_Planner_Agent` | `flows/Mira_01_Planner_Agent.json` | 13 | 0 |
| `Mira_02_Risk_Assessor_Agent` | `flows/Mira_02_Risk_Assessor_Agent.json` | 13 | 0 |
| `Mira_03_Status_Reporter_Agent` | `flows/Mira_03_Status_Reporter_Agent.json` | 13 | 0 |
| `Mira_04_Milestone_Tracker_Agent` | `flows/Mira_04_Milestone_Tracker_Agent.json` | 13 | 0 |
| `Mira_07_Evaluator — LLM-as-Judge` | `flows/Mira_07_Evaluator_LLM-as-Judge.json` | 4 | 0 |
| `Mira_08_Langfuse_Trace_Exporter` | `flows/Mira_08_Langfuse_Trace_Exporter.json` | 3 | 0 |
| `Mira_10_Chat_Interface` | `flows/Mira_10_Chat_Interface.json` | 16 | 0 |
| `Mira_10_TokenUsageCollector` | `flows/Mira_10_TokenUsageCollector.json` | 7 | 0 |
| `Mira_12_ReviewDispatcher` | `flows/Mira_12_ReviewDispatcher.json` | 14 | 0 |
| `Mira_99_Evaluation_Harness` | `flows/Mira_99_Evaluation_Harness.json` | 28 | 0 |
| `Mira_Project Selector` | `flows/Mira_Project_Selector.json` | 3 | 0 |
| `Mira_Stakeholder_Update_Agent` | `flows/Mira_Stakeholder_Update_Agent.json` | 13 | 0 |

## The three newly incorporated exports

- `MIRA_Data_Upload`: upload form, supported-file parsing, snapshot validation, Data Table write, and an optional evaluation-harness handoff. The SharePoint, Jira, Trello and Confluence data retrieval nodes are disabled.
- `MIRA_Get_Project_Data`: called with `snapshot_id`, `doc_kind`, and `file_name`; returns matching latest uploaded file rows from the project Data Table.
- `Mira_10_TokenUsageCollector`: fetches n8n execution metadata, extracts per-call model token usage when present, builds OTEL generation spans, and submits them to Langfuse; ingestion confirmation is not provided by this workflow.

## Rebinding dependency examples

- `MIRA_Data_Upload` → `MIRA_Project_Data` Data Table; `Mira_00_Orchestrator` node (disabled in current export) and `Mira_99_Evaluation_Harness` subworkflow.
- `MIRA_Get_Project_Data` → `MIRA_Project_Data` Data Table.
- `Mira_10_TokenUsageCollector` → local n8n Execution API and the configured Langfuse OTEL endpoint; needs n8n API and Langfuse credentials.
- Other 12 workflow exports contain similar `REMAP_*` references documented in each node selector.

The presence of a workflow export is **not** proof that every feature, integration or approval policy is active or tested.
