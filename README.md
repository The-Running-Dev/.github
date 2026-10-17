# .github

Org-wide default community health files and [workflow templates](https://docs.github.com/en/actions/how-tos/reuse-automations/reuse-workflow-templates-and-actions) for The-Running-Dev.

## workflow-templates/

Suggested workflows for the Actions tab ("New workflow"). Each one is a short caller of a shared
workflow, so a repo gets the library's behaviour without copying it. GitHub suggests a template
first when the repo matches its `filePatterns`.

| Template | Calls | Suggested for |
| --- | --- | --- |
| Node CI | [`node-ci.yml@v1`](https://github.com/The-Running-Dev/GitHub-ActionTemplates/blob/main/docs/node-ci.md) | `package.json` |
| PowerShell CI | [`pwsh-ci.yml@v1`](https://github.com/The-Running-Dev/GitHub-ActionTemplates/blob/main/docs/pwsh-ci.md) | `*.psd1`, `*.ps1` |
| .NET | [`dotnet.yml@v1`](https://github.com/The-Running-Dev/GitHub-ActionTemplates/blob/main/docs/dotnet.md) | `*.sln`, `*.slnx`, `*.csproj`, `global.json`, `Directory.Build.props` |
| Docs | [`docs.yml@v1`](https://github.com/The-Running-Dev/GitHub-ActionTemplates/blob/main/docs/docs.md) | `docusaurus.config.*` |
| Pages | [`pages.yml@v1`](https://github.com/The-Running-Dev/GitHub-ActionTemplates/blob/main/docs/pages.md) | `index.html`, `vite.config.*` |
| Container | [`container.yml@v1`](https://github.com/The-Running-Dev/GitHub-ActionTemplates/blob/main/docs/container.md) | `Dockerfile*`, `compose.yml` |
| npm package | [`npm-package.yml@v1`](https://github.com/The-Running-Dev/GitHub-ActionTemplates/blob/main/docs/npm-package.md) | `package.json` |
| Claude review | [`claude-review.yml@main`](#githubworkflowsclaude-reviewyml-and-claude-mentionyml) | |
| Claude | [`claude-mention.yml@main`](#githubworkflowsclaude-reviewyml-and-claude-mentionyml) | |

The library templates need no inputs. Each links to its inputs page; add `with:` lines there as
needed. npm package passes an `NPM_TOKEN` secret, which can be dropped with trusted publishing.

The Claude templates need the [Claude GitHub App](https://github.com/apps/claude) on the repo
and a `CLAUDE_CODE_OAUTH_TOKEN` secret (repo or org level).

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
