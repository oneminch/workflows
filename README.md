# Workflows

A set of reusable GitHub Actions workflows and configuration. They mostly automate working with dependency updates with Dependabot.

- [`.github/workflows/auto-patch.yml`](.github/workflows/auto-patch.yml)
  - Bumps package version automatically from within a dependency update PR created by Dependabot.
- [`.github/workflows/push-tags.yml`](.github/workflows/push-tags.yml)
  - Pushes tags after merging Dependabot PRs.
  - Triggers the [`npm-publish.yml`](.github/workflows/npm-publish.yml) workflow if used.
- [`.github/workflows/npm-publish.yml`](.github/workflows/npm-publish.yml)
  - Triggered by pushed tags.
- [`.github/dependabot-bimonthly-updates.yml`](.github/dependabot-bimonthly-updates.yml)
  - Dependabot bimonthly updates.


## Usage

### Simple

```yaml
name: Patch & Tag

on:
  pull_request:
    types: [opened, closed]
    branches: [main]

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

```yaml
name: Publish to NPM

on:
  push:
    tags:
      - 'v*'

jobs:
  publish:
    permissions:
      contents: read
      id-token: write
    uses: oneminch/workflows/.github/workflows/npm-publish.yml@v1
```

### Alternative

#### Auto Bump Version

```yaml
name: Auto Patch Version

on:
  pull_request:
    types: [opened]
    branches: [main]

jobs:
  patch:
    if: github.event.pull_request.user.login == 'dependabot[bot]'
    uses: oneminch/workflows/.github/workflows/auto-patch.yml@v1
    permissions:
      contents: write
```

#### Push Tags 

```yaml
name: Push Tags After Dependabot Merge

on:
  pull_request:
    types: [closed]
    branches: [main]

jobs:
  push-tags:
    if: |
      github.event.pull_request.merged == true && 
      github.event.pull_request.user.login == 'dependabot[bot]'
    uses: oneminch/workflows/.github/workflows/push-tags.yml@v1
    permissions:
      contents: write
```

#### Publish to NPM (with PNPM) 

```yaml
name: Publish to NPM

on:
  push:
    tags:
      - 'v*'

jobs:
  publish:
    permissions:
      contents: read
      id-token: write
    uses: oneminch/workflows/.github/workflows/npm-publish.yml@v1
```

