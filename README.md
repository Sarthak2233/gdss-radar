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
- [📚 Documentation Library](#-documentation-library)
- [🎯 The Problem: Single-PR Blindness](#-the-problem-single-pr-blindness)
- [🔬 How It Works: The 7 Intelligence Layers](#-how-it-works-the-7-intelligence-layers)
- [🌐 Supported Languages & Multi-Asset Reducers](#-supported-languages--multi-asset-reducers)
- [🚀 The Workflow Code (`.github/workflows/gdss-radar.yml`)](#-the-workflow-code-githubworkflowsgdss-radaryml)
  - [Code Breakdown: What Each Line Does](#-code-breakdown-what-each-line-does)
- [⚙️ The Configuration File (`.gdss.yml`)](#️-the-configuration-file-gdssyml)
  - [The Golden Rule: Avoid Fallback Constriction](#-the-golden-rule-avoid-fallback-constriction)
  - [Complete `.gdss.yml` Production Template](#-complete-gdssyml-production-template)
  - [Detailed Schema Reference & Field Breakdown](#-detailed-schema-reference--field-breakdown)
- [💡 Essential Concepts: What Every User Must Know](#-essential-concepts-what-every-user-must-know)
- [🔄 How Workflow Code & Configuration Interact at Runtime](#-how-workflow-code--configuration-interact-at-runtime)
- [🔀 Multi-PR Batch Handling (Pairwise 3-Way Triangles)](#-multi-pr-batch-handling-pairwise-3-way-triangles)
- [🛠️ Stack-Specific Configuration Recipes](#️-stack-specific-configuration-recipes)
- [🚦 Strict Merge Protection (Blocking Gate)](#-strict-merge-protection-blocking-gate)
- [📊 What You See on Pull Requests](#-what-you-see-on-pull-requests)
- [💻 Standalone Binary & IDE MCP Server (Cursor & Claude Code)](#-standalone-binary--ide-mcp-server-cursor--claude-code)
- [🛡️ Privacy & Zero Token Bleed](#️-privacy--zero-token-bleed)

---

## 📚 Documentation Library

Explore the complete documentation for GDSS Standalone CLI, IDE MCP Server, and GitHub Actions Radar:

| Guide | Description | Target Audience |
| :--- | :--- | :--- |
| [**💻 Standalone Binary & MCP Guide**](docs/STANDALONE_BINARY_AND_MCP_GUIDE.md) | Universal 1-line installation for Linux, macOS, and Windows. IDE MCP setup for Cursor, Claude Code, and Copilot. | Developers & Testers |
| [**📖 Complete User & Integrator Guide**](docs/USER_GUIDE.md) | The 4 operational modes, CLI commands, safe landing lifecycle with guarded apply and rollback. | All Users |
| [**⚙️ Repository Configuration Guide**](docs/CONFIGURATION_GUIDE.md) | Comprehensive `.gdss.yml` schema, explicit context window sizing, model suitability matrix, and provider recipes. | Maintainers & DevOps |
| [**🛰️ GitHub Actions CI Guide**](docs/GITHUB_ACTIONS_GUIDE.md) | Multi-PR batch handling, sticky PR comments, strict branch protection gating, and fork handling. | CI/CD Engineers |
| [**🌐 Universal Multi-Asset Guide**](docs/UNIVERSAL_MULTI_ASSET_GUIDE.md) | How GDSS audits package manifests (`package.json`), database migrations (split-head hazards), and SQL DDL. | Full-Stack & DBAs |
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
2. **Layer 2 — 3-Tier Multi-Language AST Parser**: Leverages Tree-sitter parsers across 20+ languages to extract symbols, classes, functions, and interfaces.
3. **Layer 3 — Semantic Transform Algebra**: Lifts line-based git diffs into semantic mutations ($dC$) — distinguishing harmless whitespace shifts from contractual signature modifications, renames, and deletions.
4. **Layer 4 — Blast Radius & Dependency Graph**: Builds an in-memory caller/callee graph to trace cascading impacts across modules and files.
5. **Layer 5 — Pairwise Collision Radar ($\Omega_{ij}$)**: Evaluates non-commutative operator interference between concurrent branches. If Branch A and Branch B alter shared contracts in conflicting ways, GDSS flags the collision before merge.
6. **Layer 6 — Ephemeral Worktree Sandbox**: Provisions isolated, throwaway Git worktrees to compile and test candidate reconciliations against your repository's native type checker and test suite.
7. **Layer 7 — Sandboxed AI Convergence Assistant**: Synthesizes minimal, conflict-free patches, explains the risks in plain English, and self-heals against compiler errors before presenting suggestions to humans.

---

## 🌐 Supported Languages & Multi-Asset Reducers

GDSS does not treat source files as raw text strings. It uses a **3-tier code parsing hierarchy** combined with specialized **non-code domain reducers**:

### 1. Code Languages (AST Parsing Hierarchy)

| Tier | Language | File Extensions | Extracted Symbols & Capabilities |
| :--- | :--- | :--- | :--- |
| **Tier 1: Specialized Tree-sitter** | **Python** | `.py`, `.pyi` | Classes, async/sync methods, decorators, type hints, import call graphs |
| | **JavaScript** | `.js`, `.jsx`, `.mjs`, `.cjs` | ES6 modules, functions, classes, arrow functions, CommonJS exports |
| | **TypeScript** | `.ts`, `.tsx` | Interfaces, type aliases, enums, generics, React functional components |
| | **Go** | `.go` | Structs, interfaces, methods, functions, package imports |
| | **Rust** | `.rs` | Structs, enums, traits, `impl` blocks, functions, macros |
| | **Java** | `.java` | Classes, interfaces, records, methods, annotations |
| | **C & C++** | `.c`, `.h`, `.cpp`, `.hpp`, `.cc`, `.cxx` | Functions, structs, classes, namespaces, headers |
| **Tier 2: Dynamic Grammar Walker** | **Any Tree-sitter grammar** | Pluggable | Any language grammar registered dynamically |
| **Tier 3: Structural Block Fallback** | **C#** | `.cs` | Classes, interfaces, methods, properties |
| | **Kotlin & Swift** | `.kt`, `.kts`, `.swift` | Classes, structs, functions, protocols |
| | **Ruby & PHP** | `.rb`, `.php` | Classes, modules, methods, functions |
| | **Shell Scripts** | `.sh`, `.bash`, `.zsh` | Function definitions, exported variables |
| | **Scala, Dart, Lua, Solidity** | `.scala`, `.dart`, `.lua`, `.sol` | Contracts, structs, classes, functions |
| | **Custom Frameworks** | `.vue`, `.svelte`, `.prisma`, `.graphql` | Configurable via `ast.additional_extensions` in `.gdss.yml` |

### 2. Multi-Asset Domain Reducers (Non-Code Intelligence)

Modern pull requests touch more than just code. GDSS includes built-in domain reducers for non-code files:

- **📦 Dependency Manifests (`package.json`, `Cargo.toml`, `pyproject.toml`)**:
  - Independent package additions commute ($[\mathcal{T}_A, \mathcal{T}_B] = 0$).
  - Overlapping additions with divergent version constraints trigger collision warnings.
  - Automatic lockfile synchronization in the sandbox (`pnpm-lock.yaml`, `yarn.lock`, `Cargo.lock`, `package-lock.json`).
- **🗄️ Database Migrations (Split-Head Hazard)**:
  - Supports Alembic, Django, Prisma (`migrations/`, `schema.prisma`), Drizzle (`_journal.json`), Flyway, and Rails.
  - Detects when two concurrent PRs branch from the **exact same parent migration revision**, creating a critical **Split-Head Hazard** ($\Omega = 1.0$) before it breaks database CI.
- **🗃️ SQL Schemas (`.sql` DDL)**:
  - Parses `CREATE TABLE`, `ALTER TABLE`, and column mutations to detect DDL conflicts across branches.
- **📝 Documentation (`.md`)**:
  - Scopes diffs to ATX headings (`#`, `##`), preventing merge conflicts when authors add distinct sections to the same document.
- **⚙️ Configuration & Infrastructure (`.yml`, `.yaml`, `.env`, `Dockerfile`)**:
  - Line-level lexical chunk diffing with proximity awareness.

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

> [!TIP]
> **Unified Configuration:** All radar tuning parameters (such as `model`, `risk_threshold`, `fail_on_collision`, `context_window`, and test commands) are configured in the single **`.gdss.yml`** file at your repository root. This ensures your local CLI and CI runner always behave identically.

---

## ⚙️ The Configuration File (`.gdss.yml`)

GDSS is zero-config by default, but **committing a `.gdss.yml` file to the root of your repository is the recommended best approach** for production teams.

> [!IMPORTANT]
> ### 📁 Exact File Naming & Directory Locations (Avoid Common Mistakes)
> Please ensure you distinguish between the two separate files:
>
> | File | Location | Purpose |
> | :--- | :--- | :--- |
> | **GitHub Actions Workflow** | `.github/workflows/gdss-radar.yml` | Tells GitHub Actions when to run and invokes `sarthak2233/gdss-radar@v1`. |
> | **Repository Configuration** | **`/.gdss.yml`** *(at repository root)* | Configures model, risk thresholds, validation commands, and branch rules. |
>
> ⚠️ **Common Mistake:** Do **NOT** name the root configuration file `.gdss-radar.yml`. The GDSS engine specifically searches for **`.gdss.yml`** (or `.gdss.yaml`). If you name it `.gdss-radar.yml`, it will not be detected.
>
> ```text
> your-project/
> ├── .github/
> │   └── workflows/
> │       └── gdss-radar.yml       <-- GitHub Actions workflow trigger
> ├── .gdss.yml                   <-- Engine configuration (MUST be .gdss.yml, NOT .gdss-radar.yml)
> ├── package.json (or pyproject.toml / go.mod)
> └── src/
> ```

---

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

## 💡 Essential Concepts: What Every User Must Know

Here are the key operational principles and behaviors you should know when using GDSS Radar:

### 1. 💰 $0 API Cost & Zero Token Bleed Core
The deterministic core of GDSS — AST parsing, dependency graphs, caller/callee tracing, domain reducers, and the $\Omega_{ij}$ collision tensor — runs **100% locally on CPU within the GitHub Actions runner**. It costs **literal $0 and 0 tokens** for every repository.

### 2. 🤖 Free AI Suggestions via GitHub Models
GDSS does not require you to purchase third-party OpenAI or Anthropic credits. In GitHub Actions, it automatically authenticates with **GitHub Models** using your workflow's `${{ secrets.GITHUB_TOKEN }}`, giving your team free candidate reconciliation patches.

### 3. ⚠️ Mandatory `fetch-depth: 0`
Standard GitHub Actions checkout uses `depth: 1` (a shallow copy of only the latest commit). GDSS **requires `fetch-depth: 0`** to inspect the Git DAG, identify the common ancestor merge-base between concurrent branches, and calculate trajectory mutations ($dC$).

### 4. 📌 Single Sticky Comment (Zero Notification Spam)
GDSS never spams your pull request with dozens of new comments. It posts **one single sticky comment** on PR creation and updates (`PATCH`) it in-place whenever new commits are pushed. When a collision is resolved, the comment automatically updates to an **"All Clear ✅"** status.

### 5. ⚡ Ephemeral Sandbox Validation
AI candidate suggestions are **never presented blind**. Before rendering a suggestion in Markdown, GDSS creates an isolated temporary Git worktree on the runner, applies the candidate patch, and executes your configured `test_command` and `typecheck_command`. If errors occur, it feeds compiler traces back to the model (up to `max_refinements`) to self-heal the patch.

### 6. 🧑‍✈️ Strict Human-in-the-Loop (Never Auto-Merges)
GDSS is a convergence assistant, **not an auto-merge robot**. It never commits code to your branch or merges branches automatically. Reconciliations are presented as GitHub native ````suggestion` blocks. A developer must review the proposed changes and click **"Commit suggestion"** to accept them.

### 7. 🔀 Multi-PR Batching via Pairwise 3-Way Triangles
If your team has 10 concurrent PRs open, GDSS does **not** dump all 10 branches into one gigantic, hallucination-prone prompt. Instead, it computes **isolated pairwise triangles**:
`Base (main)` vs `Incoming PR` vs `Concurrent PR #i`
This ensures predictable token usage and prevents cross-feature hallucinations.

### 8. 🛡️ Fork Pull Requests & Security
For repositories receiving pull requests from forks, GitHub Actions restricts write permissions on secrets by default. GDSS operates cleanly with read-only access for collision calculation, or you can run it on internal team branches where full sticky comments and sandboxing are enabled.

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

1. In your **`.gdss.yml`** at the repository root, set `fail_on_collision: true`:
```yaml
# In .gdss.yml
radar:
  fail_on_collision: true  # Exits with code 1 if severe collision is detected
  risk_threshold: 0.2      # Severity threshold (0.0 to 1.0)
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


Developers can click **"Commit suggestion"** directly inside the GitHub PR UI to land the validated reconciliation with zero manual cherry-picking.

---

## 💻 Standalone Binary & IDE MCP Server (Cursor & Claude Code)

Want to run collision checks locally on your laptop, or give **Cursor**, **Claude Code**, and **VS Code Copilot** direct access to GDSS intelligence?

Install the pre-compiled standalone binary (**zero Python installation required**).

### 🚀 1-Line Universal Installer

**macOS & Linux (Bash):**
```bash
curl -fsSL https://raw.githubusercontent.com/sarthak2233/gdss-radar/main/install.sh | bash
```

**Windows (PowerShell):**
```powershell
irm https://raw.githubusercontent.com/sarthak2233/gdss-radar/main/install.ps1 | iex
```

### 🧠 Connect to Cursor (`~/.cursor/mcp.json`)
Add GDSS to your global Cursor MCP configuration:
```json
{
  "mcpServers": {
    "gdss": {
      "command": "gdss",
      "args": ["mcp"]
    }
  }
}
```

### 🤖 Connect to Claude Code
```bash
claude mcp add gdss -- gdss mcp
```

### 🛠️ What Your AI Assistant Can Do:
Your IDE AI agent gains 6 deterministic, zero-token tools:
- `gdss_find_collisions`: Checks your active branch against teammates' open branches.
- `gdss_get_merge_order`: Computes mathematically optimal branch landing sequence.
- `gdss_inspect_symbol_impact`: Traces blast radius of any function or class refactor.
- `gdss_plan_semantic_rebase`: Rebase recommendations distinguishing signature shifts from noise.
- `gdss_sandbox_validate_patch`: Tests candidate patches in ephemeral Git worktrees.
- `gdss_validate_patch`: Instant syntax and static type verification.

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
