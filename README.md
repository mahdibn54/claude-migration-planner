# 🧭 Migration Planner — Claude Code Plugin

**Evidence-based enterprise migration plans, generated from your actual repository. No generic advice.**

[![Claude Code Plugin](https://img.shields.io/badge/Claude%20Code-Plugin-d97757)](https://docs.claude.com/en/docs/claude-code/plugins)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](../../pulls)

Most migration advice is generic: *"use the strangler pattern", "migrate incrementally"*. This plugin is different — it reads your **real** dependency manifests, infrastructure definitions, and module structure, then produces an **execution-ready, phased migration plan** with dependency ordering, risk levels, effort estimates, and rollback strategies. Every claim is backed by evidence found in your repo; everything unverifiable is explicitly flagged as `unknown`.

## ✨ What it does

Give it a repository and a target, get back a structured plan:

- 🏗️ **Architecture analysis** — languages, frameworks, deployment model, complexity
- 🕸️ **Dependency graph reasoning** — per-module risk levels and migration priority, with file-level evidence
- 🔗 **Hidden coupling detection** — shared databases, global state, implicit dependencies
- 📋 **Phased execution plan** — ordered steps, estimated duration, risks and rollback strategy *per phase*
- ⚠️ **Critical risk register** — impact and mitigation for each
- 🎯 **Quick wins** — low-effort improvements you can ship this week
- ❓ **Unknowns ledger** — what couldn't be verified from the code, stated honestly

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
/plugin marketplace add mahdibenamor/claude-migration-planner
/plugin install migration-planner@mahdi-ben-amor
```

## 📖 Usage

**Slash command** — run it in any repository:

```
/migration-planner:migration-plan Spring Boot 2 → Spring Boot 3
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
  "target_state_assumptions":  { "target": "...", "assumptions": [] },
  "dependency_analysis":       [ { "module": "...", "dependencies": [], "evidence": [], "risk_level": "high", "migration_priority": 1 } ],
  "migration_plan":            [ { "phase": 1, "name": "...", "objective": "...", "steps": [], "estimated_duration_days": 0, "risks": [], "rollback_strategy": [] } ],
  "critical_risks":            [ { "risk": "...", "impact": "...", "mitigation": "..." } ],
  "quick_wins":                [],
  "unknowns_and_assumptions":  []
}
```

See a full sample in [`examples/sample-output.json`](examples/sample-output.json).

## 🧠 Design principles

The agent is constrained by strict engineering rules, not vibes:

1. **Evidence only** — it may not invent services, files, or dependencies; findings cite the actual manifests they came from
2. **Enterprise-realistic** — assumes production can't be fully stopped, multiple teams, downtime risk
3. **Incremental over rewrite** — stabilize interfaces → isolate dependencies → compatibility layers → strangler pattern
4. **Lowest-risk first, database last** — shared state is treated as the highest-risk asset
5. **Honest about gaps** — anything not verifiable from the input lands in `unknowns_and_assumptions`, never silently assumed

## 📦 What's in the plugin

```
claude-migration-planner/
├── .claude-plugin/
│   ├── plugin.json          # plugin manifest
│   └── marketplace.json     # install via /plugin marketplace add
├── agents/
│   └── migration-planner.md # the subagent (read-only tools: Read, Grep, Glob, Bash)
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
