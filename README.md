# Reusable GitHub Actions Workflows

Reusable workflows for Node.js TypeScript projects with CI/CD automation.

## Workflows

### TypeScript CI (`typescript-ci.yml`)

Runs linting, build, and tests in parallel.

| Input | Default | Description |
|-------|---------|-------------|
| `working-directory` | `.` | Directory to run commands in |
| `node-version` | `24` | Node.js version |

### npm Publish (`npm-publish.yml`)

Builds and publishes packages to npm/GitHub Packages.

| Input | Default | Description |
|-------|---------|-------------|
| `registry` | `https://npm.pkg.github.com` | Registry URL |
| `tag` | `latest` | npm publish tag |
| `version-suffix` | `''` | Version suffix (e.g., `-dev-abc123`) |
| `check-version-exists` | `false` | Fail if version exists |

**Output:** `published-version`

### Create Release (`create-release.yml`)

Creates a Git tag and GitHub Release from package.json version.

**Outputs:** `version`, `tag`

## Usage

```yaml
# CI checks
jobs:
  ci:
    uses: rzavadzich/innowise-ci-workflows/.github/workflows/typescript-ci.yml@main

# Publish RC
jobs:
  publish-rc:
    uses: rzavadzich/innowise-ci-workflows/.github/workflows/npm-publish.yml@main
    with:
      tag: rc
      version-suffix: '-dev-${{ github.sha }}'
      check-version-exists: true
    secrets: inherit

# Final release
jobs:
  publish:
    uses: rzavadzich/innowise-ci-workflows/.github/workflows/npm-publish.yml@main
    with:
      check-version-exists: true
    secrets: inherit

  release:
    needs: publish
    permissions:
      contents: write
    uses: rzavadzich/innowise-ci-workflows/.github/workflows/create-release.yml@main
    secrets: inherit
```

## License

MIT
