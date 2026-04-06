# LintPage Action

Audit SEO, accessibility, and performance on preview deployments before they reach production.

This GitHub Action runs [LintPage](https://lintpage.com) audits on your pages and surfaces issues as GitHub annotations directly in your pull requests.

## Inputs

| Input | Description | Required | Default |
|---|---|---|---|
| `urls` | Space-separated URLs to audit | No | Auto-detected from deployment event |
| `config` | Path to LintPage config file | No | `.lintpagerc.json` (auto-detected) |
| `fail-on-warning` | Fail the check on warnings (not just errors) | No | `false` |
| `quiet` | Minimal output (counts only) | No | `false` |

## Usage

### With Vercel preview deployments

```yaml
name: LintPage
on: deployment_status

jobs:
  lintpage:
    if: github.event.deployment_status.state == 'success'
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v5
      - uses: lintpage/action@v1
```

### With explicit URLs

```yaml
name: LintPage
on: pull_request

jobs:
  lintpage:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v5
      - uses: lintpage/action@v1
        with:
          urls: 'https://staging.myapp.com https://staging.myapp.com/pricing'
```

### With local build

```yaml
name: LintPage
on: pull_request

jobs:
  lintpage:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v5
      - run: npm ci
      - run: npm run build
      - run: npm start &
      - run: npx wait-on http://localhost:3000
      - uses: lintpage/action@v1
        with:
          urls: 'http://localhost:3000'
```

### With custom config

```yaml
- uses: lintpage/action@v1
  with:
    urls: 'https://staging.myapp.com'
    config: './seo/lintpage.config.json'
    fail-on-warning: 'true'
```

## How it works

1. Installs the `lintpage` CLI
2. Detects target URLs from the action input or from `deployment_status` events (works automatically with Vercel, Netlify, etc.)
3. Runs the audit with `--format github` so issues appear as annotations on your PR
4. Exits with a non-zero code if critical issues (or warnings with `--fail-on-warning`) are found

## License

MIT
