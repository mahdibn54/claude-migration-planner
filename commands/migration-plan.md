---
description: Analyze this repository and produce an execution-ready, phased migration plan (JSON + human-readable report)
argument-hint: "[target goal, e.g. 'Spring Boot 2 → 3' or 'monolith → microservices'] [--json-only]"
---

Run a full migration planning analysis on the current repository.

Target migration goal (may be empty — infer from evidence if so): $ARGUMENTS

Steps:

1. Launch the `migration-planner` agent on this repository. Pass it the target goal above verbatim. The agent explores the repo itself (manifests, infra definitions, coupling analysis) and returns a strict-JSON migration plan.
2. Validate the returned JSON parses. If it doesn't, ask the agent to correct it.
3. Save the raw JSON to `migration-plan.json` at the repository root.
4. Unless `--json-only` was passed, also render a human-readable `MIGRATION_PLAN.md` at the repository root from the JSON:
   - System summary and target state
   - Dependency analysis table (module, risk, priority, evidence)
   - Phased plan with steps, duration, risks, and rollback strategy per phase
   - Critical risks with mitigations
   - Quick wins
   - Unknowns and assumptions (flag these prominently — the reader must know what wasn't verifiable)
5. Reply to the user with a short summary: architecture type, target, number of phases, total estimated days, top 3 critical risks, and where the files were written. Do not dump the full JSON into the chat.
