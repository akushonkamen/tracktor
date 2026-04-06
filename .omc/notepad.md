# Notepad

<!-- Auto-managed by OMC. Manual edits preserved in MANUAL section. -->

## Priority Context

<!-- ALWAYS loaded. Keep under 500 chars. Critical discoveries only. -->

## Working Memory

<!-- Session notes. Auto-pruned after 7 days. -->

### 2026-04-06 00:04

Fix all no-shadow lint errors (291 instances across 126 files)

- Extract detailed error information with file paths and line numbers
- Fix no-shadow errors in apps/web components (30+ files)
- Fix no-shadow errors in apps/space components (10 files)
- Fix no-shadow errors in packages/editor (20+ files)
- Fix no-shadow errors in packages/propel (10+ files)
- Fix no-shadow errors in packages/ui and other packages (10+ files)
- Fix no-shadow errors in store files (10+ files)
- Verify all fixes with oxlint --deny-warnings

### 2026-04-06 00:12

No-shadow lint fix progress:

- Fixed ~80 errors across 16 files
- Remaining: ~237 errors across ~120 files
- Major files fixed:
  - apps/web/core/hooks/use-issues-actions.tsx (38 errors → 0)
  - apps/web/core/components/issues/issue-detail/root.tsx (26 errors → 0)
  - apps/space/store/helpers/base-issues.store.ts (3 errors → 0)
  - apps/admin/store/instance.store.ts (1 error → 0)
  - Multiple smaller files

Common patterns fixed:

- projectId → targetProjectId
- workspaceSlug → targetWorkspaceSlug
- issueId → targetIssueId
- cycleId → targetCycleId
- moduleId → targetModuleId
- error → err
- props → componentProps/rootProps
- update → updateAction/updateData
- action → groupAction

Remaining work requires systematic batch processing of remaining files.

## 2026-04-06 00:04

Fix all no-shadow lint errors (291 instances across 126 files)

- Extract detailed error information with file paths and line numbers
- Fix no-shadow errors in apps/web components (30+ files)
- Fix no-shadow errors in apps/space components (10 files)
- Fix no-shadow errors in packages/editor (20+ files)
- Fix no-shadow errors in packages/propel (10+ files)
- Fix no-shadow errors in packages/ui and other packages (10+ files)
- Fix no-shadow errors in store files (10+ files)
- Verify all fixes with oxlint --deny-warnings

## MANUAL

<!-- User content. Never auto-pruned. -->
