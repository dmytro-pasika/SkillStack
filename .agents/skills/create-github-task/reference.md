# Task Brief and publishing rules

Shared contract for `create-github-task` (stages 1-2) and `publish-github-task` (stage 3).

## Task Brief

Handoff object between stages. Never publish anything that is not an approved Brief.

```json
{
  "type": "feature",
  "title": "Add multi-stage create-github-task workflow",
  "description": "Why the task exists and what it changes, 2-5 sentences.",
  "acceptance_criteria": ["Verifiable outcome 1", "Verifiable outcome 2"],
  "labels": ["enhancement"],
  "status": "draft"
}
```

| Field                 | Rules                                                                      |
| --------------------- | -------------------------------------------------------------------------- |
| `type`                | `bug`, `feature`, or `docs`                                                |
| `title`               | Imperative, at most 70 characters, no trailing period                      |
| `description`         | What and why, 2-5 sentences, no secrets                                    |
| `acceptance_criteria` | 1-6 items, each independently verifiable                                   |
| `labels`              | Only labels that exist in the repo; may be empty                           |
| `status`              | `draft` until the user approves, then `approved`; publish requires `approved` |

One Brief is one issue. If the request holds several independent tasks, propose a split first.

## Type to label

| `type`    | Label           |
| --------- | --------------- |
| `bug`     | `bug`           |
| `feature` | `enhancement`   |
| `docs`    | `documentation` |

Check with `gh label list -R dosandk/SkillStack`. Never create new labels. Drop a label that does not
exist and mention it to the user.

## Issue body template

```markdown
## Description
<description>

## Acceptance criteria
- [ ] <criterion>
```

## Duplicate check

```bash
gh issue list -R dosandk/SkillStack --state all --search "<title keywords>" --json number,title,state
```

Show similar open or recent issues to the user and ask whether to continue. Do not decide alone.

## Publishing rules

- Repository is `dosandk/SkillStack`. Use only the `gh` CLI.
- Pass the body with `--body-file`, never inline, to keep markdown intact.
- Do not pass `--assignee`: contributors have read-only access and `gh` creates the issue before
  failing on the assignee, which leaves a duplicate on retry.
- Do not add the issue to the project board: the board picks up new issues automatically.
- After any error, search for the exact title before retrying. Never create a second issue.

## Link to the delivery process

Branches follow `{type}/{issue-number}-{slug}` and pull requests use `Closes #<n>`
(see `contributing.md`).
