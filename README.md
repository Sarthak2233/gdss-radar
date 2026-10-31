# 🛰️ GDSS PR Collision Radar Action

[![GitHub Marketplace](https://img.shields.io/badge/Marketplace-GDSS%20Radar-blue?logo=github&style=flat-square)](https://github.com/marketplace/actions/gdss-pr-collision-radar)
[![Release](https://img.shields.io/github/v/release/sarthak2233/gdss-radar?style=flat-square&color=success)](https://github.com/sarthak2233/gdss-radar/releases)
[![License](https://img.shields.io/badge/License-MIT-green.svg?style=flat-square)](LICENSE)
[![Zero Token Bleed](https://img.shields.io/badge/Core-Deterministic%20$0%20Tokens-orange?style=flat-square)](#-privacy--zero-token-bleed)
[![Docker Image](https://img.shields.io/badge/Container-GHCR%20Runner-purple?logo=docker&style=flat-square)](https://github.com/Sarthak2233/GDSS/pkgs/container/gdss-runner)

> **Air Traffic Control for GitHub Pull Requests.**  
> Automatically detects cross-branch semantic collisions across all concurrently open PRs, tests candidate reconciliations in ephemeral sandboxes, and posts a single sticky Markdown radar report with 1-click **"Commit suggestion"** blocks.

---

## 🎯 The Problem: Single-PR Blindness

Traditional CI and AI coding assistants suffer from a structural blind spot: **Single-PR Isolation**.

They only test **`PR #42 vs main`**. They have zero awareness of the 10 other active PRs your team is actively authoring.
- If **Branch A** refactors a function signature in `services/auth.py`,
- And **Branch B** invokes the old signature in `services/billing.py`,

Git reports **0 merge conflicts** and CI passes green. Yet the moment both PRs land, `main` breaks in production.

**GDSS PR Collision Radar solves this by acting as an Air Traffic Control radar:** it continuously computes pairwise branch interactions across all concurrent flight paths, warning your team of semantic collisions **before merge**.

---

## 🔬 How It Works: The 7 Intelligence Layers

GDSS operates through a deterministic 7-layer semantic pipeline that lifts raw text diffs into structured program transformations:

```
┌─────────────────────────────────────────────────────────────┐
│ Layer 1: Git DAG Observer (Flight Discovery)                │
└──────────────────────────────┬──────────────────────────────┘
                               ▼
┌─────────────────────────────────────────────────────────────┐
│ Layer 2: 3-Tier Multi-Language AST Parser                   │
└──────────────────────────────┬──────────────────────────────┘
                               ▼
┌─────────────────────────────────────────────────────────────┐
│ Layer 3: Semantic Transform Algebra (dC Mutations)          │
└──────────────────────────────┬──────────────────────────────┘
                               ▼
┌─────────────────────────────────────────────────────────────┐
│ Layer 4: Blast Radius & Cross-Branch Dependency Graph       │
└──────────────────────────────┬──────────────────────────────┘
                               ▼
┌─────────────────────────────────────────────────────────────┐
│ Layer 5: Pairwise Collision Radar (Ω_ij Interaction Tensor) │
└──────────────────────────────┬──────────────────────────────┘
                               ▼
┌─────────────────────────────────────────────────────────────┐
│ Layer 6: Ephemeral Worktree Sandbox (Build & Test Gates)    │
└──────────────────────────────┬──────────────────────────────┘
                               ▼
┌─────────────────────────────────────────────────────────────┐
│ Layer 7: Sandboxed AI Convergence Assistant (Sticky Report) │
└─────────────────────────────────────────────────────────────┘
```

1. **Layer 1 — Git DAG Observer**: Discovers all active pull requests targeting the same base branch (`main`), resolving commit graphs, merge-bases, and branch trajectories.
2. **Layer 2 — 3-Tier Multi-Language AST Parser**: Leverages Tree-sitter parsers across 20+ languages (TypeScript/JS, Python, Go, Rust, Java, C/C++, etc.) to extract symbols, classes, functions, and interfaces.
3. **Layer 3 — Semantic Transform Algebra**: Lifts line-based git diffs into semantic mutations ($dC$) — distinguishing harmless whitespace shifts from contractual signature modifications, renames, and deletions.
4. **Layer 4 — Blast Radius & Dependency Graph**: Builds an in-memory caller/callee graph to trace cascading impacts across modules and files.
5. **Layer 5 — Pairwise Collision Radar ($\Omega_{ij}$)**: Evaluates non-commutative operator interference between concurrent branches. If Branch A and Branch B alter shared contracts in conflicting ways, GDSS flags the collision before merge.
6. **Layer 6 — Ephemeral Worktree Sandbox**: Provisions isolated, throwaway Git worktrees to compile and test candidate reconciliations against your repository's native type checker and test suite.
7. **Layer 7 — Sandboxed AI Convergence Assistant**: Synthesizes minimal, conflict-free patches, explains the risks in plain English, and self-heals against compiler errors before presenting suggestions to humans.

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
> **No External Accounts Needed:** By default, GDSS uses free GitHub Models via `${{ secrets.GITHUB_TOKEN }}`. You do not need an external API key or credit card.

---

## ⚙️ Configuration Guide: `.gdss.yml`

GDSS is zero-config by default, but **committing a `.gdss.yml` file to the root of your repository is the recommended best approach** for production teams.

### 🌟 The Golden Rule: Avoid Fallback Constriction

GDSS uses an intelligent symbol condensation engine that packs affected AST functions, caller/callee references, and commit histories into the model's prompt.

| Configuration Strategy | Context Budget | Code Condensation Behavior |
| :--- | :--- | :--- |
| **Heuristic / Fallback** *(omitted)* | 16,384 tokens | Severe ellipsis condensation (`/* ... 40 lines condensed ... */`). Truncates complex cross-file functions. |
| **Explicit `.gdss.yml`** *(Recommended)* | **128,000 tokens** | **Allocates up to 45% (96,000 chars)** for full implementations, interface signatures, and exact caller traces. |

> [!IMPORTANT]
> **Always declare `context_window` explicitly in `.gdss.yml`** matching your selected model (e.g. `128000` for `gpt-4o`, `64000` for `deepseek-chat`). This guarantees that your CI runner unlocks the maximum code symbol budget without relying on conservative fallbacks.

---

### 📋 Recommended Production Configuration Template

Copy [`.gdss.yml.example`](.gdss.yml.example) into `.gdss.yml` at the root of your repository:

```yaml
# .gdss.yml - Place at repository root
radar:
  model: "gpt-4o"
  context_window: 128000  # Explicit token ceiling (Recommended Best Practice)
  provider: "github"      # Uses free GitHub Models via GITHUB_TOKEN
  risk_threshold: 0.1     # Sensitivity: 0.05 (sensitive) to 0.25 (strict)
  max_refinements: 2      # Sandboxed self-correction loops on test failures
  fail_on_collision: false

validation:
  test_command: "npm test -- {test_file}"
  typecheck_command: "npx tsc --noEmit"
  timeout_seconds: 60

hierarchy:
  reference_branch: "main"
  same_base_only: true    # Audit PRs targeting the same base branch
  exclude:
    - "dependabot/*"
    - "renovate/*"
    - "spike/*"

ast:
  exclude_patterns:
    - "node_modules/**"
    - "dist/**"
    - "build/**"
    - ".venv/**"
```

---

## 🛠️ Stack-Specific Configuration Recipes

### 1. TypeScript / Node.js
```yaml
radar:
  model: "gpt-4o"
  context_window: 128000
  provider: "github"
  risk_threshold: 0.1
  max_refinements: 2

validation:
  test_command: "pnpm test -- {test_file}"
  typecheck_command: "pnpm tsc --noEmit"
  timeout_seconds: 45

hierarchy:
  reference_branch: "main"
  same_base_only: true
```

### 2. Python (pytest & mypy)
```yaml
radar:
  model: "gpt-4o"
  context_window: 128000
  provider: "github"
  risk_threshold: 0.1
  max_refinements: 2

validation:
  test_command: "pytest {test_file}"
  typecheck_command: "mypy {file}"
  timeout_seconds: 60

hierarchy:
  reference_branch: "main"
  same_base_only: true
```

### 3. Go
```yaml
radar:
  model: "gpt-4o"
  context_window: 128000
  provider: "github"
  risk_threshold: 0.1

validation:
  test_command: "go test -v ./{test_file}/..."
  typecheck_command: "golangci-lint run"
  timeout_seconds: 60

hierarchy:
  reference_branch: "main"
  same_base_only: true
```

---

## 🚦 Strict Merge Protection (Blocking Gate)

To use GDSS as a required status check in GitHub Branch Protection rules:

1. Set `fail_on_collision: true` in your workflow:
```yaml
      - name: Run GDSS PR Collision Radar (Strict Gate)
        uses: sarthak2233/gdss-radar@v1
        with:
          token: ${{ secrets.GITHUB_TOKEN }}
          fail_on_collision: 'true'
          threshold: '0.2'
```
2. In your GitHub repository settings, navigate to **Settings ➔ Branches ➔ Branch protection rules**.
3. Under **Require status checks to pass before merging**, select **`Audit Cross-Branch PR Collisions`**.

Now, if another teammate's PR collides with an incoming PR above the threshold, GitHub blocks the merge until the collision is resolved or reconciled!

---

## 📊 What You See on Pull Requests

When concurrent PRs touch overlapping symbols or introduce non-commuting operators, GDSS maintains a single **sticky comment** on the PR that updates automatically on every push:

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

Developers can click **"Commit suggestion"** directly inside the GitHub PR UI to land the validated reconciliation with zero manual cherry-picking.

---

## 📋 Action Inputs Reference

| Input | Description | Required | Default |
| :--- | :--- | :---: | :--- |
| `token` | GitHub token for querying open PRs and posting sticky reports | **Yes** | `${{ github.token }}` |
| `threshold` | Minimum collision score ($\Omega_{ij} \in [0, 1]$) to flag | No | `'0.1'` |
| `fail_on_collision` | Set to `'true'` to fail CI job when severe collision is detected | No | `'false'` |
| `provider` | LLM provider for convergence suggestions (`github-models`, `openai`, `openrouter`) | No | `'github-models'` |
| `model` | Model name for suggestion generation | No | `'gpt-4o'` |
| `args` | Additional CLI flags passed directly to `gdss audit-pr` | No | `''` |

---

## 🛡️ Privacy & Zero Token Bleed

- **100% Deterministic Core:** Abstract Syntax Tree (AST) parsing, caller/callee graphs, and the pairwise collision radar run purely on the local GitHub Actions runner.
- **Zero Proprietary Cloud Ingestion:** Your source code is never transmitted to any external GDSS server or centralized database.
- **Bring Your Own Compute (BYOC):** AI convergence suggestions run via your repository's entitled GitHub Models or your own API keys.
- **No Unsolicited Telemetry:** GDSS respects developer privacy and complies with strict zero-telemetry boundaries.

---

## 📄 License

This GitHub Action wrapper and starter configurations are licensed under the [MIT License](LICENSE).  
The underlying analysis engine is powered by Gate Distributive Semantic Synopsis (GDSS).
