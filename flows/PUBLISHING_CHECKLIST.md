# Publishing checklist

- [ ] Keep the repository private during review.
- [ ] Confirm exported workflow JSON does not expose personal or customer data.
- [ ] Verify that credentials, webhook IDs, instance URLs, and cloud file IDs are not embedded in custom Code nodes or expressions.
- [ ] Inspect git history as well as current files if any unredacted export was previously committed.
- [ ] Ensure sample documents and screenshots contain no private addresses, reviewer email addresses, authorization tokens, or temporary approval URLs.
- [ ] Check all workflow imports and cross-workflow references after remapping identifiers.
- [ ] Document which dependencies are not packaged (data tables, ingestion workflows, Google Docs template, sample data, credentials).
- [ ] Do not claim the published template is plug-and-play or production ready.
- [ ] Review GitHub About/README wording to align with the current n8n implementation.
