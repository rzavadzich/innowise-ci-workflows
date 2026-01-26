# Reusable GitHub Actions Workflows

This repository contains reusable GitHub Actions workflows designed for Node.js TypeScript projects. These workflows are part of the Innowise DevOps CI/CD pipeline.

## Workflows

### 1. Node.js Setup (`node-setup.yml`)
Reusable workflow for consistent Node.js project setup with shared `node_modules` via artifacts.

- **Capabilities**: Configures Node.js environment, installs dependencies using `npm ci`, and uploads `node_modules` as an artifact for downstream jobs.
- **Inputs**:
  - `node-version`: The version of Node.js to use (default: `24`).
  - `working-directory`: Directory to run commands in (default: `.`).
- **Artifact**: Uploads `node_modules` as `node-modules-${{ github.run_id }}` for reuse by dependent jobs.

### 2. TypeScript CI (`typescript-ci.yml`)
Reusable workflow for running code quality checks and tests with shared dependency caching.

- **Capabilities**: Executes linting, dual-module build (ESM/CJS), and unit tests. Uses shared `node_modules` artifact to avoid redundant `npm ci` calls.
- **Inputs**:
  - `working-directory`: Directory to run commands in (default: `.`).
  - `node-version`: The version of Node.js to use (default: `24`).
- **Jobs**: `install` → `lint`, `build`, `test` (parallel, using shared artifact)

### 3. npm Publish (`npm-publish.yml`)
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

## Methodology

This project follows the **Repository Planning Graph (RPG)** methodology for structured, dependency-aware development.

## License

MIT
