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
