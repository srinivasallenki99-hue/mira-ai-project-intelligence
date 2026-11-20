# MIRA — Cost Analysis and Scaling Model

**Project:** MIRA — AI-Powered Project Intelligence Assistant  
**Scenario:** Nexora / ABCDE Ltd. AI Adoption (capstone training scenario)  
**Platform:** n8n multi-agent prototype  
**Evidence baseline:** Evaluation run **5814**, November 18, 2026  
**Status:** Prototype financial analysis; **not** production spending or an independently audited cost report.

## 1. Executive summary

MIRA automates draft project plans, risk assessments, status reports, milestone analyses, and stakeholder updates through intent-based routing and specialized n8n workflows. Cost is driven by LLM inference (routing, specialist agents, and optional LLM-as-Judge), retrieval, retries, observability, document generation, and hosting.

The November 18, 2026 evaluation harness recorded **12 of 12 passing baseline test verdicts** for run **5814**. The sum of **recorded per-test estimated model costs was approximately $0.03974** with **97,724 recorded generation tokens**. These are partial instrumented estimates: they are **not** a full invoice for the run or proof that all model calls were captured. A detailed project-plan test (T1) recorded **11,229 input + 2,250 output = 13,479 model tokens** and an estimated model cost of **$0.00731**.

The usage and ROI scenarios below are planning illustrations. They must not be presented as measured production costs or observed labor savings.

## 2. Cost scope and accounting boundaries

| Cost component | Implementation context | Measurement status / treatment |
|---|---|---|
| Specialist-agent model inference | Planner, Risk Assessor, Status Reporter, Milestone Tracker, Stakeholder Update | Partial token and cost data from harness; per-agent averages not yet established |
| Chat classification and routing | Chat Interface, Orchestrator, Project Selector | May incur model calls even for clarification-only responses; not fully attributed |
| LLM-as-Judge evaluations | Separate evaluator / baseline harness | Distinguish judge tokens/cost from generation costs; coverage incomplete |
| Retrieval and data storage | n8n Data Tables, document ingestion, snapshot retrieval | Measure separately; do not assume zero |
| n8n runtime | Cloud or self-hosted instance | Plan-dependent subscription / compute / execution expenses, not measured here |
| Langfuse / telemetry | Trace exporter and token-usage collector | Infrastructure or service costs and telemetry coverage not fully measured |
| Google integrations | Docs, Drive, Gmail / approval workflow | Account, quota, storage, and integration costs not fully measured |
| Review, exception handling, maintenance | Human approval, source validation, corrections, operational support | Include in fully loaded operational model |

**Important:** A workflow execution marked *Succeeded* does not demonstrate complete cost attribution, valid ground truth, or successful delivery by every optional side-effect node.

## 3. Observed evaluation evidence

| Evidence | Value | Interpretation |
|---|---:|---|
| Evaluation harness run | **5814** | November 18, 2026 baseline |
| Harness verdicts | **12 / 12 passed** | One predefined batch; not a universal accuracy guarantee |
| Recorded generation tokens, batch | **97,724** | Partial captured token total |
| Sum of recorded per-test estimated model costs | **≈ $0.03974** | Partial model-generation cost estimates; not a fully loaded bill |
| T1 input tokens | **11,229** | Detailed plan scenario |
| T1 output tokens | **2,250** | Detailed plan scenario |
| T1 total tokens | **13,479** | Input + output |
| T1 recorded estimated model cost | **≈ $0.00731** | Single-run reference for illustrative scaling |

### Evidence interpretation and limitations

- Some agent outputs have **PARTIAL** validation because source documents do not supply every owner, likelihood, severity, mitigation status, milestone calendar date, or freshness indicator.
- The independent judge reported warnings that certain factual-grounding checks were not fully verified against original source documents.
- Clarification-path tests **T2, T4, T6, and T9** did not record specialist/tool execution; this **does not mean** the chat/router incurred zero model tokens or cost.
- Chat runs and evaluation-harness runs are different evidence sets and may have different IDs and wording.
- Token estimates should be reconciled to provider bills and trace-level usage before being treated as actual expenditure.

## 4. Agent-level execution evidence and measurement plan

The following elapsed times are from **individual selected screenshots**, not per-agent averages or percentiles. Costs for those executions were not established from those screenshots.

| Specialist | Screenshot execution ID | Selected execution duration | Input tokens | Output tokens | Estimated model cost |
|---|---:|---:|---|---|---|
| Planner | 6065 | 38.457 s | To collect | To collect | To calculate |
| Risk Assessor | 6015 | 10.964 s | To collect | To collect | To calculate |
| Status Reporter | 6033 | 29.696 s | To collect | To collect | To calculate |
| Milestone Tracker | 6041 | 20.185 s | To collect | To collect | To calculate |
| Stakeholder Update | 6050 | 14.077 s | To collect | To collect | To calculate |

For each execution, capture: workflow and run ID, UTC timestamp, model/provider, input tokens, cached input tokens if separately billed, output tokens, retries, routing tokens, judge tokens, latency, source snapshot ID, pricing version/date, and estimated versus billed cost. Avoid adding duplicated tokens from parent and child traces.

**Per-call formula (using model-specific published rates applicable to the run):**

```text
estimated_model_cost = (uncached_input_tokens / 1_000_000 * input_price_per_1M)
                     + (cached_input_tokens   / 1_000_000 * cached_input_price_per_1M)
                     + (output_tokens         / 1_000_000 * output_price_per_1M)
```

If cache categories are unavailable, use the appropriate uncached rate for all input tokens as a labeled conservative assumption. Keep routing, judge calls, retries, and other distinct calls in the same end-to-end cost ledger without double-counting.

## 5. Usage projections — illustrative scenarios

**Reference rate:** $0.00731 per report, equal to the **single observed T1 plan-generation estimate**. This is *not* a validated blended rate for all five agents. The multipliers below are hypothetical sensitivities, **not** a measured overhead ratio.

| Scenario | PMs / TPMs | Reports per user per workday | Workdays per month | Monthly reports | Rate assumption | Partial projected model cost / month |
|---|---:|---:|---:|---:|---|---:|
| Low usage | 10 | 1 | 20 | 200 | 1× T1 | **$1.46** |
| Reference | 10 | 5 | 20 | 1,000 | 1× T1 | **$7.31** |
| Sensitivity | 10 | 5 | 20 | 1,000 | 2× T1 | **$14.62** |
| Higher-cost sensitivity | 10 | 5 | 20 | 1,000 | 5× T1 | **$36.55** |

```text
monthly_reports = users × reports_per_user_per_day × working_days_per_month
partial_projected_model_cost = monthly_reports × 0.00731 × hypothetical_multiplier
```

These scenarios omit subscription/hosting charges, any uncaptured model calls, judge and routing calls, document generation, human review, storage, and maintenance. They must not be used as full deployment budgets.

### Fully loaded monthly cost model

```text
monthly_fully_loaded_cost = captured_generation_model_cost
                          + uncaptured_router_and_judge_cost
                          + retry_and_error_handling_cost
                          + n8n_subscription_or_compute
                          + Langfuse_or_observability_cost
                          + Google_and_other_integration_cost
                          + storage_and_network_cost
                          + maintenance_and_human_review_cost
```

**Budget worksheet for a pilot:** Replace every `TBD` using actual quotes, invoices, or time-tracked labor rates; do not treat TBD as zero.

| Monthly line item | Pilot estimate | Basis / evidence needed |
|---|---|---|
| Generation model inference | Scenario above | Recompute using representative agent mix |
| Router / judge / retries | TBD | End-to-end traces and provider charges |
| n8n hosting/subscription | TBD | Plan or compute bill |
| Langfuse telemetry | TBD | Plan and event volume |
| Google / document / email integration | TBD | Account or infrastructure charges |
| Storage, backups, monitoring | TBD | Usage and infrastructure bill |
| Operator and reviewer time | TBD | Time study and loaded hourly rates |
| **Fully loaded total** | **TBD** | Sum only after each required line is populated |

## 6. Business value and ROI — illustrative, not measured

The project scenario describes substantial time spent manually drafting plans and status updates, but an actual before/after MIRA time study has **not** been completed. To illustrate a future evaluation:

| Example assumption | Value |
|---|---:|
| PMs / TPMs | 10 |
| Reports per person per week | 2 |
| Planning weeks per month | 4 |
| Total reports per month | 80 |
| **Assumed net time saved per report** | **0.5 hour** |
| **Potential gross hours saved** | **40 hours/month** |
| Assumed fully loaded labor value | $60/hour |
| **Illustrative gross labor-value equivalent** | **$2,400/month** |

```text
gross_hours_saved = 10 users × 2 reports/week × 4 weeks × 0.5 hours/report = 40 hours
gross_labor_value = 40 hours × $60/hour = $2,400/month
net_monthly_benefit = measured_gross_labor_value − measured_fully_loaded_monthly_cost
ROI_percent = net_monthly_benefit / measured_fully_loaded_monthly_cost × 100
```

**Do not calculate a defensible ROI percentage until fully loaded costs and net time saved are measured.** Net time saved must include preparing sources, checking citations, editing errors, obtaining approval, and handling exceptions. The hypothetical 80-report ROI scenario is **separate** from the 1,000-report model-cost reference scenario and should not be combined without normalizing volume.

### Suggested pilot measurement

1. Recruit up to 10 PMs / TPMs for a controlled, authorized pilot using synthetic or approved project data.
2. Time identical reporting tasks manually and with MIRA, including review/correction and approval.
3. Log report type, input document size, snapshot readiness, corrections, completion time, and user acceptance.
4. Reconcile n8n executions, Langfuse traces, token collector records, and provider charges for each case.
5. Report median, p95 latency, token-cost distribution by agent, verified grounding/error rate, task-count reconciliation, and net time saved with sample sizes.

## 7. Cost and performance optimization priorities

- **Intent-based routing:** Invoke only relevant specialists, avoiding unnecessary multi-agent calls.
- **Evidence scoping:** Retrieve only the authorized project snapshot and required document sections.
- **Context management:** Trim redundant source text without removing material facts or citations.
- **Cache suitable repeated inputs:** Measure cache savings only when billing confirms them.
- **Tier models by task:** Evaluate smaller models for simple transformations, subject to quality tests.
- **Targeted evaluation:** Run the independent harness on an appropriate regression schedule instead of assuming every chat request needs a full judge pass.
- **Retry policy:** Log retries explicitly; enforce bounded retry limits and track errors.
- **Telemetry reconciliation:** Resolve missing usage events, parent/child duplicates, and model-label gaps.

Each optimization must be checked against grounding quality, safety, task reconciliation, and reviewer acceptance—not just token totals.

## 8. Risks, assumptions, and honest claims

| Statement | How to present it |
|---|---|
| “12/12 tests passed” | Correct only as the reported verdict for baseline run 5814 |
| “$0.03974 total cost” | Incorrect if it implies complete spending; say **sum of recorded per-test partial model-cost estimates** |
| “$0.00731/report” | Single T1 reference, not a measured average across all report types |
| “MIRA saves 40 hours/month” | Hypothetical example only until measured in an actual pilot |
| “Production-ready” | Not established; project is a capstone prototype |
| “Zero hallucination” | Not established; judge grounding warnings and missing source fields remain |

## 9. Evidence and repository links

- [Main project README](../README.md)
- [Screenshots index](../Screenshots/README.md)
- [LLM-as-Judge screenshot](../Screenshots/16_MIRA_LLM_As_Judge_Evaluator_Execution.png)
- [Langfuse Trace Exporter screenshot](../Screenshots/17_MIRA_Langfuse_Trace_Exporter_Execution.png)
- [Token Usage Collector screenshot](../Screenshots/18_MIRA_Token_Usage_Collector_Execution.png)
- [Evaluation Harness workflow screenshot](../Screenshots/20_MIRA_Evaluation_Harness_Workflow.png)
- Baseline run 5814: link the actual evidence workbook and per-test outputs from `testing/` when committed; do not invent a filename or link.

**Disclosure:** All costs in this file are either (a) recorded **estimated model costs** from the specified capstone evaluation, or (b) explicitly labeled planning assumptions. No contemporary API price-sheet claim is made without a provider and pricing-date verification.
