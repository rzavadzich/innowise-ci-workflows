# Reusable GitHub Actions Workflows

This repository contains reusable GitHub Actions workflows designed for Node.js TypeScript projects. These workflows are part of the Innowise DevOps CI/CD pipeline.

## Workflows

### 1. TypeScript CI (`typescript-ci.yml`)
Reusable workflow for running code quality checks and tests in parallel.

- **Capabilities**: Executes linting, dual-module build (ESM/CJS), and unit tests in parallel jobs.
- **Inputs**:
  - `working-directory`: Directory to run commands in (default: `.`).
  - `node-version`: The version of Node.js to use (default: `24`).
- **Jobs**: `lint`, `build`, `test` (parallel)

### 2. npm Publish (`npm-publish.yml`)
Reusable workflow for publishing packages to GitHub Packages or npm registry with optional RC version support.

- **Capabilities**: Builds and publishes the package with configurable versioning and existence checks.
- **Inputs**:
  - `registry`: The registry URL to publish to (default: `https://npm.pkg.github.com`).
  - `tag`: The npm tag to use for publishing (default: `latest`).
  - `version-suffix`: Optional suffix to append to version (e.g., `-dev-abc123` for RC builds).
  - `check-version-exists`: Fail if version already exists in registry (default: `false`).
- **Outputs**:
  - `published-version`: The version that was published.
- **Secrets**: Uses `GITHUB_TOKEN` for authentication (inherited).

### 3. Create Release (`create-release.yml`)
Reusable workflow for creating Git tags and GitHub Releases from package.json version.

- **Capabilities**: Creates a `vX.Y.Z` tag and GitHub Release with auto-generated notes.
- **Outputs**:
  - `version`: The version from package.json.
  - `tag`: The git tag that was created (e.g., `v1.0.0`).
- **Secrets**: Uses `GITHUB_TOKEN` for authentication (inherited).

## Usage Examples

### TypeScript CI (PR checks)

```yaml
jobs:
  ci:
    uses: rzavadzich/innowise-ci-workflows/.github/workflows/typescript-ci.yml@main
    with:
      working-directory: '.'
```

### Publish Release Candidate

```yaml
jobs:
  get-sha:
    runs-on: ubuntu-latest
    outputs:
      short-sha: ${{ steps.sha.outputs.short }}
    steps:
      - uses: actions/checkout@v4
      - id: sha
        run: echo "short=$(git rev-parse --short HEAD)" >> $GITHUB_OUTPUT

  publish-rc:
    needs: get-sha
    uses: rzavadzich/innowise-ci-workflows/.github/workflows/npm-publish.yml@main
    with:
      tag: rc
      version-suffix: '-dev-${{ needs.get-sha.outputs.short-sha }}'
      check-version-exists: true
```

### Publish Final Release

```yaml
jobs:
  publish:
    uses: rzavadzich/innowise-ci-workflows/.github/workflows/npm-publish.yml@main
    with:
      tag: latest
      check-version-exists: true
```

### Create GitHub Release

```yaml
jobs:
  create-release:
    permissions:
      contents: write
    uses: rzavadzich/innowise-ci-workflows/.github/workflows/create-release.yml@main
    secrets: inherit
```

## Methodology

This project follows the **Repository Planning Graph (RPG)** methodology for structured, dependency-aware development.

## License

MIT
