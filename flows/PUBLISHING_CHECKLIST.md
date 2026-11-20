# Publishing and import checklist — 15 workflows

- [ ] Confirm 15 JSON files exist under `flows/`, and that every file opens as valid JSON.
- [ ] Review all original n8n workflows for credentials, private URLs, webhook routes, reviewer addresses, and execution/pinned data.
- [ ] Check code expressions and downloaded sample fixtures manually (automated scans cannot rule out all secrets).
- [ ] Inspect existing Git history and remove or rotate previously committed secrets; deleting a current file does not erase history.
- [ ] Confirm all `REMAP_WORKFLOW_ID` and `REMAP_DATA_TABLE_ID` references are replaced in your private test deployment before any replay.
- [ ] Rebind local OpenAI, Google, Langfuse, and n8n REST credentials. Do not re-export credentials into GitHub.
- [ ] Configure a fresh form path and n8n base URL and keep publicly addressable triggers disabled until reviewed.
- [ ] Review disabled SharePoint/Jira/Trello/Confluence connectors; do not describe them as operational.
- [ ] Upload test fixtures, Langfuse screenshots and test ledger only after checking privacy and usage rights.
- [ ] Reconcile GitHub documentation count (15) and the n8n workflow registry.
- [ ] Run ingestion, retrieval, T1–T12, negative tests, token collection, approval and downstream PDF smoke tests.
- [ ] Keep capstone implementation and unapproved production target separately identified.
