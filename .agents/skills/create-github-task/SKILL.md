---
name: create-github-task
description: >-
  Stages 1-2 of the task workflow: analyze a request, build a Task Brief
  (type, title, description, acceptance criteria, labels), and get the user's
  approval. Then hands off to publish-github-task. Use when the user asks to
  add, create, or file a task, ticket, issue, or backlog item in
  dosandk/SkillStack.
---

# Create GitHub task (orchestrator)

Multi-stage workflow: **Analyze -> Approve -> Publish**. This skill owns the first two stages and
delegates the third to `publish-github-task`. Brief format and rules: [reference.md](reference.md).

## Hard rules

- Never publish from this skill. Publishing belongs to `publish-github-task`.
- Never hand off a Brief the user has not explicitly approved.
- One Brief per issue. Propose a split when the request holds several tasks.
- No secrets, `.env` values, or private data in the Brief.
- This stage is read-only: it may run `gh` list and search commands, never `gh issue create`.

## Stage 1 - Analyze

1. Preflight: `gh auth status`. If `gh` is missing or not logged in, tell the user
   (`brew install gh`, `gh auth login`) and stop.
2. Read the request. Ask the user only for what you cannot infer: type, scope, or acceptance
   criteria. One question at a time.
3. Fill the Task Brief fields following the rules in `reference.md`, with `status: draft`.
4. Fetch labels with `gh label list -R dosandk/SkillStack` and map `type` to a label that exists.
5. Run the duplicate check from `reference.md`. If similar issues exist, show them and ask whether
   to continue.

## Stage 2 - Approve

Show the Brief as a readable draft (not raw JSON):

```markdown
**Type:** <type>   **Labels:** <labels or none>
**Title:** <title>

## Description
<description>

## Acceptance criteria
- [ ] <criterion>
```

Ask for approval. On edits, update the Brief and show it again. Only an explicit yes
("yes", "ok", "create") sets `status: approved`. Silence or questions do not count.

## Stage 3 - Hand off

Pass the approved Brief to `publish-github-task` and let it run. Do not run `gh issue create`
yourself. When it returns, relay the issue URL and number to the user.
