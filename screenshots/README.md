# MIRA - Implementation Screenshots

This folder documents **actual n8n workflow captures** for the MIRA AI-Powered Project Intelligence Assistant. It preserves the screenshots as supplied and includes a [PDF visual walkthrough](MIRA_Implementation_Visual_Walkthrough.pdf).

## Screenshot index

| Order | File | Evidence |
|---|---|---|
| 01 | [01_MIRA_Data_Upload_Overview.png](01_MIRA_Data_Upload_Overview.png) | MIRA_Data_Upload - overview |
| 02 | [02_MIRA_Data_Upload_Expanded.png](02_MIRA_Data_Upload_Expanded.png) | MIRA_Data_Upload - expanded view |
| 03 | [03_MIRA_Get_Project_Data_Workflow.png](03_MIRA_Get_Project_Data_Workflow.png) | MIRA_Get_Project_Data - editor |
| 04 | [04_MIRA_Project_Selector_Workflow.png](04_MIRA_Project_Selector_Workflow.png) | Mira_Project_Selector - editor |
| 05 | [05_MIRA_Chat_Interface_Workflow.png](05_MIRA_Chat_Interface_Workflow.png) | Mira_10_Chat_Interface - editor |
| 06 | [06_MIRA_Orchestrator_Workflow.png](06_MIRA_Orchestrator_Workflow.png) | Mira_00_Orchestrator - editor |
| 07 | [07_MIRA_Orchestrator_Successful_Execution.png](07_MIRA_Orchestrator_Successful_Execution.png) | Mira_00_Orchestrator - execution |
| 08 | [08_MIRA_Get_Project_Data_Execution.png](08_MIRA_Get_Project_Data_Execution.png) | MIRA_Get_Project_Data - execution |
| 09 | [09_MIRA_Project_Selector_Execution.png](09_MIRA_Project_Selector_Execution.png) | Mira_Project_Selector - execution |
| 10 | [10_MIRA_Data_Upload_Execution.png](10_MIRA_Data_Upload_Execution.png) | MIRA_Data_Upload - executed overview |
| 11 | [11_MIRA_Planner_Agent_Execution.png](11_MIRA_Planner_Agent_Execution.png) | Planner Agent - successful execution, ID 6065, 38.457 s |
| 12 | [12_MIRA_Risk_Assessor_Agent_Execution.png](12_MIRA_Risk_Assessor_Agent_Execution.png) | Risk Assessor Agent - successful execution, ID 6015, 10.964 s |
| 13 | [13_MIRA_Status_Reporter_Agent_Execution.png](13_MIRA_Status_Reporter_Agent_Execution.png) | Status Reporter Agent - successful execution, ID 6033, 29.696 s |
| 14 | [14_MIRA_Milestone_Tracker_Agent_Execution.png](14_MIRA_Milestone_Tracker_Agent_Execution.png) | Milestone Tracker Agent - successful execution, ID 6041, 20.185 s |
| 15 | [15_MIRA_Stakeholder_Update_Agent_Execution.png](15_MIRA_Stakeholder_Update_Agent_Execution.png) | Stakeholder Update Agent - successful execution, ID 6050, 14.077 s |

| 16 | [16_MIRA_LLM_as_Judge_Evaluator_Execution.png](16_MIRA_LLM_as_Judge_Evaluator_Execution.png) | LLM-as-Judge Evaluator — execution #5885, succeeded in 6.69s |
| 17 | [17_MIRA_Langfuse_Trace_Exporter_Execution.png](17_MIRA_Langfuse_Trace_Exporter_Execution.png) | Langfuse Trace Exporter — execution #6069, succeeded in 594ms |
| 18 | [18_MIRA_Token_Usage_Collector_Execution.png](18_MIRA_Token_Usage_Collector_Execution.png) | Token Usage Collector — execution #5884, succeeded in 915ms |
| 19 | [19_MIRA_Review_Dispatcher_Execution.png](19_MIRA_Review_Dispatcher_Execution.png) | Review Dispatcher — execution #6070, succeeded in 42.232s |
| 20 | [20_MIRA_Evaluation_Harness_Workflow.png](20_MIRA_Evaluation_Harness_Workflow.png) | Evaluation Harness — editor workflow overview |

## Agent execution evidence

- **Planner:** ID 6065, Succeeded in 38.457s (historical execution; header shows Offline).
- **Risk assessor:** ID 6015, Succeeded in 10.964s.
- **Status reporter:** ID 6033, Succeeded in 29.696s.
- **Milestone tracker:** ID 6041, Succeeded in 20.185s.
- **Stakeholder update:** ID 6050, Succeeded in 14.077s. The Export Agent Observation node has a red indicator; investigate separately.
- A second Risk Assessor image was supplied but not added because it duplicates the same execution.


## Evaluation, observability and review evidence

- **LLM-as-Judge evaluator:** November 18 execution #5885 succeeded in 6.69s. The screenshot shows `Judge Project Grounding` linked to an OpenAI chat model and structured output parser. No particular assessment score or verdict is shown.
- **Langfuse trace exporter:** November 19 execution #6069 succeeded in 594ms. JavaScript prepares a POST HTTP request to a Langfuse endpoint; dashboard ingestion is not independently verified.
- **Token usage collector:** November 18 execution #5884 succeeded in 915ms. Visible path fetches execution details, extracts model usage, resolves a Langfuse trace, builds token OpenTelemetry data and exports generation usage. The screenshot does not show numeric token totals.
- **Review dispatcher:** November 19 execution #6070 succeeded in 42.232s. The approved reviewer branch includes document updates and PDF export. The declined branch appears in the design but is not executed in the supplied screenshot.
- **Evaluation harness:** Editor overview of `Mira_99_Evaluation_Harness` demonstrates workflow structure. An execution screenshot and pass/fail metric output have not yet been provided.

The original screenshots have been copied without image editing. The accompanying PDF contains interpretive captions separate from those originals.

## Important evidence details

- **Orchestrator:** November 19 execution ID `6063`, status `Succeeded`, duration `39.671s`.
- **Project data retrieval:** November 19 execution ID `6067`, status `Succeeded`, duration `81ms`; visible data path includes 7 input rows and 1 resulting item.
- **Project selector:** November 19 execution ID `6062`, status `Succeeded`, duration `1.528s`; 51 input rows enter the project-list builder.
- **Sample project-list output:** one example reports `ready_snapshot_count: 0` and `latest_ready_snapshot_id: null` for a capstone sample snapshot that lacks timeline and risks.
- **Other supplied record:** the `project_timeline.csv` record under snapshot `ABCDE-LTD-AI-ADOPTION-20261119210755-3e537c7f` contains 8 timeline rows. This is a different snapshot from the not-ready example.

A successful execution is not by itself evidence that agent output passed grounding, completeness, or quality checks. The PNGs are implementation evidence, not source-code exports.

## Recommended GitHub use

Keep these original screenshot files in the repository's `Screenshots/` directory. Link the PDF or individual PNG files from the repository's main README. Do not place API keys, connection credentials, or confidential project information in screenshots.
