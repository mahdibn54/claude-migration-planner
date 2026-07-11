# 🧭 Migration Planner — Claude Code Plugin

**Evidence-based enterprise migration plans, generated from your actual repository. No generic advice.**

[![Claude Code Plugin](https://img.shields.io/badge/Claude%20Code-Plugin-d97757)](https://docs.claude.com/en/docs/claude-code/plugins)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](../../pulls)

Most migration advice is generic: *"use the strangler pattern", "migrate incrementally"*. This plugin is different — it reads your **real** dependency manifests, infrastructure definitions, and module structure, then produces an **execution-ready, phased migration plan** with dependency ordering, risk levels, effort estimates, and rollback strategies. Every claim is backed by evidence found in your repo; everything unverifiable is explicitly flagged as `unknown`.

## ✨ What it does

**You name only the target.** The agent inspects the repository, detects what you're currently running, and produces the detailed path between the two:

- 🔍 **Current-state detection** — exact framework and language versions, proven from your manifests **and lockfiles** (`pom.xml`, `package.json` + `package-lock.json`, `composer.json`, `requirements.txt`/`poetry.lock`, `go.mod`, `*.csproj`, `Cargo.toml`, `mix.exs`, `pubspec.yaml`, Dockerfile base images, `.nvmrc`/`.tool-versions`, …) — it never asks what you're on
- 📊 **Executive summary** — headline, total duration, recommended team size, and confidence level up front, so the person approving budget gets the answer in the first screen
- 🪜 **Upgrade path computation** — including mandatory intermediate hops (Spring Boot 2.3 → 2.7 → 3.x, Django 2 → 3.2 LTS → 4.2, AngularJS → hybrid ngUpgrade → Angular)
- 💥 **Breaking-changes mapping** — researched from the *official* upgrade guides for your exact version gap, then mapped to the **actual files in your repo** that each change affects, with available automation noted (OpenRewrite, Rector, codemods, `ng update`, .NET Upgrade Assistant)
- 🛑 **End-of-life detection** — components past EOL are flagged as security findings, because running unpatched software is a risk independent of the migration itself
- 🏗️ **Architecture analysis** — languages, frameworks, deployment model, complexity; monorepos get per-module version detection, and `--scope` limits analysis to one service
- 🕸️ **Dependency graph reasoning** — per-module risk levels and migration priority, with file-level evidence
- 🔗 **Hidden coupling detection** — shared databases, global state, implicit dependencies
- 🧪 **Safety-net assessment** — modules without tests are identified and get test scaffolding as explicit Phase 1 work, never a silent assumption
- 📋 **Phased execution plan** — ordered steps, estimated duration with confidence level, parallelization notes, verifiable **exit criteria**, risks and rollback strategy *per phase*
- ⚠️ **Critical risk register** — impact and mitigation for each
- 🎯 **Quick wins** — low-effort improvements you can ship this week
- ❓ **Unknowns ledger** — what couldn't be verified from the code, stated honestly

Works on **any stack the manifests reveal**: Java/Spring, Node/JS frameworks, Python/Django, PHP/Laravel/Symfony, .NET, Go, Ruby/Rails, Rust, Elixir, Swift, Flutter/Dart, Scala, databases, and infrastructure.

Typical targets:

| From | To |
|---|---|
| Java 8 | Java 21 |
| Spring Boot 2 | Spring Boot 3 |
| Monolith | Modular monolith → microservices |
| On-prem | Cloud |
| AngularJS | Angular |
| Legacy schema | Modern database design |

## 🚀 Install

Inside Claude Code:

```
/plugin marketplace add mahdibn54/claude-migration-planner
/plugin install migration-planner@mahdi-ben-amor
```

## 📖 Usage

**Slash command** — run it in any repository, naming only where you want to go:

```
/migration-planner:migration-plan Spring Boot 3
/migration-planner:migration-plan Java 21
/migration-planner:migration-plan Laravel 11
/migration-planner:migration-plan Angular 18
/migration-planner:migration-plan microservices
```

**Flags:**

| Flag | Effect |
|---|---|
| `--scope <path>` | Monorepos: analyze only that service/package (cross-boundary coupling still reported) |
| `--quick` | Fast feasibility read — detection, upgrade path, and top risks only. Answers *"how big is this?"* before you commit to a full plan |
| `--json-only` | Skip the Markdown report |

This writes two files at your repo root:

- `migration-plan.json` — machine-readable, feed it to CI, dashboards, or other agents
- `MIGRATION_PLAN.md` — human-readable report, executive summary first

**Or just ask** — the agent activates automatically on migration questions:

> *"We need to move this monolith to microservices, what's the plan?"*
>
> *"Assess upgrading this project from Java 8 to Java 21."*

**Run it headless / in CI** — regenerate the plan on a schedule or on dependency changes:

```bash
claude -p "/migration-planner:migration-plan Spring Boot 3 --json-only" --permission-mode acceptEdits
# migration-plan.json is now at the repo root — diff it, gate on it, dashboard it
```

## 🧾 Output shape

```json
{
  "executive_summary":         { "headline": "...", "total_estimated_duration_days": 38, "recommended_team_size": 2, "overall_confidence": "medium", "top_risks": [] },
  "system_summary":            { "architecture_type": "...", "languages": [], "frameworks": [], "deployment_model": "...", "complexity_assessment": "..." },
  "current_state":             { "detected_versions": [ { "component": "spring-boot", "version": "2.3.12", "evidence": "pom.xml", "detection_confidence": "high", "eol": true } ] },
  "target_state_assumptions":  { "target": "...", "assumptions": [] },
  "upgrade_path":              [ { "from": "2.3.12", "to": "2.7.18", "mandatory": true, "reason": "..." } ],
  "breaking_changes":          [ { "change": "...", "introduced_in": "...", "affected_files": [], "required_action": "...", "automation_available": "...", "source": "..." } ],
  "dependency_analysis":       [ { "module": "...", "dependencies": [], "evidence": [], "risk_level": "high", "migration_priority": 1 } ],
  "migration_plan":            [ { "phase": 1, "name": "...", "objective": "...", "steps": [], "estimated_duration_days": 0, "confidence": "high", "parallelizable_with": [], "exit_criteria": [], "risks": [], "rollback_strategy": [] } ],
  "critical_risks":            [ { "risk": "...", "impact": "...", "mitigation": "..." } ],
  "quick_wins":                [],
  "unknowns_and_assumptions":  [],
  "sources":                   []
}
```

See a full sample in [`examples/sample-output.json`](examples/sample-output.json).

## 🧠 Design principles

The agent is constrained by strict engineering rules, not vibes:

1. **Evidence only** — it may not invent services, files, or dependencies; findings cite the actual manifests they came from
2. **Official sources over memory** — breaking changes are researched from the official upgrade guides and changelogs for your exact version gap, with source URLs recorded in the output
3. **Enterprise-realistic** — assumes production can't be fully stopped, multiple teams, downtime risk
4. **Incremental over rewrite** — stabilize interfaces → isolate dependencies → compatibility layers → strangler pattern
5. **Lowest-risk first, database last** — shared state is treated as the highest-risk asset
6. **Honest about gaps** — anything not verifiable from the input lands in `unknowns_and_assumptions`, never silently assumed
7. **Verifiable phase gates** — every phase ends with observable exit criteria (tests green, staging soak, error rate at baseline); a phase without a verifiable exit is not a phase
8. **Honest estimates** — every duration carries a confidence level and a stated team-size assumption; low-confidence numbers are never presented as precise

## 🏢 Enterprise notes

- **Read-only by design** — the agent's toolset can inspect and search your repository but never modify it. The only writes are the two output files, written by the command after the analysis.
- **What leaves your machine** — the agent fetches *public* documentation (official upgrade guides, changelogs, EOL data). Your code is analyzed locally by Claude Code under your existing Anthropic data terms; the plugin adds no third-party services.
- **No web access?** The plan is still produced from repository evidence — breaking-change completeness is then explicitly flagged in `unknowns_and_assumptions` rather than silently degraded.
- **Repeatable** — `migration-plan.json` is stable, diffable output. Re-run it after each phase to track progress, or headless in CI to keep the plan current as the repo evolves.

## 📦 What's in the plugin

```
claude-migration-planner/
├── .claude-plugin/
│   ├── plugin.json          # plugin manifest
│   └── marketplace.json     # install via /plugin marketplace add
├── agents/
│   └── migration-planner.md # the subagent (read-only repo tools + web research for official upgrade guides)
├── commands/
│   └── migration-plan.md    # /migration-planner:migration-plan
└── examples/
    └── sample-output.json
```

The agent runs with **read-only repository access** — it analyzes and plans; it never modifies your code.

## 🤝 Contributing

Issues and PRs welcome — especially:

- New migration target playbooks (e.g. .NET Framework → .NET 8, Vue 2 → 3)
- Better coupling-detection heuristics
- Real-world sample outputs (anonymized)

## 📄 License

[MIT](LICENSE) © Mahdi Ben Amor

---

⭐ **If this saved you a planning meeting, star the repo** — it helps others find it.
