# lawois/.github

Default community files and **reusable workflows** for every repo under `lawois`.

## What's here

- `.github/workflows/security-reusable.yml` — reusable workflow. On pull requests and pushes it runs one quick job (gitleaks on the new commits, actionlint). The five full jobs (gitleaks, trufflehog, semgrep, trivy, actionlint) and run only on `workflow_dispatch` or with `full_scan: true`; CodeQL stays opt-in (`enable_codeql`) and, when enabled, runs on pull requests and pushes. The quick job takes a `runner` input (JSON) for repositories that have their own runners. The weekly full scan of every repository runs on the home lab, not in GitHub Actions: a `schedule` trigger in a caller runs nothing here. Callable from any repo. SARIF outputs are uploaded as workflow artifacts always, and additionally to the Security tab when `private_repo: false` (public repos or private repos with GHAS).
- `workflow-templates/security.yml` — surfaces a "Security baseline" entry in the **Actions → New workflow** picker for every repo.
- `examples/dependabot.yml` — copy into each repo at `.github/dependabot.yml`.

## How to adopt in a new repo

Either click **Actions → New workflow → Security baseline** in the repo UI, or drop this file in at `.github/workflows/security.yml`:

```yaml
name: Security
on:
  pull_request:
  schedule:
    - cron: '23 6 * * 1'
  workflow_dispatch:

jobs:
  security:
    uses: lawois/.github/.github/workflows/security-reusable.yml@main
    permissions:
      contents: read
      security-events: write
      actions: read
      pull-requests: read
    with:
      private_repo: true          # false for public repos (enables SARIF → Security tab)
      enable_codeql: false        # true for public repos
      codeql_languages: '[]'      # e.g. '["go","python","javascript-typescript"]'
```

Then copy `examples/dependabot.yml` to `.github/dependabot.yml` and trim ecosystems as needed.
