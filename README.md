# 🛰️ GDSS PR Collision Radar Action

[![GitHub Marketplace](https://img.shields.io/badge/Marketplace-GDSS%20Radar-blue?logo=github&style=flat-square)](https://github.com/marketplace/actions/gdss-pr-collision-radar)
[![Release](https://img.shields.io/github/v/release/sarthak2233/gdss-radar?style=flat-square&color=success)](https://github.com/sarthak2233/gdss-radar/releases)
[![License](https://img.shields.io/badge/License-GSARL--1.0-blue.svg?style=flat-square)](LICENSE)
[![Deterministic Core](https://img.shields.io/badge/Core-Deterministic%20$0%20Tokens-brightgreen?style=flat-square)](#-privacy-deterministic-core--bring-your-own-key-byok)
[![AI Model](https://img.shields.io/badge/AI%20Convergence-BYOK%20%2F%20BYOC-blue?style=flat-square)](#-privacy-deterministic-core--bring-your-own-key-byok)
[![Docker Image](https://img.shields.io/badge/Container-GHCR%20Runner-purple?logo=docker&style=flat-square)](https://github.com/Sarthak2233/GDSS/pkgs/container/gdss-runner)

> **Air Traffic Control for GitHub Pull Requests.**  
> Automatically detects cross-branch semantic collisions across all concurrently open PRs, tests candidate reconciliations in ephemeral sandboxes, and posts a single sticky Markdown radar report with 1-click **"Commit suggestion"** blocks.

> [!TIP]
> **Recommended AI Setup for Convergence Patches**: We explicitly recommend using **[OpenRouter](https://openrouter.ai)** (`provider: "openrouter"`, `model: "openrouter/free"` or Claude/DeepSeek) authenticated via the dedicated **`GDSS_API_KEY`** secret. OpenRouter requires zero credit card setup for its free tier and grants single-key access to 100+ models. Using `GDSS_API_KEY` guarantees that GDSS credentials never collide with or burn your application's existing `.env` or CI secrets.

---

### 🤖 1-Prompt Integration for AI Agents (Cursor, Claude Code, Copilot, Antigravity)

Copy and paste this directive to your AI coding assistant to autonomously configure and integrate GDSS PR Collision Radar into your repository:

```text
Integrate GDSS PR Collision Radar into this repository using the authoritative sources from https://github.com/sarthak2233/gdss-radar:

1. Repository Inspection:
   - Identify our build descriptors, package managers (npm, pnpm, yarn, cargo, poetry, pip, go), test frameworks (pytest, vitest, jest, cargo test, go test), static typecheckers (mypy, tsc), and database migration directories (alembic, prisma, django, drizzle, rails, flyway).
   - Identify our primary landing branch (main or develop).

2. Fetch Reference Specs & Schemas:
   - Consult the official configuration guide and examples at:
     https://raw.githubusercontent.com/sarthak2233/gdss-radar/main/.gdss.yml.example
     https://raw.githubusercontent.com/sarthak2233/gdss-radar/main/docs/CONFIGURATION_GUIDE.md

3. Create Repository Configuration (.gdss.yml):
   - Create .gdss.yml in the repository root with `version: 1`.
   - Configure `radar:` section with:
     * `provider: "openrouter"` (explicitly recommended multi-model gateway)
     * `model: "openrouter/free"` (or "anthropic/claude-3.5-sonnet", "deepseek/deepseek-chat")
     * `context_window: 128000` (or matching model token limit)
     * `api_key_env: "GDSS_API_KEY"` (dedicated key variable to prevent secret collision)
   - Configure `validation:` section with:
     * `mode: "targeted"`
     * `symlink_dependencies: true`
     * Tailored `test_command` (using {test_file} or {file} placeholder)
     * Tailored `typecheck_command` (if static typechecker is present)
     * Optional `install_command` if needed for ephemeral worktrees.
   - If database migrations exist, configure `migrations:` section with framework hint and paths.
   - Configure `hierarchy:` with `reference_branch` matching our default branch and `same_base_only: true`.
   - Configure `ast:` with appropriate `exclude_patterns`.

4. Create GitHub Actions CI Workflow:
   - Create `.github/workflows/gdss-radar.yml` triggering on `pull_request: [opened, synchronize, reopened]`.
   - Use `actions/checkout@v4` with `fetch-depth: 0` (mandatory for Git DAG merge-base resolution).
   - Invoke `sarthak2233/gdss-radar@v1.1.1` (or `@v1`) with `token: ${{ secrets.GITHUB_TOKEN }}`.
   - Set env with `GDSS_API_KEY: ${{ secrets.GDSS_API_KEY }}` (or `OPENROUTER_API_KEY: ${{ secrets.OPENROUTER_API_KEY }}`).
   - Set concurrency group `gdss-radar-${{ github.event.pull_request.number }}` with `cancel-in-progress: true`.
   - Add permissions: `contents: read`, `pull-requests: write`, `issues: write`.

5. Verification:
   - Verify that .gdss.yml is valid YAML and the workflow syntax is clean.
   - Do NOT modify any existing application code or logic.
```

---

## 📑 Table of Contents
- [🤖 1-Prompt Integration for AI Agents](#-1-prompt-integration-for-ai-agents-cursor-claude-code-copilot-antigravity)
- [📚 Documentation Library](#-documentation-library)
- [💰 Pricing & Commercial Model](#-pricing--commercial-model)
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
- [🛡️ Privacy, Deterministic Core & Bring Your Own Key (BYOK)](#-privacy-deterministic-core--bring-your-own-key-byok)

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
| | **C# (.NET 6-9)** | `.cs`, `.csx` | Namespaces, classes, records, structs, interfaces, methods, constructors, properties with `required`/`init`, generic constraints |
| | **Ruby** | `.rb` | Classes, modules, methods, singleton methods |
| | **Shell Scripts** | `.sh`, `.bash` | Function declarations, invocations, subshells |
| **Tier 2: Sub-Document Projection** | **Blazor & Razor** | `.razor`, `.cshtml` | Extracts embedded `@code` and `@functions` blocks mapped 1-to-1 to C# AST |
| | **Vue & Svelte** | `.vue`, `.svelte` | Extracts `<script>` and `<script setup>` tags mapped 1-to-1 to TS/JS AST |
| **Tier 3: Lexical Advisory Mode** | **Kotlin, Swift, PHP, Dart, Scala** | `.kt`, `.swift`, `.php`, etc. | Transparent chunk diffing with `lexical_advisory: true` warning; safe commutativity |

### 2. Multi-Asset Domain Reducers (Non-Code Intelligence)

Modern pull requests touch more than just code. GDSS includes built-in domain reducers for non-code files:

- **📦 Dependency Manifests & MSBuild DAGs**:
  - `package.json`, `Cargo.toml`, `pyproject.toml`.
  - `.csproj`, `.props`, `.targets`, `Directory.Build.props`, `Directory.Packages.props`, `.sln`, `.slnx`.
  - Tracks NuGet `<PackageReference>`, Central Package Management (CPM) `<PackageVersion>`, project couplings `<ProjectReference>`, and Visual Studio solution project memberships.
  - Independent package additions commute ($[\mathcal{T}_A, \mathcal{T}_B] = 0$). Overlapping additions with divergent version constraints trigger collision warnings.
  - Automatic lockfile synchronization in the sandbox (`pnpm-lock.yaml`, `yarn.lock`, `Cargo.lock`, `package-lock.json`).
- **🗄️ Database Migrations (Split-Head Hazard)**:
  - Supports Alembic, Django, Prisma (`migrations/`, `schema.prisma`), Drizzle (`_journal.json`), Flyway, and Rails.
  - Detects when two concurrent PRs branch from the **exact same parent migration revision**, creating a critical **Split-Head Hazard** ($\Omega = 1.0$) before it breaks database CI.
- **🗃️ SQL Schemas (`.sql` DDL)**:
  - Parses `CREATE TABLE`, `ALTER TABLE`, and column mutations to detect DDL conflicts across branches.
- **📝 Documentation (`.md`)**:
  - Scopes diffs to ATX headings (`#`, `##`), preventing merge conflicts when authors add distinct sections to the same document.
- **🛡️ Noise & Generated Code Filtering**:
  - Automatically filters out compiler artifacts (`*.Designer.cs`, `*.g.cs`, `*AssemblyInfo.cs`, `*.pb.go`, `*.min.js`, `bin/**`, `obj/**`, `target/**`) from collision scoring.
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
        uses: sarthak2233/gdss-radar@v1.1.1  # Or @v1
        with:
          token: ${{ secrets.GITHUB_TOKEN }}
        env:
          # Optional: Bring Your Own Key for AI suggestions (omit for 100% deterministic $0 radar)
          # RECOMMENDED: Set GDSS_API_KEY using OpenRouter (or OPENROUTER_API_KEY):
          GDSS_API_KEY: ${{ secrets.GDSS_API_KEY }}
          # or: OPENROUTER_API_KEY: ${{ secrets.OPENROUTER_API_KEY }}
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
| `uses: sarthak2233/gdss-radar@v1.1.1`<br>*(or `@v1`)* | Runs the pre-compiled container image (`ghcr.io/sarthak2233/gdss-runner:v1`) directly on GitHub's Ubuntu runner. | **Zero Setup:** No Python installation, no virtual environment creation, and no pip build overhead required. |
| `with.token: ${{ secrets.GITHUB_TOKEN }}` | **The only action input.** Built-in GitHub token automatically provided to every Actions workflow. Used strictly to discover concurrent PRs and post/update the sticky radar comment. | Action cannot query GitHub REST API to discover concurrent pull requests or post sticky report comments. |
| `env.GDSS_API_KEY`<br>*(or `env.OPENROUTER_API_KEY`)* | **Recommended standard.** Passes your secret into the runner for AI convergence patch generation (BYOK). Using `GDSS_API_KEY` isolates GDSS credentials from your existing project `.env` or application secrets. | If omitted, GDSS runs purely in deterministic mode ($0 & 0 tokens), outputting the collision radar and merge order without AI text patches. |

> [!IMPORTANT]
> **Single Action Input (`token`)**: Notice that `with:` only accepts `token`. All radar tuning parameters (such as `risk_threshold`, `fail_on_collision`, `provider`, and `model`) are configured in the single **`.gdss.yml`** file at your repository root. Do not pass `threshold` or `provider` under `with:`.

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

# Configuration schema version
version: 1

# ------------------------------------------------------------------------------
# 1. AI Convergence & PR Collision Radar
# ------------------------------------------------------------------------------
radar:
  # Inference provider backend: "openrouter" is explicitly recommended (multi-model gateway, $0 free tier, no credit card required)
  provider: "openrouter"  # "openrouter" (recommended), "deepseek", "groq", "together", "mistral", "anthropic", "gemini", "cohere", "perplexity", "openai", "azure", "copilot", "ollama", "vllm", "lm-studio", "localai", "openai-compatible"
  # Model name on provider:
  # - "openrouter/free" (zero-cost router, no credit card required)
  # - "anthropic/claude-3.5-sonnet", "deepseek/deepseek-chat", "gpt-4o", "meta-llama/llama-3.3-70b-instruct"
  model: "openrouter/free"
  context_window: 128000  # Explicit token ceiling (MANDATORY BEST PRACTICE)
  # Dedicated API key variable name (optional).
  # Setting GDSS_API_KEY in .env or CI secrets isolates GDSS credentials from your application's own keys.
  api_key_env: "GDSS_API_KEY"
  risk_threshold: 0.1     # Sensitivity: 0.05 (sensitive) to 0.25 (strict)
  max_refinements: 2      # Sandboxed self-correction loops on compiler errors
  concurrency_limit: 4    # Concurrent worker threads for parallel conflict drafting
  condensation: "lossless" # Context policy: "lossless", "ast_minimal", or "off"
  fail_on_collision: false

# ------------------------------------------------------------------------------
# 2. Ephemeral Sandbox Validation
# ------------------------------------------------------------------------------
validation:
  mode: "targeted"         # "targeted" (filtered to changed files) or "full_suite"
  symlink_dependencies: true # Symlink parent dependencies (node_modules, .venv) into worktree
  # install_command: "npm ci" # Optional install command when manifests change
  test_command: "npm test -- {test_file}"
  typecheck_command: "npx tsc --noEmit"
  timeout_seconds: 60
  anti_gaming_strict: true # Strictly forbid deleting assertions to force failing tests to pass
  allow_callsite_updates: true # Allow adapting test call-site signatures to match refactored functions

# ------------------------------------------------------------------------------
# 3. Deterministic Semantic Impact Closure (SIC) Engine
# ------------------------------------------------------------------------------
# Transitive blast radius expansion across reverse call graphs to find affected callers,
# contracts, and test suites. Follows Two-Tier Precedence (Explicit Globs > Built-in Heuristics).
impact_closure:
  max_hops: 2             # Reverse caller depth (default: 2)
  relevance_threshold: 0.70 # Minimum edge-weight cutoff for transitive caller inclusion
  token_budget: 4000      # Token ceiling for packaging closure context
  # Explicit glob overrides (Tier 1 Primary Authority)
  test_patterns:
    - "tests/**"
    - "spec/**"
    - "e2e/**/*.spec.ts"
  contract_patterns:
    - "proto/**"
    - "schemas/**/*.json"
    - "src/contracts/**"

# ------------------------------------------------------------------------------
# 4. Branch Hierarchy & Flight Boundaries
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
# 5. AST Symbol & Path Filtering
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

# ------------------------------------------------------------------------------
# 6. Database Migration Reducer (Universal Multi-Stack DAG Intelligence)
# ------------------------------------------------------------------------------
migrations:
  # Framework hint (optional): "alembic", "django", "prisma", "drizzle", "flyway", "rails", "knex", "typeorm", "goose", "atlas"
  # When omitted, GDSS applies universal timestamp/sequence pattern matching.
  # framework: "alembic"
  paths:
    - "migrations/**"
    - "alembic/versions/**"
    - "db/migrate/**"
    - "prisma/migrations/**"
```

---

### 🔍 Detailed Schema Reference & Field Breakdown

#### Section 0: Root Directives
| Field | Type | Default | What It Does & Recommended Values |
| :--- | :--- | :--- | :--- |
| `version` | `integer` | `1` | Schema version indicator. Guarantees backwards-compatible schema parsing. |

#### Section 1: `radar` (AI Convergence & Collision Detection)
| Field | Type | Default | What It Does & Recommended Values |
| :--- | :--- | :--- | :--- |
| `provider` | `string` | `"openrouter"` | **Explicitly recommended backend:** `"openrouter"` (multi-model gateway, $0 free tier via `openrouter/free`, single key for 100+ models). Also supports `"copilot"` (GitHub Copilot CLI via `GITHUB_TOKEN`), `"azure"`, `"openai"`, `"deepseek"`, `"groq"`, `"ollama"`. |
| `model` | `string` | `"openrouter/free"` | The model ID used for semantic patch reconciliation. Common choices: `"openrouter/free"`, `"anthropic/claude-3.5-sonnet"`, `"deepseek/deepseek-chat"`, `"gpt-4o"`, `"meta-llama/llama-3.3-70b-instruct"`. |
| `context_window` | `integer` | `128000` | **Mandatory Best Practice.** Declares the exact token window capacity. Allocates up to 45% of tokens to symbol definitions. Prevents fallback to 16,384 tokens. |
| `api_key_env` | `string \| null` | `null` | Custom environment variable name storing the API key (e.g. `"GDSS_API_KEY"`). When set or when `GDSS_API_KEY` exists in `.env` / CI, GDSS uses this key with highest priority, isolating GDSS from project app keys. |
| `risk_threshold` | `float` | `0.1` | Collision intensity threshold ($\Omega_{ij} \in [0.0, 1.0]$).<br>• `0.05`: High sensitivity (flags subtle caller dependencies)<br>• `0.10`: Recommended default balance<br>• `0.25`: Strict (flags only direct signature changes)<br>• `0.80`: Critical only (flags conflicting implementations) |
| `max_refinements` | `integer` | `2` | Number of automated self-correction loops. If tests fail in the sandbox worktree, compiler error traces are fed back to the model to refine the patch up to $N$ times. |
| `fail_on_collision` | `boolean` | `false` | If `true`, the CI job exits with code `1` when severe collisions are detected (used for required branch protection status checks). |

#### Section 2: `validation` (Ephemeral Worktree Sandbox)
| Field | Type | Default | What It Does & Recommended Values |
| :--- | :--- | :--- | :--- |
| `mode` | `string` | `"targeted"` | Validation scope: `"targeted"` (constrains test/typecheck runs strictly to changed files / targeted tests) or `"full_suite"` (runs unconstrained suite). |
| `symlink_dependencies` | `boolean` | `true` | When `true`, automatically symlinks root and subpackage dependency directories (`node_modules`, `.venv`, `vendor`) from parent repo into ephemeral worktrees if manifests are untouched. |
| `install_command` | `string \| null` | `null` | Shell command executed inside the ephemeral worktree prior to typecheck and tests (e.g. `npm ci`, `poetry install`). |
| `test_command` | `string` | `""` | Command to execute targeted unit tests in the isolated sandbox. Supports `{test_file}` interpolation (e.g. `pytest {test_file}`, `npm test -- {test_file}`). |
| `typecheck_command` | `string` | `""` | Command to verify static types or lint rules in the sandbox. Supports `{file}` interpolation (e.g. `npx tsc --noEmit`, `mypy {file}`, `cargo check`). |
| `timeout_seconds` | `integer` | `60` | Hard timeout ceiling for test execution in the ephemeral sandbox worktree to prevent hangs. |
| `anti_gaming_strict` | `boolean` | `true` | When `true`, strictly forbids the convergence engine from deleting or dropping test assertions to force a failing test to pass. |
| `allow_callsite_updates` | `boolean` | `true` | When `true`, permits adapting test call-site invocations to updated function signatures (e.g. adding required arguments) without triggering anti-gaming penalties. |

#### Section 3: `impact_closure` (Deterministic Semantic Impact Closure)
Computes the transitive blast radius around conflict symbols ($C_0$). Follows the **Two-Tier Precedence Strategy**:
- **Tier 1 (Explicit Configuration)**: Glob patterns defined in `.gdss.yml` take primary authority.
- **Tier 2 (Final Resort / Built-in Heuristics)**: If omitted or unmatched, GDSS automatically applies built-in multi-language indicators (supporting Python, TS/JS, Go, Rust, C#, Java, C/C++).

| Field | Type | Default | What It Does & Recommended Values |
| :--- | :--- | :--- | :--- |
| `max_hops` | `integer` | `2` | Maximum depth of reverse caller graph traversal ($C_0 \rightarrow D(C_0) \rightarrow \text{transitive callers}$). |
| `relevance_threshold` | `float` | `0.70` | Relevance edge-weight cutoff ($0.0$ to $1.0$). Distant or weak caller edges below this cutoff are pruned from convergence prompts. |
| `token_budget` | `integer` | `4000` | Context token ceiling for semantic impact closure packaging. Pruned via Knapsack prioritization ($C_0 > \text{Tests} > \text{Direct Callers} > \text{Transitive Callers}$). |
| `test_patterns` | `list` | `[]` | Explicit glob patterns for test files (e.g. `["tests/**", "spec/**", "e2e/**/*.spec.ts"]`). Evaluated **before** built-in heuristics. |
| `contract_patterns` | `list` | `[]` | Explicit glob patterns for API schemas, IDLs, and interface definitions (e.g. `["proto/**", "schemas/**/*.json"]`). Evaluated **before** built-in heuristics. |

#### Section 4: `hierarchy` (Branch Selection & Attractor Precedence)
| Field | Type | Default | What It Does & Recommended Values |
| :--- | :--- | :--- | :--- |
| `reference_branch` | `string` | `"main"` | The primary attractor base branch against which feature branches are audited. |
| `priority_chain` | `list` | `["main"]` | Ordered fallback list of base branches for multi-stage landing pipelines (e.g. `["main", "develop", "staging"]`). |
| `same_base_only` | `boolean` | `true` | **Crucial for multi-branch repos.** When `true`, only PRs targeting the exact same base landing branch are audited against each other, eliminating false alarms between production and maintenance branches. |
| `include` | `list` | `[]` | Glob patterns of branch names to audit (e.g. `["feature/*", "fix/*"]`). |
| `exclude` | `list` | `[]` | Glob patterns of branch names to ignore (e.g. `["dependabot/*", "renovate/*", "spike/*"]`). |
| `target_bases` | `list` | `["main"]` | Specific base branches that activate collision auditing when targeted by incoming PRs. |

#### Section 5: `ast` (Syntax Parsing & Exclusions)
| Field | Type | Default | What It Does & Recommended Values |
| :--- | :--- | :--- | :--- |
| `additional_extensions` | `list` | `[]` | Additional file extensions mapped to AST or Domain Reducer parsers (e.g. `[".vue", ".svelte", ".sol"]`). |
| `exclude_patterns` | `list` | `[]` | File paths to exclude from symbol parsing and collision computation (e.g. `["node_modules/**", "dist/**", ".venv/**"]`). |

#### Section 6: `migrations` (Universal Multi-Stack DAG Reducer)
| Field | Type | Default | What It Does & Recommended Values |
| :--- | :--- | :--- | :--- |
| `framework` | `string \| null` | `null` | Framework hint (`"alembic"`, `"django"`, `"prisma"`, `"drizzle"`, `"flyway"`, `"rails"`, `"knex"`, `"typeorm"`, `"goose"`, `"atlas"`). When omitted, universal timestamp and sequence matching is used. |
| `paths` | `list` | `["migrations/**", ...]` | Glob patterns where database migrations reside for DAG split-head collision detection. |

---

## 💡 Essential Concepts: What Every User Must Know

Here are the key operational principles and behaviors you should know when using GDSS Radar:

### 1. 💰 100% Free Deterministic Core ($0 & 0 Tokens)
The deterministic core of GDSS — AST parsing, dependency graphs, caller/callee tracing, domain reducers, and the $\Omega_{ij}$ collision tensor — runs **100% locally on CPU within the GitHub Actions runner**. It costs **literal $0 and consumes 0 tokens** for every repository, requiring zero third-party API keys.

### 2. 🤖 Bring Your Own Key (BYOK) AI Convergence
GDSS is not a paid AI middleman and does not provide or subsidize cloud LLM inference. For synthesizing natural-language conflict explanations and automated 1-click reconciliation patches, GDSS uses a strict **Bring Your Own Key (BYOK)** / **Bring Your Own Compute (BYOC)** model:
- **OpenRouter Free Tier ($0, No Credit Card):** Set `provider: "openrouter"` and `model: "openrouter/free"` in `.gdss.yml` and provide `OPENROUTER_API_KEY` in GitHub Secrets. The `openrouter/free` meta-router automatically connects to active zero-cost models with coding capabilities without requiring credits. (Note: specific endpoints like `qwen/qwen-2.5-coder-32b-instruct` without a `:free` suffix are billed at standard token rates on OpenRouter).
- **OpenAI / DeepSeek / Direct Providers:** Provide `OPENAI_API_KEY` or `DEEPSEEK_API_KEY` in GitHub Secrets. Network requests travel directly from the runner to the provider without intermediate GDSS proxies.
- **GitHub Copilot CLI in CI:** If your organization has Copilot Business or Enterprise, set `provider: "copilot"`, add `copilot-requests: write` to your workflow permissions, and install `@github/copilot`. GDSS strictly runs Copilot with `--deny-tool='*'` so it acts purely as an inference engine to stdout and cannot edit repository files directly.
- **Deterministic Baseline ($0 Tokens, No Keys):** If no AI keys are provided, GDSS runs completely deterministically on runner CPU, generating collision interaction tensors ($\Omega_{ij}$), risk topologies, and optimal merge order sequences.

> [!TIP]
> **How Team Credentials Work in GitHub Actions:**  
> Does every developer on your team need an individual API key? **No.**  
> The repository administrator configures your team's API key (e.g. `OPENAI_API_KEY`, `DEEPSEEK_API_KEY`, or `OPENROUTER_API_KEY`) **once** in GitHub Repository Secrets (`Settings ➔ Secrets and variables ➔ Actions`).  
> When any developer opens a PR, GitHub Actions automatically passes the repository secret to the runner. Individual developers never need to manage or expose personal keys.  
> If no secret is configured, GDSS runs purely in deterministic mode ($0 & 0 tokens) and generates full collision matrices and merge orders without AI text generation.

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
  provider: "openrouter"
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
  provider: "openrouter"
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
  provider: "openrouter"
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
  provider: "openrouter"
  risk_threshold: 0.1

validation:
  test_command: "cargo test --test {test_file}"
  typecheck_command: "cargo check"
  timeout_seconds: 90

hierarchy:
  reference_branch: "main"
  same_base_only: true
```

### 5. .NET / C# (Solution & Project Builds)
```yaml
radar:
  model: "gpt-4o"
  context_window: 128000
  provider: "openrouter"
  risk_threshold: 0.1
  max_refinements: 2

validation:
  test_command: "dotnet test --no-build"
  typecheck_command: "dotnet build --no-incremental"
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
Your IDE AI agent gains 8 deterministic, zero-token tools:
- `gdss_find_collisions`: Checks your active branch against teammates' open branches.
- `gdss_get_merge_order`: Computes mathematically optimal branch landing sequence.
- `gdss_inspect_symbol_impact`: Traces callers, contracts, and verifying tests (2-hop SIC).
- `gdss_get_impact_closure`: Computes 2-hop Semantic Impact Closure with token budget pruning.
- `gdss_plan_semantic_rebase`: Rebase recommendations distinguishing signature shifts from noise.
- `gdss_validate_patch`: Multi-file, multi-language Tree-sitter AST and in-memory diff validation.
- `gdss_sandbox_validate_patch`: Tests candidate patches in ephemeral Git worktrees with structured diagnostics ($E_k$).
- `gdss_check_anti_gaming`: Audits test modifications against assertion-preservation invariants.

---

## 💰 Licensing & Commercial Safe Harbor

GDSS is distributed under a **Source-Available, Free Community Safe Harbor** model (governed by the [GDSS Source-Available Reciprocal License 1.0 (GSARL 1.0)](LICENSE)). We believe that PR collision radar and deterministic repository intelligence should be freely accessible to developers and small teams without cost friction:

> [!NOTE]
> **Licensing Architecture**: Both this GitHub Action repository (`gdss-radar`) and the underlying core engine (`GDSS`) are governed by the **GDSS Source-Available Reciprocal License 1.0 (GSARL 1.0)**. Free production usage is granted under the Community Safe Harbor for individual developers, non-commercial open-source projects, and organizations with up to 20 active Git contributors.

| Plan | Pricing | Target Audience | What is Included |
| :--- | :---: | :--- | :--- |
| **Community Safe Harbor** | **$0 / Free Forever** | Individual developers, public open-source projects, and teams with up to 20 active Git contributors | • **100% Free Deterministic Core** ($0 & 0 tokens charged by GDSS)<br>• Unlimited pull request collision radar audits on single repos & monorepos<br>• Full pairwise branch collision matrix . 3-tier multi-language AST parsing (Python, TS/JS, Go, Rust, Java, C/C++ + universal structural fallback)<br>• Ephemeral worktree sandbox validation (syntax, types, targeted unit tests)<br>• Bring Your Own Key (BYOK) AI convergence suggestions (Ollama, vLLM, OpenRouter, GitHub Models)<br>• Standalone CLI (`gdss`) & IDE MCP Server for Cursor, Claude Code, and Copilot |
| **Commercial / Enterprise** | **Commercial Agreement** | Organizations with > 20 active Git contributors, or enterprises requiring private modifications | • **Enterprise Commercial License** (authorizes production use for teams with > 20 active contributors)<br>• **Private Modification Waiver** (allows deploying internal proprietary forks without mandatory reciprocal share-back under GSARL 1.0)<br>• Dedicated onboarding, integration support, and commercial SLA<br>• Priority issue triage and upstream feature steering |

> [!IMPORTANT]
> **Scope & Multi-Repository Boundary**:  
> GDSS currently analyzes concurrent branches and pull requests **within a single Git repository or multi-package monorepo**. Cross-repository contract synchronization across independent microservice repositories is an architectural problem space and research track (see [`Distributed Collaboration Across Repositories and Organizations.md`](../docs/problem-definations/Distributed%20Collaboration%20Across%20Repositories%20and%20Organizations.md)) and is not an active product feature.

> 💬 **Enterprise Inquiries:**  
> To discuss enterprise licensing for teams with > 20 active contributors, private modification waivers, or custom agreements, start a thread on [GitHub Discussions](https://github.com/sarthak2233/gdss-radar/discussions) or contact sarthak.basnet5@gmail.com.

---

## 🛡️ Privacy, Deterministic Core & Bring Your Own Key (BYOK)

### 1. 100% Free Deterministic Core ($0 Compute & 0 Tokens)
The deterministic collision radar runs entirely on local CPU:
- Abstract Syntax Tree (AST) parsing across 20+ languages.
- Blast radius caller/callee graphs and reverse impact closure.
- Pairwise collision tensor calculations ($\Omega_{ij}$) and optimal topological merge ordering.
- Ephemeral Git worktree validation with your native test and typecheck runners.

**GDSS charges $0 and requires zero tokens or API keys for core collision analysis.** If no AI API key is configured, the action completes successfully with full risk tables and merge sequence recommendations.

---

### 2. Strict Bring Your Own Key (BYOK) / BYOC
GDSS is not an LLM broker, markup reseller, or paid inference proxy. When AI convergence patch generation is enabled, requests travel directly between your runner/laptop and your model provider.

#### 🌟 Explicit Recommendation: OpenRouter + `GDSS_API_KEY`
For teams enabling AI convergence patches, we explicitly recommend **[OpenRouter](https://openrouter.ai)**:
1. **Multi-Model Flexibility (Single Key):** Switch between DeepSeek V3, Claude 3.5 Sonnet, Llama 3.3, and GPT-4o simply by editing `model:` in `.gdss.yml`—no need to manage multiple vendor billing accounts.
2. **$0 Free Tier (Zero Credit Card):** Set `model: "openrouter/free"` to automatically route through high-quality free coding models with zero credit card setup.
3. **Dedicated Key Isolation (`GDSS_API_KEY`):** Most production repositories already maintain `.env` files with application secrets (e.g., your SaaS app's `OPENAI_API_KEY` or `DATABASE_URL`). Setting **`GDSS_API_KEY`** in your GitHub Secrets or local `.env` ensures GDSS never interferes with or exhausts your application's production tokens.
4. **Custom Team Secret Names (`api_key_env`):** If your organization mandates specific secret naming conventions (e.g. `TEAM_OPENROUTER_SECRET`), declare `api_key_env: "TEAM_OPENROUTER_SECRET"` under `radar:` in `.gdss.yml`.

---

### 3. How Authentication Works Across the 4 Deployment Modes

| Deployment Surface | Where Key Lives | How GDSS Reads It | Token Cost to GDSS |
| :--- | :--- | :--- | :--- |
| **GitHub Actions CI (`gdss audit-pr`)** | GitHub Repository Secrets (`GDSS_API_KEY` or `OPENROUTER_API_KEY`) | Injected via `env:` in `.github/workflows/gdss-radar.yml`. *(Note: CI runners do not read uncommitted `.env` files for security).* | **$0** (Billed directly to your provider or $0 on free tier) |
| **Local CLI (`gdss suggest`)** | Project root `.env` | **Zero-config auto-discovery!** GDSS automatically parses `.env` at your repository root without requiring manual `export` or `source`. | **$0** (Direct to provider) |
| **Web Dashboard & API (`gdss serve`)** | Project root `.env` | Automatically read from repository root `.env` or process environment on service start. | **$0** (Direct to provider) |
| **IDE MCP Server (Cursor / Claude Code)** | Handled by your IDE subscription | Runs locally over `stdio`. Exposes deterministic AST tools to your IDE agent. | **0 tokens & keyless** (Covered by your Cursor / Claude Code subscription) |

---

### 4. Zero Proprietary Cloud Ingestion & Strict Privacy
- **No Code Ingestion:** Your source code is never uploaded to any centralized GDSS cloud server or external training database.
- **Strict Privacy Boundaries:** All AST graphs and worktree test operations remain entirely contained within your runner or developer workstation.
- **Zero Telemetry:** GDSS respects open-source developer privacy and enforces strict zero-telemetry defaults.

---

## 📄 License

This repository and its action configurations are licensed under the **[GDSS Source-Available Reciprocal License 1.0 (GSARL 1.0)](LICENSE)**.  
Free for evaluation, research, and production use for teams with up to 20 active contributors. For enterprise licensing, organization agreements (>20 contributors), or private modification waivers, contact sarthak.basnet5@gmail.com or visit [GitHub Discussions](https://github.com/sarthak2233/gdss-radar/discussions).
