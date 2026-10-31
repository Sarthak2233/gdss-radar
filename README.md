# 🛰️ GDSS PR Collision Radar Action

[![GitHub Marketplace](https://img.shields.io/badge/Marketplace-GDSS%20Radar-blue?logo=github&style=flat-square)](https://github.com/marketplace/actions/gdss-pr-collision-radar)
[![Release](https://img.shields.io/github/v/release/sarthak2233/gdss-radar?style=flat-square&color=success)](https://github.com/sarthak2233/gdss-radar/releases)
[![License](https://img.shields.io/badge/License-MIT-green.svg?style=flat-square)](LICENSE)
[![Zero Token Bleed](https://img.shields.io/badge/Core-Deterministic%20$0%20Tokens-orange?style=flat-square)](#-privacy--zero-token-bleed)
[![Docker Image](https://img.shields.io/badge/Container-GHCR%20Runner-purple?logo=docker&style=flat-square)](https://github.com/Sarthak2233/GDSS/pkgs/container/gdss-runner)

> **Air Traffic Control for GitHub Pull Requests.**  
> Automatically detects cross-branch semantic collisions across all concurrently open PRs, tests candidate reconciliations in ephemeral sandboxes, and posts a single sticky Markdown radar report with 1-click **"Commit suggestion"** blocks.

---

## 📑 Table of Contents
- [🎯 The Problem: Single-PR Blindness](#-the-problem-single-pr-blindness)
- [🔬 How It Works: The 7 Intelligence Layers](#-how-it-works-the-7-intelligence-layers)
- [🚀 The Workflow Code (`.github/workflows/gdss-radar.yml`)](#-the-workflow-code-githubworkflowsgdss-radaryml)
  - [Code Breakdown: What Each Line Does](#-code-breakdown-what-each-line-does)
- [⚙️ The Configuration File (`.gdss.yml`)](#️-the-configuration-file-gdssyml)
  - [The Golden Rule: Avoid Fallback Constriction](#-the-golden-rule-avoid-fallback-constriction)
  - [Complete `.gdss.yml` Production Template](#-complete-gdssyml-production-template)
  - [Detailed Schema Reference & Field Breakdown](#-detailed-schema-reference--field-breakdown)
- [🔄 How Workflow Code & Configuration Interact at Runtime](#-how-workflow-code--configuration-interact-at-runtime)
- [🔀 Multi-PR Batch Handling (Pairwise 3-Way Triangles)](#-multi-pr-batch-handling-pairwise-3-way-triangles)
- [🛠️ Stack-Specific Configuration Recipes](#️-stack-specific-configuration-recipes)
- [🚦 Strict Merge Protection (Blocking Gate)](#-strict-merge-protection-blocking-gate)
- [📊 What You See on Pull Requests](#-what-you-see-on-pull-requests)
- [🛡️ Privacy & Zero Token Bleed](#️-privacy--zero-token-bleed)

---

## 🎯 The Problem: Single-PR Blindness

Traditional CI and AI coding assistants operate with a fatal structural blind spot: **Single-PR Isolation**.

They only test **`PR #42 vs main`**. They have zero awareness of the 10 other active PRs your teammates are actively writing.
- If **Branch A** refactors a function signature in `services/auth.py`,
- And **Branch B** invokes the old signature in `services/billing.py`,

Git reports **0 merge conflicts** and CI passes green. Yet the moment both PRs land, `main` breaks in production.

**GDSS PR Collision Radar acts as an Air Traffic Control radar:** it continuously computes pairwise branch interactions across all concurrent flight paths, warning your team of semantic collisions **before merge**.

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

## 🚀 The Workflow Code (`.github/workflows/gdss-radar.yml`)

Add this workflow file to your repository at `.github/workflows/gdss-radar.yml`:

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
      - name: Checkout repository with full commit history
        uses: actions/checkout@v4
        with:
          fetch-depth: 0  # CRITICAL: Required for 3-way DAG & merge-base resolution

      - name: Run GDSS PR Collision Radar
        uses: sarthak2233/gdss-radar@v1
        with:
          token: ${{ secrets.GITHUB_TOKEN }}
          threshold: "0.1"             # Optional: collision intensity threshold (0.0 to 1.0)
          fail_on_collision: "false"   # Optional: set to 'true' to block PR merge
          provider: "github"           # Optional: github, openai, openrouter, deepseek
          model: "gpt-4o"              # Optional: model identifier
```

---

### 🔍 Code Breakdown: What Each Line Does

| Workflow Code Block | Why It Is Configured | What Happens If Omitted or Misconfigured |
| :--- | :--- | :--- |
| `on.pull_request.types: [opened, synchronize, reopened]` | Triggers the collision radar whenever a new PR is created (`opened`), new commits are pushed (`synchronize`), or a closed PR is revived (`reopened`). | Omitting `synchronize` means pushes to an existing PR will never recalculate collision risks against newly merged code. |
| `concurrency.group: gdss-radar-${{ github.event.pull_request.number }}`<br>`cancel-in-progress: true` | Prevents race conditions and saves GitHub Actions minutes. If an engineer pushes 3 rapid commits, previous in-flight audits are automatically canceled. | Multiple duplicate runner jobs execute simultaneously, wasting CI quota and generating comment race conditions. |
| `permissions: contents: read` | Grants the runner permission to read repository files and Git tree commits. | Action fails at startup when checking out repository files. |
| `permissions: pull-requests: write`<br>`permissions: issues: write` | Grants permission to post and update the sticky Markdown collision radar report directly on the incoming pull request. | Action calculates collisions but crashes when attempting to post the sticky report comment to the PR. |
| `uses: actions/checkout@v4`<br>`with: fetch-depth: 0` | **Mandatory requirement.** Clones the entire Git commit history rather than a shallow single commit (`depth: 1`). | **Fatal Failure:** GDSS cannot resolve the common merge-base ancestor between concurrent PR branches, causing DAG trajectory calculation to abort. |
| `uses: sarthak2233/gdss-radar@v1` | Runs the pre-compiled container image (`ghcr.io/sarthak2233/gdss-runner`) directly on GitHub's Ubuntu runner. | **Zero Setup:** No Python installation, no virtual environment creation, and no pip build overhead required. |
| `with.token: ${{ secrets.GITHUB_TOKEN }}` | Built-in GitHub token automatically provided to every Actions workflow. Used to query concurrent open PRs and authenticate free GitHub Models API requests. | Action cannot query GitHub REST API to discover concurrent pull requests. |
| `with.threshold: "0.1"` | Sets the collision sensitivity threshold ($\Omega_{ij} \in [0.0, 1.0]$). Collisions below this score are treated as commuting/safe. | Defaults to `0.1`. |
| `with.fail_on_collision: "false"` | Determines whether detected severe collisions exit with code `1` (blocking PR merge) or code `0` (informational sticky report). | Defaults to `false` (informational mode). Set to `'true'` for strict branch protection gating. |

---

## ⚙️ The Configuration File (`.gdss.yml`)

GDSS is zero-config by default, but **committing a `.gdss.yml` file to the root of your repository is the recommended best approach** for production teams.

### 🌟 The Golden Rule: Avoid Fallback Constriction

GDSS uses an intelligent symbol condensation engine that packs affected AST functions, caller/callee references, and commit histories into the model's prompt.

| Configuration Strategy | Context Token Window | Symbol Code Condensation Behavior |
| :--- | :--- | :--- |
| **Heuristic / Fallback** *(omitted)* | 16,384 tokens | Severe ellipsis condensation (`/* ... 40 lines condensed ... */`). Truncates complex cross-file functions and caller graphs. |
| **Explicit `.gdss.yml`** *(Recommended)* | **128,000 tokens** | **Allocates up to 45% (up to 96,000 chars)** for full implementations, interface signatures, and exact caller traces. |

> [!IMPORTANT]
> **Always declare `context_window` explicitly in `.gdss.yml`** matching your selected model (e.g. `128000` for `gpt-4o`, `64000` for `deepseek-chat`). This guarantees that your CI runner unlocks the maximum code symbol budget without relying on conservative 16k fallbacks.

---

### 📋 Complete `.gdss.yml` Production Template

Save this file as **`.gdss.yml`** in the **root** of your repository:

```yaml
# ==============================================================================
# .gdss.yml - GDSS Semantic Repository Intelligence Configuration
# ==============================================================================
# Check this file into repository root so local CLI and CI share identical rules.

# ------------------------------------------------------------------------------
# 1. AI Convergence & PR Collision Radar
# ------------------------------------------------------------------------------
radar:
  model: "gpt-4o"
  context_window: 128000  # Explicit token ceiling (MANDATORY BEST PRACTICE)
  provider: "github"      # Uses free GitHub Models via GITHUB_TOKEN
  risk_threshold: 0.1     # Sensitivity: 0.05 (sensitive) to 0.25 (strict)
  max_refinements: 2      # Sandboxed self-correction loops on compiler errors
  fail_on_collision: false

# ------------------------------------------------------------------------------
# 2. Ephemeral Sandbox Validation
# ------------------------------------------------------------------------------
validation:
  test_command: "npm test -- {test_file}"
  typecheck_command: "npx tsc --noEmit"
  timeout_seconds: 60

# ------------------------------------------------------------------------------
# 3. Branch Hierarchy & Flight Boundaries
# ------------------------------------------------------------------------------
hierarchy:
  reference_branch: "main"
  priority_chain:
    - "main"
    - "develop"
  same_base_only: true    # Audit PRs targeting the same base landing branch
  include:
    - "feature/*"
    - "fix/*"
  exclude:
    - "dependabot/*"
    - "renovate/*"
    - "spike/*"
  target_bases:
    - "main"
    - "develop"

# ------------------------------------------------------------------------------
# 4. AST Symbol & Path Filtering
# ------------------------------------------------------------------------------
ast:
  additional_extensions:
    - ".vue"
    - ".svelte"
    - ".sol"
  exclude_patterns:
    - "node_modules/**"
    - "dist/**"
    - "build/**"
    - "vendor/**"
    - ".venv/**"
```

---

### 🔍 Detailed Schema Reference & Field Breakdown

#### Section 1: `radar` (AI Convergence & Collision Detection)
| Field | Type | Default | What It Does & Recommended Values |
| :--- | :--- | :--- | :--- |
| `model` | `string` | `"gpt-4o"` | The model ID used for semantic patch reconciliation. Common choices: `"gpt-4o"`, `"gpt-4o-mini"`, `"deepseek-chat"`, `"claude-3-5-sonnet-20241022"`, `"llama-3.3-70b-versatile"`. |
| `context_window` | `integer` | `128000` | **Mandatory Best Practice.** Declares the exact token window capacity. Allocates up to 45% of tokens to symbol definitions. Prevents fallback to 16,384 tokens. |
| `provider` | `string` | `"github"` | Inference backend: `"github"` (uses free GitHub Models via `GITHUB_TOKEN`), `"openai"` (`OPENAI_API_KEY`), `"openrouter"`, `"deepseek"`, `"ollama"`. |
| `risk_threshold` | `float` | `0.1` | Collision intensity threshold ($\Omega_{ij} \in [0.0, 1.0]$).<br>• `0.05`: High sensitivity (flags subtle caller dependencies)<br>• `0.10`: Recommended default balance<br>• `0.25`: Strict (flags only direct signature changes)<br>• `0.80`: Critical only (flags conflicting implementations) |
| `max_refinements` | `integer` | `2` | Number of automated self-correction loops. If tests fail in the sandbox worktree, compiler error traces are fed back to the model to refine the patch up to $N$ times. |
| `fail_on_collision` | `boolean` | `false` | If `true`, the CI job exits with code `1` when severe collisions are detected (used for required branch protection status checks). |

#### Section 2: `validation` (Ephemeral Worktree Sandbox)
| Field | Type | Default | What It Does & Recommended Values |
| :--- | :--- | :--- | :--- |
| `test_command` | `string` | `""` | Command to execute targeted unit tests in the isolated sandbox. Supports `{test_file}` interpolation (e.g. `pytest {test_file}`, `npm test -- {test_file}`). |
| `typecheck_command` | `string` | `""` | Command to verify static types or lint rules in the sandbox. Supports `{file}` interpolation (e.g. `npx tsc --noEmit`, `mypy {file}`, `cargo check`). |
| `timeout_seconds` | `integer` | `60` | Hard timeout ceiling for test execution in the ephemeral sandbox worktree to prevent hangs. |

#### Section 3: `hierarchy` (Branch Selection & Attractor Precedence)
| Field | Type | Default | What It Does & Recommended Values |
| :--- | :--- | :--- | :--- |
| `reference_branch` | `string` | `"main"` | The primary attractor base branch against which feature branches are audited. |
| `priority_chain` | `list` | `["main"]` | Ordered fallback list of base branches for multi-stage landing pipelines (e.g. `["main", "develop", "staging"]`). |
| `same_base_only` | `boolean` | `true` | **Crucial for multi-branch repos.** When `true`, only PRs targeting the exact same base landing branch are audited against each other, eliminating false alarms between production and maintenance branches. |
| `include` | `list` | `[]` | Glob patterns of branch names to audit (e.g. `["feature/*", "fix/*"]`). |
| `exclude` | `list` | `[]` | Glob patterns of branch names to ignore (e.g. `["dependabot/*", "renovate/*", "spike/*"]`). |
| `target_bases` | `list` | `["main"]` | Specific base branches that activate collision auditing when targeted by incoming PRs. |

#### Section 4: `ast` (Syntax Parsing & Exclusions)
| Field | Type | Default | What It Does & Recommended Values |
| :--- | :--- | :--- | :--- |
| `additional_extensions` | `list` | `[]` | Additional file extensions mapped to AST or Domain Reducer parsers (e.g. `[".vue", ".svelte", ".sol"]`). |
| `exclude_patterns` | `list` | `[]` | File paths to exclude from symbol parsing and collision computation (e.g. `["node_modules/**", "dist/**", ".venv/**"]`). |

---

## 🔄 How Workflow Code & Configuration Interact at Runtime

The diagram below illustrates how `.github/workflows/gdss-radar.yml` and `.gdss.yml` work together during a pull request:

```
[Developer pushes to PR #42]
            │
            ▼
GitHub Actions Runner launches
  │
  ├── 1. actions/checkout (fetch-depth: 0)
  │      Clones full Git DAG (all commits, tags, and branch heads)
  │
  └── 2. sarthak2233/gdss-radar@v1
         Pulls ghcr.io/sarthak2233/gdss-runner:latest
         Mounts workspace with .gdss.yml
         Executes: gdss audit-pr
            │
            ├── Loads .gdss.yml:
            │     - Sets context_window = 128000
            │     - Sets risk_threshold = 0.1
            │     - Applies hierarchy rules (same_base_only = true)
            │
            ├── Queries GitHub REST API:
            │     - Fetches concurrent open PRs targeting main (#39, #41)
            │
            ├── Computes Pairwise Collision Tensor (Ω_42,39 & Ω_42,41):
            │     - Parses AST symbols across branches
            │     - Evaluates non-commutative operators
            │
            ├── Ephemeral Sandbox Worktree:
            │     - Creates isolated temporary worktree
            │     - Applies candidate reconciliation patch
            │     - Executes validation.test_command & typecheck_command
            │     - If failed, refines patch (up to max_refinements = 2)
            │
            └── Posts / Updates Sticky Markdown Report:
                  - Writes radar table & 1-click suggestion to PR #42
```

---

## 🔀 Multi-PR Batch Handling (Pairwise 3-Way Triangles)

When an incoming pull request (e.g., PR #42) is opened while 5 other PRs are concurrently open (#39, #40, #41, #43, #44), **GDSS processes all 5 PRs in a single workflow run**:

1. **Autonomous Discovery:** Automatically lists all open pull requests targeting the same base attractor (`main`).
2. **Pairwise 3-Way Triangles:** Instead of dumping all 5 branches into an unpredictable $N$-way monolithic prompt, GDSS evaluates each concurrent PR pairwise:
   - **Triangle 1:** Base (`main`) vs Incoming PR #42 vs Concurrent PR #39
   - **Triangle 2:** Base (`main`) vs Incoming PR #42 vs Concurrent PR #40
   - **Triangle 3:** Base (`main`) vs Incoming PR #42 vs Concurrent PR #41
3. **Isolated Reconciliation & Ephemeral Sandboxing:**
   For each pair with significant collision ($\Omega_{ij} \ge \text{threshold}$):
   - GDSS extracts the exact 3-way symbol representation ($V_{base}, V_{incoming}, V_{concurrent}$) and impacted callers.
   - The convergence engine drafts the reconciled definition with zero cross-PR pollution.
   - An ephemeral Git worktree compiles and tests the candidate patch against your configured `validation` commands.
4. **Aggregated Sticky Radar Report:**
   All detected collisions and validated patches across all concurrent PRs are aggregated into a single, clean markdown comment posted to the incoming PR.

This pairwise 3-way batching guarantees that adding 5 or 50 concurrent PRs **never creates prompt explosion or cross-feature hallucination**.

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

### 4. Rust
```yaml
radar:
  model: "gpt-4o"
  context_window: 128000
  provider: "github"
  risk_threshold: 0.1

validation:
  test_command: "cargo test --test {test_file}"
  typecheck_command: "cargo check"
  timeout_seconds: 90

hierarchy:
  reference_branch: "main"
  same_base_only: true
```

---

## 🚦 Strict Merge Protection (Blocking Gate)

To use GDSS as a required status check in GitHub Branch Protection rules:

1. In your `.github/workflows/gdss-radar.yml`, set `fail_on_collision: 'true'`:
```yaml
      - name: Run GDSS PR Collision Radar (Strict Gate)
        uses: sarthak2233/gdss-radar@v1
        with:
          token: ${{ secrets.GITHUB_TOKEN }}
          fail_on_collision: 'true'
          threshold: '0.2'
```
2. In your GitHub repository settings, navigate to **Settings ➔ Branches ➔ Branch protection rules**.
3. Under **Require status checks to pass before merging**, search for and select **`Audit Cross-Branch PR Collisions`**.

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

## 🛡️ Privacy & Zero Token Bleed

- **100% Deterministic Core:** Abstract Syntax Tree (AST) parsing, caller/callee graphs, and the pairwise collision radar run purely on the local GitHub Actions runner.
- **Zero Proprietary Cloud Ingestion:** Your source code is never transmitted to any external GDSS server or centralized database.
- **Bring Your Own Compute (BYOC):** AI convergence suggestions run via your repository's entitled GitHub Models or your own API keys.
- **No Unsolicited Telemetry:** GDSS respects developer privacy and complies with strict zero-telemetry boundaries.

---

## 📄 License

This GitHub Action wrapper and starter configurations are licensed under the [MIT License](LICENSE).  
The underlying analysis engine is powered by Gate Distributive Semantic Synopsis (GDSS).
