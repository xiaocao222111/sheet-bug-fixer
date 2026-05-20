---
name: sheet-bug-fixer
description: Fix bugs from a Google Sheets or spreadsheet bug tracker where rows contain bug descriptions, optional screenshots, statuses, and remarks. Use when Codex is asked to process only new bugs, update statuses while working, modify the local codebase, verify fixes, and write concise results back to the sheet.
---

# Sheet Bug Fixer

## Overview

Use this skill for a spreadsheet-driven bug-fixing workflow. The sheet is the source of truth: claim only eligible rows, keep status transitions current, fix the local codebase, verify the result, and write back a clear status and remark for each bug.

## Workflow

### 1. Inspect the sheet

When the user provides a Google Sheets URL or spreadsheet file, read its metadata and headers before deciding what to work on.

- Identify columns by header text, not only by the user's stated column number. Real sheets often drift from the description.
- Find the bug description column, optional screenshot/preview column, status column, remarks column, and any date or owner columns.
- Read data validation for the status and remarks area when possible, then use the sheet's allowed status values.
- Process only rows whose status means new work, usually `新增`. Preserve every other row.
- Capture row numbers, bug descriptions, screenshot links or formulas, current status, and existing remarks.

### 2. Claim or decline rows

Before changing code for a bug, update that row's status to `修改中`.

- Batch status updates when claiming multiple clear bugs.
- If a row is vague, cannot be reproduced from the available information, is a product optimization rather than a bug, or lacks essential page/steps/expected behavior/data, mark it as `不解决` and write the specific question or blocker in the remarks column.
- Do not mark a row as `修改中` if no code investigation or fix will be attempted for it.
- If the sheet uses different allowed labels, use the closest validated equivalents and mention the mapping in the final response.

### 3. Fix the code

Treat the bug descriptions as requirements, then inspect the repository to find the relevant modules.

- Use fast code search such as `rg` first.
- Keep each fix scoped to the behavior described in the row.
- If several bugs touch the same module, make coherent changes without bundling unrelated refactors.
- Respect existing dirty worktree changes; never revert unrelated user edits.
- If a screenshot is essential and inaccessible, leave the row as `不解决` with a concrete note about what is missing.

### 4. Verify the fixes

Run the checks that match the blast radius.

- For frontend changes, run the app's build, typecheck, lint, or targeted tests as available.
- For backend changes, run compile/tests with the repo's expected runtime. If the default runtime fails due environment mismatch, retry with the appropriate configured runtime.
- For UI or navigation bugs, use a browser check when practical and the route/data can be reached.
- Run a whitespace or diff sanity check when useful, such as `git diff --check`.

### 5. Close the sheet rows

After verification, write the final status and remarks back to the same rows.

- Fixed rows: set status to `待验证`.
- Unfixed, unclear, or unreproducible rows: set status to `不解决`.
- Do not set rows to `已解决` unless the user explicitly asks; human validation owns that step.
- Write a concise remark for every touched row. For fixes, mention what behavior changed. For declined rows, state the blocker or exact question.
- Re-read the changed range after updating to confirm statuses and remarks landed correctly.

## Remark Style

Keep remarks short and operational.

- Fixed: `修复通知跳转类型，作业提交通知现在打开对应作业详情。`
- Fixed: `发布态隐藏富文本列表的冗余标记，避免正文前出现额外数字。`
- Not solved: `缺少具体页面、复现步骤、预期位置和截图；暂不按 bug 修改。`

## Final Response

Summarize the outcome in the user's language.

- List which rows were fixed and which were marked `不解决`.
- Mention the verification commands and whether they passed.
- Mention the main files changed.
- Note any residual risk or verification gap, especially if a browser/manual reproduction was not possible.
