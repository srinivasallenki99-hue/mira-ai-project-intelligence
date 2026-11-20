# MIRA — AI-Powered Project Intelligence Assistant

An **n8n-based Agentic AI capstone prototype** combining PMP/TPM-informed project management with source-grounded multi-agent workflows, evaluation, and human approval.

## Capabilities
- Project planning
- Risk assessment
- Sprint/project status reporting
- Milestone tracking
- Stakeholder updates

## Engineering features
- Project discovery and snapshot-based grounding
- Intent-based multi-agent orchestration
- Positive and negative evaluation scenarios; LLM-as-judge assessment
- Evidence gap identification and task-count reconciliation
- Langfuse tracing and token-usage instrumentation
- Human review and approval; Google Docs/PDF/email workflow

## Evidence and limitations
A previously recorded evaluation batch reported **12/12 predefined scenarios passing in one run**. This is a bounded regression-test result, **not** evidence of zero hallucinations, quantified cost reduction, complete security assessment, or production readiness. This repository is a sanitized sharing template; it is **not directly runnable without reconfiguration**.

## Import and setup
1. Import JSON files from `flows/` into your own n8n instance.
2. Configure the required OpenAI, Google, Langfuse, and HTTP credentials locally.
3. Replace each `REMAP_*` configuration with your own workflow, data-table, spreadsheet, and document IDs.
4. Create data tables, templates, and sample project source records appropriate to your own environment. Some supporting workflows and data schemas are not included in this package.
5. Review expressions and linked workflow calls; rebind subworkflow references after import.
6. Test negative-path grounding, evaluations, approval emails, and PDF export before activating workflows.

Do not publish real reviewer addresses, private project data, credentials, service URLs, webhook links, or execution logs.

## Licensing
No license is included. All rights reserved unless a license is added by the repository owner.
