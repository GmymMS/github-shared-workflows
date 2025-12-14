# GitHub Shared Workflows

Reusable GitHub Actions workflows for security scanning and CI/CD.

## Available Workflows

### `security-scan.yml`

Comprehensive security scanning workflow with:

| Scanner | Description |
|---------|-------------|
| **Gitleaks** | Secret scanning - detects accidentally committed secrets |
| **Semgrep** | SAST - Static Application Security Testing |
| **Trivy (deps)** | Dependency vulnerability scanning |
| **Trivy (container)** | Docker image scanning |
| **Trivy (IaC)** | Infrastructure as Code scanning (Docker, Terraform) |
| **npm audit** | Built-in npm security audit |

## Usage

### Basic Usage

```yaml
name: Security Scan

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]
  schedule:
    - cron: '0 6 * * 1'  # Weekly on Monday
  workflow_dispatch:

jobs:
  security:
    uses: MaciekStrzelbicki/github-shared-workflows/.github/workflows/security-scan.yml@main
    with:
      image-name: 'my-app'
```

### Full Configuration

```yaml
jobs:
  security:
    uses: MaciekStrzelbicki/github-shared-workflows/.github/workflows/security-scan.yml@main
    with:
      # Container scanning
      dockerfile: 'Dockerfile.prod'      # default: 'Dockerfile'
      image-name: 'my-app'               # required

      # Node.js version
      node-version: '20'                 # default: '20'

      # Toggle individual scans (all default to true)
      run-gitleaks: true
      run-semgrep: true
      run-trivy-deps: true
      run-trivy-container: true
      run-trivy-iac: true
      run-npm-audit: true
```

### Skip Container Scanning (no Dockerfile)

```yaml
jobs:
  security:
    uses: MaciekStrzelbicki/github-shared-workflows/.github/workflows/security-scan.yml@main
    with:
      image-name: 'my-app'
      run-trivy-container: false
```

## Ignoring Vulnerabilities

Create a `.trivyignore` file in your repository root to ignore specific CVEs:

```
# Ignore specific vulnerabilities
CVE-2023-12345
CVE-2024-67890
```

## Artifacts

Each scan produces JSON artifacts that can be downloaded from the workflow run:

- `semgrep-results` - Semgrep SAST results
- `trivy-fs-results` - Trivy dependency scan results
- `trivy-container-results` - Trivy container scan results
- `npm-audit-results` - npm audit results

## Requirements

- Repository must have `package.json` and `package-lock.json` for npm-based scans
- Dockerfile must exist for container scanning (or disable with `run-trivy-container: false`)
