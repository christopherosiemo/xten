# Repository working rules

Before each task, read [README](README.md), [PRODUCT](docs/PRODUCT.md), [ROADMAP](docs/ROADMAP.md), [ACCEPTANCE](docs/ACCEPTANCE.md) and [STATUS](docs/STATUS.md), plus any applicable local instructions.

## Scope and review

- Implement only the current authorised task, then stop. Do not start the next milestone implicitly.
- Inspect the actual repository state and existing code before changing files. Preserve unrelated work; make small, reviewable changes.
- Do not change architecture silently. Record proposed changes and obtain the appropriate human decision through the product change-control process.
- Do not lower acceptance requirements to make a task pass. Do not remove or skip failing tests merely to get green results.
- The implementing agent may mark completed work `READY_FOR_REVIEW`, but may not mark a milestone `ACCEPTED` on its own. Acceptance requires the designated human reviewer and evidence for the agreed scope.
- Return exact base and resulting revisions, actual branch, changed files, executed commands and outcomes. Never claim a command passed when it was not executed. Label checks `NOT_RUN`, `BLOCKED` or `NOT_APPLICABLE` where appropriate.

## Financial and engineering integrity

- Distinguish synthetic fixtures, emulators, mocked tests and real integration evidence. Record the scope and limitations of each.
- Do not use binary floating-point for authoritative monetary calculations. Follow explicit currency, conversion and rounding policies.
- Keep tenant isolation and source lineage explicit across APIs, storage, workers, exports and reports.
- Never fabricate utilisation, ownership, financial figures or savings evidence. Missing evidence remains unknown.
- Do not give AI independent execution or financial authority. Financial calculations must be deterministic and inspectable; changes require separate authorisation.
- When implementation begins, use supported, verified dependencies and pin reproducible versions. Verify support and compatibility at that time; these documents do not authorise scaffolding or installation.
- Start security and operational checks with each affected capability. M20 is a final broad assessment, not a reason to defer those checks.

## Data and authority

- Never put secrets or real client data in repository files or public logs. Sanitise remote URLs and evidence summaries; do not print environment values containing credentials.
- Use safe example configuration with placeholders and explicitly synthetic test fixtures. Keep private runtime data in designated ignored locations with appropriate external access and retention controls.
- `.gitignore` does not remove secrets from Git history and is not a complete data-security control. Do not treat an ignored path as authorisation to store customer data there.
- Do not provision paid resources or perform production actions without explicit authorisation.
- Do not modify repository visibility, licensing or access policies without explicit authorisation. Record unresolved administrative decisions instead.
- Keep read/collection access separate from execution access. Read-only authorisation never implies permission to change customer resources.

The current authorised task defines its permitted files and operations. Task-specific scope restrictions expire with that task unless deliberately incorporated into durable project documentation. Follow both these repository rules and the current task; if they conflict, stop and report the conflict rather than silently overriding either. Completing one task does not authorise work on the next task or milestone.
