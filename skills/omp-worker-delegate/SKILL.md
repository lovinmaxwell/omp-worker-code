---
name: omp-worker-delegate
description: Delegate bounded coding tasks (scans, reviews, patches) to local Oh My Pi workers via omp-worker-mcp. Require absolute cwd; prefer compact run tools; keep architecture decisions in the parent; never print secrets from logs.
---

# OMP Worker delegate

Use this skill when Claude Code / Desktop / Cursor should offload **bounded** coding work to a local OMP worker through the connected `omp-worker` MCP.

## When to delegate

Good fits:

- Repo scans / grep-style exploration
- Code review of a known path or PR-sized change
- Bounded implementation tasks with a clear acceptance check

Keep in the **parent** agent:

- Architecture choices
- Final merge / ship decisions
- Anything needing the user’s secrets UI or irreversible external actions without approval

## How to run

1. Confirm MCP is connected (`omp-worker-setup`).
2. List tools; call **only** real tool names. Prefer compact run / delegate tools if present (examples only — may vary: `omp_run_compact`, `omp_delegate`, or run + wait pairs).
3. Pass an **absolute** `cwd` / working directory. Never rely on relative paths from an unknown shell.
4. Give a clear task prompt, scope limits, and what “done” means.
5. Wait or poll with the wait/status tools the server exposes; **cancel** with cancel tools if the user aborts or the job hangs.
6. Summarize worker output for the user; integrate results in the parent before claiming the task is finished.

## Guardrails

- **Never invent tool slugs** — if unsure, list tools again.
- **Never print secrets** from job logs (tokens, keys, `.env` contents). Redact if they appear.
- Do not start unbounded refactors or multi-hour jobs without user agreement.
- Prefer test/lint commands the repo already documents over inventing new pipelines.
