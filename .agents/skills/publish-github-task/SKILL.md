---
name: publish-github-task
description: >-
  Stage 3 of the task workflow: create a GitHub issue in dosandk/SkillStack
  from an already approved Task Brief. Use only after create-github-task has
  produced a Brief with status approved, or when the user pastes an approved
  Brief and asks to publish it.
---

# Publish GitHub task

Final stage of **Analyze -> Approve -> Publish**. Brief format and publishing rules:
[../create-github-task/reference.md](../create-github-task/reference.md).

## Hard rules

- Publish only a Brief with `status: approved`. Otherwise stop and send the user back to
  `create-github-task`.
- Do not change the approved title, description, or criteria. Fix mistakes by returning to approval.
- Create exactly one issue. Never retry `gh issue create` before the existence check below.
- Do not pass `--assignee` and do not add the issue to the project board (see `reference.md`).

## Steps

1. Validate the Brief against `reference.md`: required fields present, `status` is `approved`.
2. Existence check, so a rerun cannot create a duplicate:
   ```bash
   gh issue list -R dosandk/SkillStack --state all --search "<exact title>" --json number,title,url
   ```
   If an issue with the exact title already exists, report its URL and stop.
3. Write the body to a temp file using the template in `reference.md`, then create the issue:
   ```bash
   gh issue create -R dosandk/SkillStack --title "<title>" --body-file "$BODY_FILE" [--label "<label>"]
   ```
4. If the command fails, run the existence check again before doing anything else. If the issue
   exists, report it. If not, show the error to the user and stop.
5. Remove the temp file.

## Report

Reply with the issue URL, the number `#<n>`, and a reminder that the board adds it automatically.
Suggest the branch name `{type}/<n>-<slug>` and `Closes #<n>` for the PR.
