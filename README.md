# Shared Workflows

Reusable GitHub Actions workflows for my projects.

## Overview

This repository centralizes workflow logic that can be reused across multiple repositories. By keeping common automation in one place, each project can call the same validated workflows without duplicating YAML files.

## Repository structure

- .github/workflows/ - reusable workflow templates
- .gitattributes - repository line-ending normalization settings
- .gitignore - local macOS metadata exclusions

## Included workflows

### 1. Auto-approve owner PRs

The workflow in [.github/workflows/auto-approve-owner-pr.yml](.github/workflows/auto-approve-owner-pr.yml) approves pull requests created by the repository owner.

### 2. Node.js CI

The workflow in [.github/workflows/node-ci.yml](.github/workflows/node-ci.yml) provides a reusable Node.js build and test pipeline with inputs for:

- node version
- working directory
- install command
- build command
- test command

### 3. .NET CI

The workflow in [.github/workflows/dotnet-ci.yml](.github/workflows/dotnet-ci.yml) provides a reusable .NET restore/build/test pipeline with inputs for:

- .NET SDK version
- working directory
- restore command
- build command
- test command

### 4. Jest tests

The workflow in [.github/workflows/jest-tests.yml](.github/workflows/jest-tests.yml) runs a Node.js test suite and optionally uploads coverage artifacts.

### 5. Cypress E2E tests

The workflow in [.github/workflows/cypress-e2e-tests.yml](.github/workflows/cypress-e2e-tests.yml) runs Cypress browser tests and uploads failure artifacts.

### 6. Automated package updates

The workflow in [.github/workflows/auto-package-updates.yml](.github/workflows/auto-package-updates.yml) updates dependencies and opens a dependency PR.

## Example usage

### Auto-approve owner PRs

```yaml
name: Approve owner pull requests

on:
  pull_request:
    types: [opened, synchronize, reopened]

jobs:
  auto-approve:
    uses: <your-org>/SharedWorkflows/.github/workflows/auto-approve-owner-pr.yml@v1.0.0
```

### Node.js CI

```yaml
name: CI

on:
  pull_request:
    branches: [main]

jobs:
  checks:
    uses: <your-org>/SharedWorkflows/.github/workflows/node-ci.yml@v1.0.0
    with:
      working-directory: ./Source/ClientApp
      install-command: npm ci
      build-command: npm run build --if-present
      test-command: npm test
```

### .NET CI

```yaml
name: CI

on:
  pull_request:
    branches: [main]

jobs:
  checks:
    uses: <your-org>/SharedWorkflows/.github/workflows/dotnet-ci.yml@v1.0.0
    with:
      working-directory: ./Source
      dotnet-version: 9.0.x
      restore-command: dotnet restore
      build-command: dotnet build --no-restore
      test-command: dotnet test --no-build --verbosity normal
```

### Jest tests

```yaml
jobs:
  test:
    uses: <your-org>/SharedWorkflows/.github/workflows/jest-tests.yml@v1.0.0
    with:
      working-directory: .
      install-command: npm ci
      test-command: npm run test:ci
      coverage-path: coverage/
```

### Cypress E2E tests

```yaml
jobs:
  e2e:
    uses: <your-org>/SharedWorkflows/.github/workflows/cypress-e2e-tests.yml@v1.0.0
    with:
      working-directory: .
      install-command: npm ci
      start-command: npm start
      browser: chrome
```

### Automated package updates

```yaml
name: Dependency updates

on:
  schedule:
    - cron: '0 2 * * 1,3,5'
  workflow_dispatch:

jobs:
  deps:
    uses: <your-org>/SharedWorkflows/.github/workflows/auto-package-updates.yml@v1.0.0
    with:
      working-directory: .
      base-branch: main
      assignee: TheManOfTeel
      labels: dependencies
```

## Notes

- This repository is intentionally lightweight and focused on shared automation.
- Reusable workflows should stay generic and accept inputs instead of hardcoding repo-specific values.
- Update workflow files here when the shared logic needs to change across projects.
