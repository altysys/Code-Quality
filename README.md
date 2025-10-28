# Code-Quality

Sharing Code Quality Best Practices

## Overview

This repository provides reusable GitHub Actions workflows and guidelines for maintaining high-quality code in **backend** and **frontend** projects. It automates linting, formatting, dependency management, builds, and basic tests, ensuring consistent standards across projects.

---

## Features

* **Backend Quality Checks**

  * Linting using [Ruff](https://github.com/charliermarsh/ruff)
  * Code formatting checks
  * Basic unit testing with [pytest](https://docs.pytest.org/)

* **Frontend Quality Checks**

  * Detects Node.js project type (npm, Yarn, pnpm)
  * Dependency installation with caching for faster CI runs
  * TypeScript type checks (if applicable)
  * Build verification
  * ESLint checks, including unused variables and imports

* **Full Stack Quality Check**

  * Combines backend and frontend checks
  * Frontend check can run even if backend fails
  * Provides a single entry point to validate the whole project

* **Reusable GitHub Actions Workflows**

  * Workflows can be triggered via `workflow_call` from other repositories
  * Supports push, pull_request, and manual triggers
  * Parameterized paths for backend and frontend projects
  * Customizable lockfile paths for dependency caching

---

## Usage

### Option 1: Separate Backend and Frontend Workflows

You can run backend and frontend quality checks individually by calling their respective workflows:

```yaml
name: Run Backend and Frontend Quality Checks

on:
  push:
    branches: ["Dev"]
  pull_request:
    branches: ["Dev"]
  workflow_dispatch:

jobs:
  backend-quality:
    uses: ./.github/workflows/backendQuality.yml
    with:
      branch: "Dev"
      backend_path: "backend"

  frontend-quality:
    uses: ./.github/workflows/frontendQuality.yml
    with:
      branch: "Dev"
      frontend_path: "Frontend"
      lockfile_paths: "Frontend/package-lock.json"
    needs: backend-quality
```

*Optional:* To run frontend checks **regardless of backend result**, add:

```yaml
    if: always()
```

---

### Option 2: Combined Full Stack Workflow

If you prefer a single entry point for both backend and frontend checks:

```yaml
name: Run Full Stack Quality Checks

on:
  push:
    branches: ["Dev"]
  pull_request:
    branches: ["Dev"]
  workflow_dispatch:

jobs:
  call-quality-checker:
    uses: ./.github/workflows/codeQualityChecker.yml
    with:
      branch: "Dev"
      backend_path: "backend"
      frontend_path: "Frontend"
      lockfile_paths: "Frontend/package-lock.json"
```

This workflow internally calls both backend and frontend workflows and ensures consistent full-stack validation.

---

### Inputs

| Input            | Description                                   | Default                        |
| ---------------- | --------------------------------------------- | ------------------------------ |
| `branch`         | Target branch for code checks                 | `"Dev"`                        |
| `backend_path`   | Path to backend project folder                | `"backend"`                    |
| `frontend_path`  | Path to frontend project folder               | `"Frontend"`                   |
| `lockfile_paths` | Path(s) to lockfiles for caching dependencies | `"Frontend/package-lock.json"` |

---

### Supported Package Managers

* **npm**: `package-lock.json`
* **Yarn**: `yarn.lock`
* **pnpm**: `pnpm-lock.yaml`

---

## Benefits

* Ensures consistent coding standards across projects
* Reduces manual effort for linting, testing, and builds
* Speeds up CI with dependency caching
* Easily extendable to add new quality checks

---

## Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/my-check`)
3. Add or improve workflows
4. Open a Pull Request

---

## License

This repository is licensed under the MIT License. See [LICENSE](LICENSE) for details.

Do you want me to do that?
