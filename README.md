# 🧭 Migration Planner — Claude Code Plugin

**Evidence-based enterprise migration plans, generated from your actual repository. No generic advice.**

[![Claude Code Plugin](https://img.shields.io/badge/Claude%20Code-Plugin-d97757)](https://docs.claude.com/en/docs/claude-code/plugins)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](../../pulls)

Most migration advice is generic: *"use the strangler pattern", "migrate incrementally"*. This plugin is different — it reads your **real** dependency manifests, infrastructure definitions, and module structure, then produces an **execution-ready, phased migration plan** with dependency ordering, risk levels, effort estimates, and rollback strategies. Every claim is backed by evidence found in your repo; everything unverifiable is explicitly flagged as `unknown`.

## ✨ What it does

**You name only the target.** The agent inspects the repository, detects what you're currently running, and produces the detailed path between the two:

- 🔍 **Current-state detection** — exact framework and language versions, proven from your manifests (`pom.xml`, `package.json`, `composer.json`, `requirements.txt`, `go.mod`, `*.csproj`, …) — it never asks what you're on
- 🪜 **Upgrade path computation** — including mandatory intermediate hops (Spring Boot 2.3 → 2.7 → 3.x, Django 2 → 3.2 LTS → 4.2, AngularJS → hybrid ngUpgrade → Angular)
- 💥 **Breaking-changes mapping** — researched from the *official* upgrade guides for your exact version gap, then mapped to the **actual files in your repo** that each change affects, with available automation noted (OpenRewrite, Rector, codemods, `ng update`, .NET Upgrade Assistant)
- 🏗️ **Architecture analysis** — languages, frameworks, deployment model, complexity
- 🕸️ **Dependency graph reasoning** — per-module risk levels and migration priority, with file-level evidence
- 🔗 **Hidden coupling detection** — shared databases, global state, implicit dependencies
- 📋 **Phased execution plan** — ordered steps, estimated duration, risks and rollback strategy *per phase*
- ⚠️ **Critical risk register** — impact and mitigation for each
- 🎯 **Quick wins** — low-effort improvements you can ship this week
- ❓ **Unknowns ledger** — what couldn't be verified from the code, stated honestly

Works on **any stack the manifests reveal**: Java/Spring, Node/JS frameworks, Python/Django, PHP/Laravel/Symfony, .NET, Go, Ruby/Rails, databases, and infrastructure.

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

This writes two files at your repo root:

- `migration-plan.json` — machine-readable, feed it to CI, dashboards, or other agents
- `MIGRATION_PLAN.md` — human-readable report for your team

**Or just ask** — the agent activates automatically on migration questions:

> *"We need to move this monolith to microservices, what's the plan?"*
>
> *"Assess upgrading this project from Java 8 to Java 21."*

## 🧾 Output shape

```json
{
  "system_summary":            { "architecture_type": "...", "languages": [], "frameworks": [], "deployment_model": "...", "complexity_assessment": "..." },
  "current_state":             { "detected_versions": [ { "component": "spring-boot", "version": "2.3.12", "evidence": "pom.xml" } ] },
  "target_state_assumptions":  { "target": "...", "assumptions": [] },
  "upgrade_path":              [ { "from": "2.3.12", "to": "2.7.18", "mandatory": true, "reason": "..." } ],
  "breaking_changes":          [ { "change": "...", "introduced_in": "...", "affected_files": [], "required_action": "...", "automation_available": "...", "source": "..." } ],
  "dependency_analysis":       [ { "module": "...", "dependencies": [], "evidence": [], "risk_level": "high", "migration_priority": 1 } ],
  "migration_plan":            [ { "phase": 1, "name": "...", "objective": "...", "steps": [], "estimated_duration_days": 0, "risks": [], "rollback_strategy": [] } ],
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
