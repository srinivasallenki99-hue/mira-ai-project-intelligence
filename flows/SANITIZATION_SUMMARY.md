# Automated sanitization summary

This package intentionally removes credential bindings, instance webhook IDs, instance-specific document and workflow IDs, known cloud document URLs, and reviewer email addresses. Imported workflows will need reconfiguration. Additional manual privacy/security review is required, including embedded text, custom JS, and git history.

- Google URL substitutions: 0
- cachedResultUrl: 21
- credentials: 25
- dataTableId: 7
- documentId: 7
- email substitutions: 1
- fileId: 2
- id: 12
- meta: 12
- n8n URL substitutions: 2
- pinData: 12
- sheetName: 7
- versionId: 12
- webhookId: 4
- workflowId: 29
