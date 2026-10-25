# Mira – Technical Design Document (TDD)

**Version:** 1.1  
**Status:** Proposed – Conditional Architecture Approval  
**Date:** October 25, 2026  
**Project:** Mira – AI-Powered Project Intelligence Assistant  
**Client:** ABCDE Ltd.  
**Delivery Organization:** Nexora Pvt. Ltd.  
**Document Owner:** AI PM / TPM  
**Review Authority:** Enterprise Technical Architect / CTO  
**Reference:** Mira PRD v1.1  
**Implementation Approach:** No-Code First  
**Primary Technology:** Langflow, OpenAI GPT-4o-mini, LangWatch, GitHub

---

# 1. Executive Architecture Summary

Mira is a multi-agent AI project intelligence assistant intended to improve the efficiency and consistency of project management activities at Nexora.

The system will support planning, risk assessment, status reporting, milestone tracking, and stakeholder updates for ABCDE Ltd.'s six-month AI Adoption Project.

The solution will use a Router/Dispatcher architecture built primarily with Langflow visual components.

The architecture consists of:

1. Presentation and interaction layer.
2. Request validation and orchestration.
3. Four specialist AI agent services.
4. Shared project-data services.
5. Deterministic processing and validation controls.
6. AI inference through OpenAI.
7. Observability and evaluation.
8. GitHub-based version management.

**Fundamental design principle:** AI will generate narrative analysis and recommendations, while factual calculations and source validation will rely on deterministic processing.

The MVP is a controlled proof of concept, not a production-ready enterprise deployment.

# 2. Scope and PRD Alignment

## 2.1 In Scope

- Project description and CSV ingestion.
- Request classification and specialist routing.
- Project plan generation.
- Risk assessment.
- Sprint and project status reporting.
- Milestone and blocked-task tracking.
- Sprint-specific stakeholder update generation.
- Structured outputs.
- Missing-data and error handling.
- Observability and evaluation.
- GitHub workflow versioning.
- Human review before external distribution.

## 2.2 Out of Scope

- Automated Jira/Trello modification.
- Production SSO.
- Autonomous stakeholder communication.
- Fine-tuning.
- Enterprise-scale high availability.
- Multi-tenant access management.
- Live production logistics integration.
- Complex RAG infrastructure.

## 2.3 Requirements Coverage

| PRD requirements | Architecture component |
|---|---|
| FR-001 | Project Data Ingestion |
| FR-002 | Mira Orchestrator |
| FR-003–FR-004 | Planner Agent |
| FR-005–FR-006 | Risk Assessor |
| FR-007–FR-008 | Status Reporter and aggregation services |
| FR-009–FR-010 | Milestone Tracker and date evaluation |
| FR-011 | Status Reporter – Stakeholder Mode |
| FR-012–FR-013 | Validation and error-handling services |
| FR-014–FR-015 | LangWatch and evaluation workflow |
| FR-016 | Structured response formatter |
| FR-017 | Human review procedure |
| FR-018 | Deferred integration capability |

# 3. Architecture Principles

| Principle | Technical policy |
|---|---|
| No-code first | Use visual Langflow components where supported |
| Data integrity | Never use an LLM as the source of exact task counts |
| Source grounding | Each factual output must correspond to approved source data |
| Separation of concerns | Each specialist independently owns one capability |
| Modular design | Specialist flows are reusable through Run Flow |
| Clear contracts | Requests and outputs follow standard field definitions |
| Secure configuration | Secrets excluded from GitHub and workflow exports |
| Human accountability | Project commitments require PM/TPM approval |
| Observability | Executions and quality evidence are recorded |
| Reproducibility | Tested flow versions are stored in GitHub |

# 4. Architecture Style and Orchestration

## 4.1 Architecture Pattern

**Selected pattern:** Router/Dispatcher with independent specialist workflows.

The Orchestrator classifies the user's business intent and invokes the appropriate specialist.

Supported primary intents:

- PLAN
- RISK
- STATUS
- TRACKING
- CLARIFICATION

Stakeholder updates are handled as a STATUS output mode.

## 4.2 Routing Design

The MVP will use a Langflow Agent with four specialist Run Flow tools.

Each tool must have:

- A unique name.
- A clear capability description.
- Defined inputs.
- Defined outputs.
- Documented error behavior.

Tool selection is AI-based and must be evaluated.

If routing quality is insufficient, explicit intent classification and supported conditional routing will replace unrestricted tool selection.

## 4.3 Multi-Intent Requests

If a user requests multiple reports, the initial MVP may:

1. Execute supported specialist tools sequentially when available.
2. Combine their outputs only when source references remain intact.
3. Request clarification when the requested combination is ambiguous.

Multi-intent support is not a substitute for successful single-agent acceptance tests.

# 5. Logical System Components

## 5.1 User Interaction Layer

**Technology:** Langflow Playground.

Responsibilities:

- Capture user requests.
- Display agent responses.
- Support interactive testing.
- Display missing-information questions.

The Playground is sufficient for the capstone MVP. A custom web frontend is not required.

## 5.2 Request Validation Layer

Required logical inputs:

- User message.
- Project identifier.
- Requested capability, if known.
- Sprint or reporting-period filter, when applicable.
- As-of date for temporal analysis.

The system must not assume a default sprint when the requested sprint is essential to the answer.

For date-based requests, the effective evaluation date must be explicit, correctly supplied by the runtime, or recorded in the test fixture.

## 5.3 Mira Orchestrator

**Flow:** `Mira_00_Orchestrator`

Proposed components:

- Chat Input.
- Agent configured with GPT-4o-mini.
- Four Run Flow components in Tool Mode.
- Chat Output.

Responsibilities:

- Identify user intent.
- Select specialist flow.
- Pass user request context.
- Receive specialist output.
- Return the result without introducing unsupported facts.

The Orchestrator must not independently invent project status or task information.

## 5.4 Shared Project Data Layer

The project data layer is a logical shared service, not necessarily a separately deployed application.

It provides:

- Approved project documents.
- Structured task records.
- Structured risk records.
- Structured timeline records.
- Record filtering.
- Source identifiers.
- Data quality checks.
- Dataset version information.

Specialist flows may directly consume approved input files through their own Read File components in the initial MVP.

A separate shared-data subflow is optional if reusable preprocessing is demonstrated to be beneficial.

## 5.5 Deterministic Processing Layer

This layer handles operations that must be exact:

- Filtering tasks by sprint.
- Grouping tasks by status.
- Counting valid records.
- Identifying explicitly blocked tasks.
- Comparing valid due dates with the evaluation date.
- Selecting milestones within a date window.

These operations must not be delegated to an LLM as unverified arithmetic or logical inference.

Use built-in structured-data components where feasible.

If essential deterministic behavior cannot be achieved reliably within the no-code component set, activate the documented architecture decision gate before adding another processing platform.

## 5.6 Response Validation Layer

Response validation has two levels.

**Structural validation:**

- Required response fields exist.
- Correct output types are used.
- Status values are valid.
- The response follows the selected output contract.

**Factual validation:**

- Task IDs exist in the supplied dataset.
- Reported task counts match deterministic totals.
- Referenced milestones exist in the timeline.
- Existing risk identifiers match the risk register.
- Unsupported claims are rejected or labeled as recommendations.

A structured-output component alone does not satisfy factual validation.

# 6. Specialist Agent Design

## 6.1 Planner Agent

**Flow:** `Mira_01_Planner`

**Business capability:** Project plan generation.

Inputs:

- User request.
- Approved project description.
- Approved timeline.

Processing:

1. Load project description.
2. Load approved timeline.
3. Check for essential planning information.
4. Generate structured plan.
5. Validate documented phases and milestone references.
6. Return plan or missing-information response.

Output fields:

- project_name
- objectives
- scope
- phases
- activities
- deliverables
- milestones
- dependencies
- assumptions
- missing_information
- source_references

**Rules:**

- The six-month duration must be represented when using the ABCDE dataset.
- Existing timeline phases must not be silently replaced.
- Proposed activities must be distinguishable from approved activities.
- Vague requests must produce clarification.

**Baseline tests:** T1, T2, T9.

## 6.2 Risk Assessor

**Flow:** `Mira_02_Risk_Assessor`

Inputs:

- Project description.
- Approved risk register.
- Risk assessment request.

Processing:

1. Read relevant risk records.
2. Filter by project.
3. Identify requested categories or priorities.
4. Generate risk narrative and recommendations.
5. Verify existing risk identifiers.
6. Return structured assessment.

Output fields:

- risk_id
- category
- description
- impact
- mitigation
- priority
- ranking_basis
- recommendation
- source_reference

Risk prioritization must follow a documented method.

If the provided register does not contain sufficient scoring fields, the system must disclose that ranking is provisional or request a prioritization basis.

**Baseline tests:** T3, T4, T7.

## 6.3 Status Reporter

**Flow:** `Mira_03_Status_Reporter`

Inputs:

- Task-board CSV.
- Sprint or reporting period.
- User request.
- Optional audience.

Processing:

1. Load valid task records.
2. Apply requested sprint filter.
3. Calculate status counts.
4. Identify blockers and relevant upcoming work.
5. Prepare grounded report.
6. Validate totals and task names.
7. Return report.

Output fields:

- project_id
- reporting_period
- total_tasks
- done_count
- in_progress_count
- todo_count
- blocked_count
- tasks_by_status
- blockers
- upcoming_work
- overall_health
- health_rationale
- source_references

**Acceptance invariants:**

- Counts must reflect the selected task population.
- Total must reconcile to valid records.
- Sprint reports must not include unrelated sprints.
- Overall health cannot be stated as a confirmed fact without supporting criteria.
- Missing task data results in INSUFFICIENT_DATA.

### Stakeholder Communication Mode

When the user requests a stakeholder email, the Status Reporter shall produce a draft using the same validated reporting dataset.

Email generation must not cause automatic sending.

**Baseline tests:** T5, T6, T10, T12.

## 6.4 Milestone Tracker

**Flow:** `Mira_04_Milestone_Tracker`

Inputs:

- Approved timeline.
- Validated task board.
- As-of date.
- Requested time window.

Processing:

1. Load timeline and task records.
2. Identify explicitly blocked tasks.
3. Identify overdue incomplete tasks.
4. Select upcoming milestones.
5. Match relationships only when documented.
6. Explain risk indicators and recommended actions.
7. Return evidence-backed alerts.

Output fields:

- task_id
- task_name
- status
- due_date
- milestone_id
- milestone_name
- alert_type
- alert_reason
- recommended_action
- source_references

**Rules:**

- BLOCKED means explicitly blocked in source data.
- OVERDUE means incomplete with a due date before the as-of date.
- UPCOMING means the milestone falls within the requested date interval.
- AT_RISK requires supporting schedule or dependency evidence.
- Unknown dependencies must not be invented.

**Baseline tests:** T8, T11.

# 7. Data Architecture

## 7.1 Source Datasets

| Source | Business authority |
|---|---|
| project_description.txt | Project goals, approved scope and approach |
| project_timeline.csv | Approved phases and milestone dates |
| project_risks.csv | Registered risks and mitigations |
| sample_task_board.csv | Task statuses, assignments, sprints and due dates |
| eval_mira_inputs.txt | Evaluation requests |

## 7.2 Expected Data Volumes

The capstone describes:

- One project description.
- Eight timeline phases.
- Ten registered risks.
- Twenty-five task-board records.
- Twelve baseline test cases.

These are expected test-data characteristics. Actual records and field structures must be verified.

## 7.3 Canonical Entities

**Project:** project_id, name, scope, objectives, duration, source.

**Task:** task_id, task_name, sprint, status, assignee, due_date, source.

**Risk:** risk_id, category, description, impact, mitigation, priority_if_available, source.

**Milestone:** milestone_id_if_available, name, phase, due_date, documented_dependency_if_available, source.

## 7.4 Data Quality Rules

- Reject files that cannot be read.
- Identify missing required columns.
- Check duplicate task identifiers.
- Normalize recognized status labels without changing their meaning.
- Validate dates before comparisons.
- Preserve the original source record ID.
- Prevent mixing records from different datasets without explicit selection.
- Document the dataset version used for each test run.

## 7.5 Source Versioning

For the capstone, the source dataset version can be represented by a GitHub commit reference or approved dataset version label.

Each formal evaluation run shall identify the dataset version used.

GitHub versioning does not automatically update files already uploaded to Langflow. File changes must be reloaded and verified.

## 7.6 Retrieval Strategy

The MVP shall use direct file loading and structured filtering.

RAG/vector search is excluded initially because the datasets are small and the core evaluation requires exact record-level behavior.

# 8. Interface Contracts

## 8.1 Request Contract

Logical request fields:

- request_id: string
- project_id: string
- user_message: string
- intent: optional string
- sprint: optional string
- as_of_date: optional ISO-format date
- reporting_window: optional date range

The implementation must identify which fields are actually supported by the selected Langflow component connections.

## 8.2 Response Contract

Logical response fields:

- request_id
- project_id
- agent_name
- result_status
- response_data
- source_references
- missing_information
- warnings
- execution_metadata

## 8.3 Status Codes

- SUCCESS
- INSUFFICIENT_DATA
- VALIDATION_ERROR
- UNSUPPORTED_REQUEST
- SERVICE_ERROR

## 8.4 Contract Compatibility

The Orchestrator must be able to interpret each specialist's response.

Specialist flows must not use inconsistent names or incompatible result structures for shared fields.

Changes to response contracts require a documented version update and regression testing.

# 9. Langflow Physical Implementation Mapping

| Logical capability | Proposed visual component | Verification requirement |
|---|---|---|
| User interaction | Chat Input / Chat Output | Playground execution |
| Model inference | Agent / OpenAI | Valid model response |
| Subflow invocation | Run Flow, Tool Mode | Tool successfully invokes target flow |
| Text ingestion | Read File | Project description parsed |
| CSV ingestion | Read File, structured output | CSV rows accessible |
| Filtering | Data Operations | Sprint-specific records selected |
| Aggregation | Native data operations if supported | Exact counts demonstrated |
| Date calculations | Compatible visual processing component | Correct time-window results |
| Output shaping | Prompt Template / Structured Output | Required fields validated |
| Observability | LangWatch-supported integration | Real execution trace verified |
| Repository versioning | Langflow export + GitHub browser upload | Re-import successful |

**Architecture restriction:** Components that have not been proven capable of a required operation are provisional selections, not implemented controls.

# 10. Security and Privacy Architecture

## 10.1 Trust Boundaries

The solution crosses these boundaries:

1. User-to-Langflow interaction.
2. Langflow-to-OpenAI API processing.
3. Langflow-to-observability processing.
4. Developer-to-GitHub artifact publishing.

## 10.2 Credential Controls

- API keys must be stored in supported credential settings.
- Secrets must not be embedded in prompts.
- Secrets must not appear in GitHub commits.
- Exported flows must be reviewed before upload.
- Unnecessary external integrations must remain disabled.

## 10.3 Data Handling

- Use approved capstone data.
- Do not publish sensitive task descriptions or client information without authorization.
- Review trace payloads before enabling external observability on real company data.
- Avoid storing private content in public repositories.
- Document the distinction between synthetic test data and real organizational information.

## 10.4 Human Approval

The MVP uses a manual approval process:

1. Generate report.
2. PM/TPM reviews accuracy.
3. Correct unsupported or incomplete content.
4. Approve finalized artifact.
5. Share externally using the organization's approved communication process.

A built-in automated approval workflow is not required for the MVP.

# 11. Observability and Evaluation

## 11.1 Observability Platform

LangWatch is the preferred observability solution.

Integration compatibility shall be verified with the selected Langflow version and runtime before formal testing.

## 11.2 Trace Requirements

Capture when technically available:

- Request ID.
- Invoked specialist.
- Model identifier.
- Start/end timestamps.
- Latency.
- Token usage.
- Estimated cost.
- Failure information.
- Evaluation outcome.
- Workflow version.

## 11.3 Quality Evaluation

| Metric | Acceptance interpretation |
|---|---|
| Routing accuracy | Correct specialist selection |
| Grounding accuracy | Claims supported by source records |
| Count accuracy | Exact status-count reconciliation |
| Schema validity | Required response fields present |
| Date accuracy | Correct deadline/window evaluation |
| Hallucination count | Unsupported project facts |
| Latency | Measured execution duration |
| Cost per plan | Recorded token-based LLM cost |

## 11.4 Baseline Tests

The mandatory evaluation matrix contains T1–T12.

Each run must record:

- Test input.
- Expected behavior.
- Actual behavior.
- Pass/fail.
- Workflow version.
- Dataset version.
- Trace reference, where available.
- Defect reference, if failed.

No test shall be declared passed solely because the output looks professional.

# 12. Error Handling and Recovery

| Scenario | Expected behavior |
|---|---|
| Missing project file | INSUFFICIENT_DATA |
| Malformed source CSV | VALIDATION_ERROR |
| Invalid sprint | INSUFFICIENT_DATA or a documented no-record result |
| Unknown status value | VALIDATION_ERROR |
| Missing due date | Do not report overdue |
| Missing dependency | Do not invent milestone impact |
| Model service unavailable | SERVICE_ERROR |
| Agent tool unavailable | SERVICE_ERROR |
| Invalid JSON response | Validation failure or bounded retry |
| LangWatch unavailable | Identify missing external trace evidence |
| Unauthorized data request | Reject or require authorized access controls |

Retry policies must be bounded and must not allow repeated unlimited model calls.

# 13. Performance and Cost Architecture

## 13.1 Proposed Performance Target

Routine request p95 latency under 30 seconds in the test environment.

This is an objective to verify, not a guaranteed property.

## 13.2 Cost Control

- Use GPT-4o-mini initially.
- Avoid unnecessary specialist invocations.
- Restrict context to relevant source records.
- Track model usage per request.
- Compare evaluation quality against cost.
- Avoid uncontrolled retries.

## 13.3 Workload Assumptions

The MVP supports a small capstone dataset and limited concurrent use.

Production-scale capacity planning, high availability and disaster recovery are excluded.

# 14. Deployment and GitHub Versioning

## 14.1 Environment Model

**Development:** Langflow instance, approved test data, OpenAI credentials, GitHub.

**Evaluation:** Stable workflow exports, versioned datasets, LangWatch traces and test results.

**Production:** Outside MVP scope.

## 14.2 GitHub Repository Structure

```text
mira-ai-project-intelligence/
├── README.md
├── docs/
│   ├── PRD.md
│   ├── Technical_Design_Document.md
│   ├── Architecture.md
│   ├── ADR.md
│   └── Program_Charter.md
├── flows/
│   ├── Mira_00_Orchestrator.json
│   ├── Mira_01_Planner.json
│   ├── Mira_02_Risk_Assessor.json
│   ├── Mira_03_Status_Reporter.json
│   └── Mira_04_Milestone_Tracker.json
├── data/
├── prompts/
├── testing/
│   ├── Baseline_Test_Results.md
│   └── Evaluation_Metrics.md
└── screenshots/
```

## 14.3 Release Management

The tested GitHub main branch serves as the accepted configuration baseline.

Development changes should be made on feature branches.

A workflow change is eligible for merge when:

- The flow executes.
- Applicable tests pass.
- Required documentation is updated.
- Secrets are absent.
- Review is complete.

## 14.4 Rollback

Restore a previously accepted JSON flow from GitHub, re-import it into Langflow and re-run representative acceptance tests.

External credentials and file references may need to be reconfigured after import.

# 15. Architecture Decision Records

| ADR | Decision | Status |
|---|---|---|
| ADR-001 | Langflow is the primary visual agent platform | Selected |
| ADR-002 | Router/Dispatcher architecture | Selected |
| ADR-003 | GPT-4o-mini is the initial LLM | Selected |
| ADR-004 | Direct file retrieval; no vector database initially | Selected |
| ADR-005 | Deterministic data calculations required | Mandatory; mechanism pending |
| ADR-006 | LangWatch for observability | Selected; integration pending |
| ADR-007 | GitHub JSON export/import versioning | Selected |
| ADR-008 | Human approval before external distribution | Selected |
| ADR-009 | One authoritative dataset per formal test run | Selected |

# 16. Technical Proof-of-Concept Gates

## Gate 1: Environment Readiness

- Langflow starts successfully.
- OpenAI connectivity works.
- First sample flow runs.
- Workflow exports successfully.

## Gate 2: Data Readiness

- All required files available.
- Source schemas inspected.
- Record counts checked.
- Field mappings documented.

## Gate 3: No-Code Feasibility

- Sprint filtering works.
- Exact task aggregation works.
- T024 is identified as blocked.
- Date comparisons produce correct results.
- Missing data produces a controlled outcome.

**Failure of Gate 3 requires architecture remediation before full implementation.**

## Gate 4: Specialist Agent Acceptance

Each of the four specialist flows runs independently with its required source data.

## Gate 5: Orchestrator Integration

Requests invoke the intended specialist and return compatible outputs.

## Gate 6: Observability and Testing

- LangWatch trace verified.
- All 12 baseline tests executed.
- Critical grounding issues addressed.
- Cost and latency recorded.

## Gate 7: Submission Readiness

- Approved JSON flows in GitHub.
- PRD and TDD committed.
- Architecture diagram available.
- Test evidence documented.
- Screenshots captured.
- README includes reproduction steps.

# 17. Requirements Traceability

| PRD requirement group | System component | Evaluation |
|---|---|---|
| Ingestion and validation | Data Services | File and schema tests |
| Intent routing | Orchestrator | Routing tests |
| Project planning | Planner | T1, T2, T9 |
| Risk assessment | Risk Assessor | T3, T4, T7 |
| Task reporting | Status Reporter | T5, T6, T10 |
| Milestone intelligence | Milestone Tracker | T8, T11 |
| Stakeholder updates | Status Reporter | T12 |
| Grounding and error handling | Shared validation | Cross-cutting |
| Observability | LangWatch | Execution evidence |
| Output format | Response validation | Schema tests |

# 18. Open Architecture Risks

| ID | Open issue | Resolution owner |
|---|---|---|
| OA-01 | Actual input CSV field schemas | AI PM/TPM |
| OA-02 | Native no-code aggregation mechanism | Technical implementer |
| OA-03 | Date evaluation component | Technical implementer |
| OA-04 | Risk ranking basis | Product owner |
| OA-05 | Milestone-task relationship availability | Data owner |
| OA-06 | LangWatch runtime integration | Technical implementer |
| OA-07 | Source-reference validation mechanism | Technical architect |
| OA-08 | Multi-agent routing accuracy | QA/Evaluation owner |
| OA-09 | Workflow import/export compatibility | Technical implementer |

# 19. Definition of Done

An agent is considered complete only after:

1. Its Langflow workflow is configured.
2. Required data sources are available and validated.
3. Applicable business rules are implemented.
4. It produces the documented response contract.
5. Factual outputs are verified.
6. Applicable baseline tests pass.
7. Trace evidence or its documented limitation is captured.
8. JSON is exported and reviewed.
9. GitHub artifacts are committed.
10. Acceptance evidence is recorded.

# 20. Architecture Approval Recommendation

**Status: Conditionally approved for proof-of-concept development.**

The proposed Langflow multi-agent architecture is aligned with Mira PRD v1.1, subject to resolution of the critical implementation dependencies.

No-code data aggregation, date comparisons, source validation and observability integration must be demonstrated rather than assumed.

The first build milestone shall be an end-to-end Status Reporter workflow that reads the approved task-board CSV and produces accurately calculated task counts.

**Next artifact:** Langflow Detailed Component Design and Workflow Configuration Specification.

---

**End of Technical Design Document v1.1**
