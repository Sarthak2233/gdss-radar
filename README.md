# 🛰️ GDSS PR Collision Radar Action

[![GitHub Marketplace](https://img.shields.io/badge/Marketplace-GDSS%20Radar-blue?logo=github&style=flat-square)](https://github.com/marketplace/actions/gdss-pr-collision-radar)
[![Release](https://img.shields.io/github/v/release/sarthak2233/gdss-radar?style=flat-square&color=success)](https://github.com/sarthak2233/gdss-radar/releases)
[![License](https://img.shields.io/badge/License-MIT-green.svg?style=flat-square)](LICENSE)
[![Zero Token Bleed](https://img.shields.io/badge/Core-Deterministic%20$0%20Tokens-orange?style=flat-square)](#-privacy--zero-token-bleed)

> **Air Traffic Control for GitHub Pull Requests.**  
> Automatically detects cross-branch semantic collisions across all concurrently open PRs, tests candidate reconciliations in ephemeral sandboxes, and posts a single sticky Markdown radar report with 1-click **"Commit suggestion"** blocks.

---

## 🎯 The Problem

Traditional CI and GitHub Copilot operate with a fatal blind spot: **Single-PR Isolation**.

They only test **`PR #42 vs main`**. They have zero awareness of the 10 other active PRs your teammates are actively writing. If Branch A modifies a function signature in `auth.py`, and Branch B invokes the old signature in `billing.py`, Git reports **0 merge conflicts** and CI passes green—only for `main` to break in production upon landing.

**GDSS PR Collision Radar acts as an Air Traffic Control radar:** it continuously computes the pairwise interaction matrix ($\Omega_{ij}$) across all concurrent flight paths, warning you of collisions **before merge**.

---

## 🚀 Quickstart (2 Minutes)

Add `.github/workflows/gdss-radar.yml` to your repository:

```yaml
name: GDSS PR Collision Radar

on:
  pull_request:
    types: [opened, synchronize, reopened]

# Cancel superseded runs when rapid pushes occur on the same PR
concurrency:
  group: gdss-radar-${{ github.event.pull_request.number }}
  cancel-in-progress: true

permissions:
  contents: read
  pull-requests: write
  issues: write

jobs:
  collision-radar:
    name: Audit Cross-Branch PR Collisions
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository with full history
        uses: actions/checkout@v4
        with:
          fetch-depth: 0  # Required to resolve branch histories & merge-bases

      - name: Run GDSS PR Collision Radar
        uses: sarthak2233/gdss-radar@v1
        with:
          token: ${{ secrets.GITHUB_TOKEN }}
```

> [!NOTE]
> **No External Secrets Required:** The action runs 100% within your GitHub Actions runner using `${{ secrets.GITHUB_TOKEN }}`. No credit card or third-party cloud account required.

---

## 📋 Action Inputs

| Input | Description | Required | Default |
| :--- | :--- | :---: | :--- |
| `token` | GitHub token for querying open PRs and posting sticky reports | **Yes** | `${{ github.token }}` |
| `threshold` | Minimum collision score ($\Omega_{ij} \in [0, 1]$) to flag | No | `'0.1'` |
| `fail_on_collision` | Set to `'true'` to fail CI job when severe collision is detected | No | `'false'` |
| `provider` | LLM provider for convergence suggestions (`github-models`, `openai`, `openrouter`) | No | `'github-models'` |
| `model` | Model name for suggestion generation | No | `'gpt-4o'` |
| `args` | Additional CLI flags passed directly to `gdss audit-pr` | No | `''` |

---

## 📊 What You See on Pull Requests

When concurrent PRs touch overlapping symbols or introduce non-commuting operators, GDSS posts a sticky comment directly on the PR:

```markdown
## 🛰️ GDSS PR Collision Radar Report

> **Incoming PR:** #42 (`feature/oauth-upgrade`)
> **Attractor Base:** `main`
> **Concurrent PRs Audited:** 4 open PRs

### ⚠️ Detected Cross-Branch Collisions

| Colliding PR | Author | Target File | Overlapping Symbol | Risk (Ω_ij) | Validation Status |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **#39** (`feature/timeout`) | @alice | `services/auth.py` | `login()` | **0.85** | ⚡ Validated in Sandbox (syntax: OK, 12 tests passed) |
| **#41** (`feature/billing`) | @bob | `services/pay.py` | `process()` | **0.62** | ⚡ Validated in Sandbox (syntax: OK, 8 tests passed) |

### 💡 Recommended Candidate Reconciliations

#### Candidate Patch for `services/auth.py` (resolves collision with PR #39):
```suggestion
def login(username: str, token: str, timeout: int = 30) -> AuthResponse:
    # Reconciled: Combines Alice's timeout parameter with OAuth2 token verification
    client = get_oauth_client(timeout=timeout)
    return client.authenticate(username, token)
```
```

---

## ⚙️ Optional Configuration (`.gdss.yml`)

GDSS is zero-config by default. To customize thresholds or test commands, add `.gdss.yml` to the root of your repo:

```yaml
radar:
  model: "gpt-4o"
  provider: "github"
  risk_threshold: 0.1
  fail_on_collision: false

validation:
  test_command: "pytest {test_file}"
  typecheck_command: "mypy {file}"
  timeout_seconds: 60

hierarchy:
  reference_branch: "main"
  same_base_only: true
  exclude:
    - "dependabot/*"
    - "spike/*"
```

---

## 🛡️ Privacy & Zero Token Bleed

- **100% Deterministic Core:** Abstract Syntax Tree (AST) parsing, dependency graphs, and the $\Omega_{ij}$ collision tensor run purely on the GitHub Actions runner.
- **Zero Proprietary Cloud Ingestion:** Your source code is never uploaded to any GDSS server or centralized database.
- **Bring Your Own Compute (BYOC):** AI suggestions run via your repository's entitled GitHub Models or your own API keys.

---

## 📄 License

This GitHub Action wrapper is licensed under the [MIT License](LICENSE).
The GDSS engine is powered by Gate Distributive Semantic Synopsis.
