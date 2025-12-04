# Workflows

A set of reusable GitHub Actions workflows and configuration.

## Workflows

### Auto Bump Version

- Bumps package version automatically
- Workflow - [`.github/workflows/auto-bump-version.yml`](.github/workflows/auto-bump-version.yml)

```yaml
name: Auto Version Bump / Push Tags

on:
  pull_request:
    types: [opened, closed]
    branches: [main]

jobs:
  bump-version:
    if: |
      github.event.action == 'opened' && 
      github.event.pull_request.user.login == 'dependabot[bot]'
    uses: oneminch/workflows/.github/workflows/auto-bump-version.yml@v1
    permissions:
      contents: write
    with:
      node-version: "22"
      version-type: patch
  
  push-tags:
    if: |
      github.event.action == 'closed' && 
      github.event.pull_request.merged == true && 
      github.event.pull_request.user.login == 'dependabot[bot]'
    runs-on: ubuntu-latest
    permissions:
      contents: write
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
        with:
          fetch-depth: 0
      
      - name: Push tags
        run: git push --follow-tags
```

### Publish to NPM (with PNPM) 

[`.github/workflows/npm-publish.yml`](.github/workflows/npm-publish.yml)

```yaml
name: Publish to NPM

on:
  push:
    tags:
      - "v*"

jobs:
  publish:
    permissions:
      contents: read
      id-token: write
    uses: oneminch/workflows/.github/workflows/npm-publish.yml@v1
```

## Config

Dependabot bimonthly updates - [`.github/dependabot-bimonthly-updates.yml`](.github/dependabot-bimonthly-updates.yml)


