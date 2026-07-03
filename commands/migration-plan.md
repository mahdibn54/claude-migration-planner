---
description: Inspect this repository and produce a detailed, phased plan to migrate it from its current (auto-detected) versions to the target you specify
argument-hint: "<target, e.g. 'Java 21', 'Laravel 11', 'Spring Boot 3', 'Angular 18', 'microservices'> [--json-only]"
---

Run a full migration planning analysis on the current repository.

Target specified by the user (only the destination — current versions are auto-detected from the repo; if empty, infer the most valuable modernization target from evidence): $ARGUMENTS

Steps:

1. Launch the `migration-planner` agent on this repository. Pass it the target above verbatim. The agent detects current framework/language versions from manifests, researches the official upgrade guides for the exact version gap, maps breaking changes to actual files, and returns a strict-JSON migration plan.
2. Validate the returned JSON parses. If it doesn't, ask the agent to correct it.
3. Save the raw JSON to `migration-plan.json` at the repository root.
4. Unless `--json-only` was passed, also render a human-readable `MIGRATION_PLAN.md` at the repository root from the JSON:
   - System summary: detected current versions (with evidence) and target state
   - Upgrade path (current → intermediate hops → target) and why each hop is mandatory
   - Breaking changes table: change, affected files in this repo, required action, available automation (codemods/recipes), source link
   - Dependency analysis table (module, risk, priority, evidence)
   - Phased plan with steps, duration, risks, and rollback strategy per phase
   - Critical risks with mitigations
   - Quick wins
   - Unknowns and assumptions (flag these prominently — the reader must know what wasn't verifiable)
5. Reply to the user with a short summary: architecture type, target, number of phases, total estimated days, top 3 critical risks, and where the files were written. Do not dump the full JSON into the chat.
