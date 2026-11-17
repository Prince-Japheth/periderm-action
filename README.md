# Periderm GitHub Action

Scans every pull request and leaves one comment with the result, updated on each push:

> **✓ 142 checks passed | 2 warnings**
> - Missing Terms of Service
> - Unbounded API retry on `/api/ai`

Periderm runs dozens of deterministic checks for the things that break apps after they ship: leaked
secrets, routes without auth, silent failures, runaway API cost loops, missing legal pages, broken
SEO and accessibility gaps. New checks ship every week.

## Setup

Add `.github/workflows/periderm.yml`:

```yaml
name: Periderm
on:
  pull_request:
  push:
    branches: [main]

permissions:
  contents: read
  pull-requests: write

jobs:
  periderm:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: Prince-Japheth/periderm-action@v1
        with:
          # Public repos: leave this out. Private repos: add a Team token as a repo secret.
          token: ${{ secrets.PERIDERM_TOKEN }}
```

That's it. Open a pull request and Periderm comments within a minute or two.

## Pricing

- **Public repositories:** free. No account, no token.
- **Private repositories:** Team ($29/month) or Business ($99/month, adds the team dashboard).
  Create a CI token in **Dashboard → Integrations → GitHub** and save it as the `PERIDERM_TOKEN`
  repository secret.

## Inputs

| Input | Default | What it does |
| --- | --- | --- |
| `token` | — | Periderm CI token. Needed for private repositories and to sync results to your dashboard. |
| `fail-on` | `critical` | Fail the job at this severity or above: `critical`, `high`, `medium` or `never`. |
| `comment` | `true` | Post or update one PR comment. |
| `working-directory` | `.` | Folder to scan (monorepos). |
| `github-token` | `github.token` | Token used to post the comment. |
| `version` | `latest` | `periderm-cli` version. |

## What it does not do

Your source code never leaves the runner. The scan runs inside the job. With a token, only the
findings and scores are sent to your Periderm dashboard.

## Pull requests from forks

GitHub gives fork pull requests a read-only token, so the comment can't be posted. The job summary
and the inline annotations still show every finding.
