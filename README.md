# Reusable GitHub Actions Workflows

This repository contains reusable GitHub Actions workflows designed for Node.js TypeScript projects. These workflows are part of the Innowise DevOps CI/CD pipeline.

## Workflows

### 1. Node.js Setup (`node-setup.yml`)
Reusable workflow for consistent Node.js project setup.
- **Capabilities**: Configures Node.js environment, restores npm cache, and installs dependencies using `npm ci`.
- **Inputs**:
  - `node-version`: The version of Node.js to use (default: `24`).
  - `cache-settings`: Cache settings for npm (default: `npm`).

### 2. TypeScript CI (`typescript-ci.yml`)
Reusable workflow for running code quality checks and tests.
- **Capabilities**: Executes linting, dual-module build (ESM/CJS), and unit tests.
- **Inputs**:
  - `project-path`: Path to the project root (default: `.`).
  - `test-command`: Command to execute unit tests (default: `npm test`).
  - `lint-command`: Command to execute linting (default: `npm run lint`).
  - `build-command`: Command to execute build (default: `npm run build`).

### 3. npm Publish (`npm-publish.yml`)
Reusable workflow for publishing packages to GitHub Packages or npm registry.
- **Capabilities**: Builds, packs, and publishes the package with a specified tag.
- **Inputs**:
  - `registry-url`: The registry URL to publish to (default: `https://npm.pkg.github.com`).
  - `package-path`: Path to the package directory (default: `.`).
  - `tag`: The npm tag to use for publishing (e.g., `latest`, `rc`).
- **Secrets**:
  - `NODE_AUTH_TOKEN`: The authentication token for the registry.

## Usage Example

To use these workflows in your repository, reference them in your `.github/workflows/*.yml` files:

```yaml
jobs:
  test:
    uses: rzavadzich/innowise-ci-workflows/.github/workflows/typescript-ci.yml@main
    with:
      node-version: 24
```

## Methodology

This project follows the **Repository Planning Graph (RPG)** methodology for structured, dependency-aware development.

## License

MIT
