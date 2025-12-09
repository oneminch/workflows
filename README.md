# Workflows

A set of reusable GitHub Actions workflows and configuration.

- [`.github/workflows/auto-patch.yml`](.github/workflows/auto-patch.yml)
  - Bumps package version automatically from within a dependency update PR created by Dependabot.
- [`.github/workflows/push-tags.yml`](.github/workflows/push-tags.yml)
  - Pushes tags after merging Dependabot PRs.
  - Triggers the [`npm-publish.yml`](.github/workflows/npm-publish.yml) workflow if used.
- [`.github/workflows/npm-publish.yml`](.github/workflows/npm-publish.yml)
  - Triggered by pushed tags.
- [`.github/workflows/gh-pages-deploy.yml`](.github/workflows/gh-pages-deploy.yml)
  - Triggered by all push events to `main`.
- [`.github/dependabot-regular-updates.yml`](.github/dependabot-regular-updates.yml)
  - Dependabot bimonthly updates.


## Usage

### Auto Bump Version

```yaml
name: Auto Patch Version

on:
  pull_request:
    types: [opened]
    branches: [main]
  workflow_dispatch:

jobs:
  patch:
    if: github.event.pull_request.user.login == 'dependabot[bot]'
    uses: oneminch/workflows/.github/workflows/auto-patch.yml@v1
    permissions:
      contents: write
```

### Push Tags 

```yaml
name: Push Tags After Dependabot Merge

on:
  pull_request:
    types: [closed]
    branches: [main]
  workflow_dispatch:

jobs:
  push-tags:
    if: |
      github.event.pull_request.merged == true && 
      github.event.pull_request.user.login == 'dependabot[bot]'
    uses: oneminch/workflows/.github/workflows/push-tags.yml@v1
    permissions:
      contents: write
```

### Publish to NPM (with PNPM) 

```yaml
name: Publish to NPM

on:
  push:
    tags:
      - 'v*'
  workflow_dispatch:

jobs:
  publish:
    permissions:
      contents: read
      id-token: write
    uses: oneminch/workflows/.github/workflows/npm-publish.yml@v1
```

### Deploy to GitHub Pages

```yaml
name: Deploy to GitHub Pages

on:
  workflow_dispatch:
  push:
    branches:
      - main

jobs:
  deploy:
    uses: oneminch/workflows/.github/workflows/gh-pages-deploy.yml@v1
    # with:
    #   node-version: '22'
    #   build-command: 'pnpm run build --preset github_pages'
    #   artifact-path: './.output/public'
```

### Alternative (Patch & Tag)

```yaml
name: Patch & Tag

on:
  pull_request:
    types: [opened, closed]
    branches: [main]
  workflow_dispatch:

jobs:
  patch:
    if: |
      github.event.action == 'opened' && 
      github.event.pull_request.user.login == 'dependabot[bot]'
    uses: oneminch/workflows/.github/workflows/auto-patch.yml@v1
    permissions:
      contents: write
  
  push-tags:
    if: |
      github.event.action == 'closed' && 
      github.event.pull_request.merged == true && 
      github.event.pull_request.user.login == 'dependabot[bot]'
    uses: oneminch/workflows/.github/workflows/push-tags.yml@v1
    permissions:
      contents: write
```
