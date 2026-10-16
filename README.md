# .github

Org-wide default community health files and [workflow templates](https://docs.github.com/en/actions/how-tos/reuse-automations/reuse-workflow-templates-and-actions) for The-Running-Dev.

## workflow-templates/code-review.yml

Adds Claude Code's automated review as a suggested GitHub Actions workflow in every repo's
Actions tab ("New workflow"). It runs on every non-draft pull request and posts findings as
inline PR comments.

To use it in a repo:

1. Install the [Claude GitHub App](https://github.com/apps/claude) on that repo.
2. Add an `ANTHROPIC_API_KEY` secret (repo or org level).
3. Actions tab → New workflow → pick "Code Review (Claude)" → commit it as
   `.github/workflows/code-review.yml`.

## .github/workflows/claude-review.yml and claude-mention.yml

Shared Claude Code workflows for SubZeroDev repos, kept here rather than in the public
GitHub-ActionTemplates library. They need the [Claude GitHub App](https://github.com/apps/claude)
on the calling repo and a `CLAUDE_CODE_OAUTH_TOKEN` secret.

They are two files because GitHub checks every nested job's permissions against the caller's,
even jobs that would be skipped. Review needs only read access to contents and issues;
answering `@claude` needs write access.

| Input | Workflow | Default | Meaning |
| --- | --- | --- | --- |
| `prompt` | both | | Prompt override |
| `model` | both | | Passed to Claude Code as `--model` |
| `skip-drafts` | review | `true` | Skip draft pull requests |
| `skip-bots` | both | `true` | Skip events raised by bots |

Pull request review:

```yaml
on:
  pull_request:
    types: [opened, synchronize, ready_for_review, reopened]

permissions: {}

jobs:
  claude-review:
    uses: The-Running-Dev/.github/.github/workflows/claude-review.yml@main
    permissions:
      contents: read
      pull-requests: write
      issues: read
      id-token: write
      actions: read
    secrets:
      CLAUDE_CODE_OAUTH_TOKEN: ${{ secrets.CLAUDE_CODE_OAUTH_TOKEN }}
```

`@claude` mentions (runs without `@claude` are skipped by the shared workflow):

```yaml
on:
  issue_comment:
    types: [created]
  pull_request_review_comment:
    types: [created]
  pull_request_review:
    types: [submitted]
  issues:
    types: [opened, assigned]

permissions: {}

jobs:
  claude:
    uses: The-Running-Dev/.github/.github/workflows/claude-mention.yml@main
    permissions:
      contents: write
      pull-requests: write
      issues: write
      id-token: write
      actions: read
    secrets:
      CLAUDE_CODE_OAUTH_TOKEN: ${{ secrets.CLAUDE_CODE_OAUTH_TOKEN }}
```
