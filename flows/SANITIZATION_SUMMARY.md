# Sanitization summary — 15-workflow package

The 12 previously sanitized exports were retained from the earlier reviewed package. **The three newly uploaded raw exports were independently sanitized and added.** This is a packaging/inspection step, **not an execution or security certification**.

## New exports — changes

| Change | Count |
|---|---:|
| credential_bindings | 4 |
| dataTableId | 2 |
| form_path | 1 |
| n8n_instance_url | 1 |
| top_id | 3 |
| top_meta | 3 |
| top_pinData | 3 |
| top_versionId | 3 |
| webhookId | 1 |
| workflowId | 2 |

**Preserved:** node names, types, connections, disabled/enabled node flags, Code node bodies (except URL substitutions), orchestration and parsing logic, and generic Langfuse endpoint.

**Sanitized:** node credential bindings, instance-specific Data Table IDs, linked workflow IDs, original n8n cloud hostname, webhook IDs/form path, top-level workflow/version/instance metadata, and pinned execution data.

**Requires manual review:** any embedded Code-node data, source fixtures, model prompts, Git history, and future added connectors.
