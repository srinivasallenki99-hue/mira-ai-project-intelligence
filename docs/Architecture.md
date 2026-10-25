# Mira – Solution Architecture

**Version:** 1.3 (proposed)  
**Status:** Architecture design for no-code MVP  
**Based on:** Mira PRD v1.1 and Technical Design v1.2  
**Stack:** Langflow · OpenAI GPT-4o-mini · LangWatch · GitHub

> **Scope clarification:** Users initiate data imports through Mira's user interface. The MVP supports file uploads; Jira/Trello read-only import is an optional future integration. A unified Mira UI, deterministic calculations, and an embedded approval control are **target architecture capabilities** and must be validated or implemented; they are not assumed to be built-in Langflow Playground functions.

## Diagram 1 — Target logical solution architecture

```mermaid
flowchart TB
  U["PM / TPM / Delivery Manager"] --> UI["Mira User Interface<br/>Choose project · Upload/import · Request output · Review"]

  subgraph IN["UI-initiated source ingestion"]
    direction LR
    FILE["File upload<br/>TXT / CSV / supported PDF<br/>MVP"]
    EXT["Jira / Trello connector<br/>Read-only · Optional / Future"]
  end
  UI --> FILE
  UI -. "Authorize and import" .-> EXT
  FILE --> ING["Ingestion and validation<br/>Parse · Schema check · Normalize"]
  EXT -.-> ING
  ING --> CTX["Approved project context<br/>project_id · source/version · imported_at"]

  UI --> REQ["Request validation<br/>Intent · project · sprint · as-of date"]
  REQ --> ROUTER["Mira Orchestrator<br/>Langflow Agent + Run Flow tools"]

  subgraph AGENTS["Langflow specialist flows"]
    direction LR
    P["Planner"]
    R["Risk Assessor"]
    S["Status Reporter<br/>+ Stakeholder Drafts"]
    M["Milestone Tracker"]
  end
  ROUTER --> P
  ROUTER --> R
  ROUTER --> S
  ROUTER --> M

  CTX --> DATA["Shared data services<br/>Project-scoped retrieval · Source references"]
  DATA --> CALC["Deterministic processing<br/>Task counts · Sprint filters · Date comparisons"]
  DATA --> P
  DATA --> R
  CALC --> S
  CALC --> M

  P --> VALID["Output assurance<br/>Schema + source checks · Missing-data handling"]
  R --> VALID
  S --> VALID
  M --> VALID
  VALID --> DRAFT["Generated DRAFT<br/>Markdown / JSON · Evidence references"]
  DRAFT --> HITL{"Human review / approval<br/>PM or TPM"}
  HITL -- "Approve" --> OUT["Approved report<br/>Download / share manually"]
  HITL -- "Reject or request edits" --> REVISE["Revise draft / regenerate with feedback"]
  REVISE --> VALID
  HITL -- "Hold" --> HOLD["Draft remains unpublished"]

  LLM["OpenAI GPT-4o-mini<br/>Model inference"] -.-> ROUTER
  LLM -.-> AGENTS
  OBS["LangWatch<br/>Traces · Evaluation · Usage"] -.-> ROUTER
  OBS -.-> AGENTS
  GH["GitHub<br/>PRD · TDD · Mermaid · Flow JSON · Test evidence"] -. "Versioned exports (manual MVP)" .-> AGENTS

  classDef ui fill:#dbeafe,stroke:#2563eb,color:#1e3a8a;
  classDef data fill:#dcfce7,stroke:#15803d,color:#14532d;
  classDef agent fill:#ede9fe,stroke:#7c3aed,color:#4c1d95;
  classDef gate fill:#ffedd5,stroke:#c2410c,color:#7c2d12;
  classDef out fill:#fce7f3,stroke:#be185d,color:#831843;
  class U,UI,REQ ui;
  class FILE,EXT,ING,CTX,DATA,CALC data;
  class ROUTER,P,R,S,M,LLM agent;
  class HITL,HOLD,REVISE gate;
  class VALID,DRAFT,OUT out;
```

**Architectural boundaries:** The project files and connectors are alternative inputs to the same ingestion/normalization process; they are not independent agent interfaces. The approved project context is a logical data service, not necessarily a new database. LangWatch and GitHub are supporting systems, not part of the report approval decision. Only validated, reviewed outputs may be distributed externally.

## Diagram 2 — Data input, report generation and human-in-the-loop approval

```mermaid
flowchart TD
  START(["PM / TPM opens Mira UI"]) --> PROJECT["Select project (MVP: ABCDE)"]
  PROJECT --> INPUT{"Choose data source"}
  INPUT -- "Upload" --> UP["Upload project files<br/>Description / timeline / risks / task board"]
  INPUT -. "Optional connector" .-> CON["Authorize read-only Jira / Trello import"]
  UP --> CHECK["Validate file type, fields, statuses and dates"]
  CON -.-> CHECK
  CHECK --> GOOD{"Data valid and sufficient?"}
  GOOD -- "No" --> ERR["Display errors / missing fields<br/>Request corrected data"]
  ERR --> INPUT
  GOOD -- "Yes" --> SNAP["Create project data snapshot<br/>Project ID + source version + timestamp"]
  SNAP --> ASK["User requests plan / risk / status / milestone report"]
  ASK --> ROUTE["Mira Router chooses specialist flow"]
  ROUTE --> EVIDENCE["Retrieve scoped source records<br/>Calculate exact counts and dates as needed"]
  EVIDENCE --> GEN["Specialist agent generates report draft"]
  GEN --> VERIFY{"Output schema and source checks pass?"}
  VERIFY -- "No" --> FIX["Return validation failure or request missing information"]
  FIX --> ASK
  VERIFY -- "Yes" --> PREVIEW["Show DRAFT to PM / TPM<br/>With source references and warnings"]
  PREVIEW --> DECIDE{"Human approval decision"}
  DECIDE -- "Approve" --> APPROVE["Mark report APPROVED<br/>Record reviewer + version + decision"]
  APPROVE --> EXPORT["Export Markdown / JSON / approved document<br/>Manual email or Slack sharing"]
  DECIDE -- "Request changes" --> FEEDBACK["Provide review comments"]
  FEEDBACK --> GEN
  DECIDE -- "Reject / hold" --> STOP(["Not distributed"])
  EXPORT --> END(["Complete"])

  classDef entry fill:#dbeafe,stroke:#2563eb,color:#1e3a8a;
  classDef ingest fill:#dcfce7,stroke:#15803d,color:#14532d;
  classDef decision fill:#ffedd5,stroke:#c2410c,color:#7c2d12;
  classDef report fill:#ede9fe,stroke:#7c3aed,color:#4c1d95;
  classDef final fill:#fce7f3,stroke:#be185d,color:#831843;
  class START,PROJECT,ASK entry;
  class UP,CON,CHECK,SNAP,EVIDENCE ingest;
  class INPUT,GOOD,VERIFY,DECIDE decision;
  class ROUTE,GEN,PREVIEW,FEEDBACK,FIX,ERR report;
  class APPROVE,EXPORT,STOP,END final;
```

### Human-in-the-loop implementation note

The **approval decision is a required business checkpoint in the target design**. For the no-code capstone MVP, a PM/TPM may review the generated draft and approve it **manually** before sharing. Do not represent the `APPROVED` status, reviewer audit log, or automatic return-to-generation loop as implemented until you configure and test those functions. If a Langflow human-input/pause mechanism is used, confirm it works in the parent flow; do not assume a specialist invoked through Run Flow can pause for approval.

### MVP vs. future capabilities

| Capability | MVP design | Future enhancement |
|---|---|---|
| UI file ingestion | Required, subject to supported Langflow UI/upload flow | Full self-service upload portal |
| Jira/Trello | Not required; static CSV export can substitute | Authorized read-only connector |
| Data processing | Deterministic counts/date rules; feasibility gate required | Reusable managed data service |
| Human approval | Manual PM/TPM review before distribution | In-app approval workflow and audit trail |
| Distribution | User manually exports/shares approved output | Controlled integration with Docs, email or Slack |
| Versioning | Manual Langflow JSON export to GitHub | Automated deployment and synchronization |

### Acceptance evidence

- Uploaded dataset is parsed and validated, with source version recorded.
- Status reports reconcile to actual task-board counts; blocked task **T024** is identified.
- Specialist outputs are grounded in the selected project only.
- Reports remain drafts until PM/TPM approval.
- Rejected reports are not shared.
- All 12 capstone baseline tests and relevant execution traces are documented.
