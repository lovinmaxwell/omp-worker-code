# OMP Worker

A Cursor / Claude plugin that wires **[omp-worker-mcp](https://www.npmjs.com/package/omp-worker-mcp)** so **Claude Desktop**, **Claude Code**, or **Cursor** on **macOS** can delegate coding tasks to local **[Oh My Pi (OMP)](https://omp.sh)** workers.

Author: Lovin Maxwell (`lovinmaxwell`). This plugin packages MCP config + skills; it does not vendor the worker runtime.

## Prerequisites (macOS)

1. **Node.js 22+** (`node -v`).
2. **Oh My Pi (`omp`)** installed and authenticated from [https://omp.sh](https://omp.sh) — do not invent alternate install commands; follow that site.
3. Verify in a terminal:

   ```bash
   node -v          # expect v22+
   which omp
   omp              # or whatever status/auth command the installed CLI documents
   ```

## MCP config (npx package — preferred)

Prefer the published npm package `omp-worker-mcp` via `npx`. Related GitHub repos (document both; install via npm/npx, not by cloning unless the user asks):

- [divenire990/omp-worker-mcp](https://github.com/divenire990/omp-worker-mcp)
- [elijah7x/omp-worker-mcp](https://github.com/elijah7x/omp-worker-mcp)

Plugin `.mcp.json` / merge target for clients:

```json
{
  "mcpServers": {
    "omp-worker": {
      "type": "stdio",
      "command": "npx",
      "args": ["-y", "omp-worker-mcp"],
      "env": {
        "OMP_WORKER_OMP_COMMAND": "omp"
      }
    }
  }
}
```

### Claude Desktop (macOS)

Edit:

```text
~/Library/Application Support/Claude/claude_desktop_config.json
```

Add the `omp-worker` server block above under `mcpServers`, then fully quit and reopen Claude Desktop.

### Claude Code

```bash
claude mcp add omp-worker -- npx -y omp-worker-mcp
```

Set `OMP_WORKER_OMP_COMMAND=omp` in the environment if your CLI supports env for MCP entries (or configure via the client’s MCP env UI). Check `claude mcp --help` for exact flags.

### Cursor

Merge into `~/.cursor/mcp.json` (same JSON as above), then reload Cursor / restart MCP.

## macOS PATH tip

GUI apps (Claude Desktop, Cursor) often **do not** inherit your Homebrew shell PATH. If `npx` or `omp` is “not found”:

- Point `command` at the absolute path to `npx` (e.g. `/opt/homebrew/bin/npx` or `$(which npx)` from a login shell).
- Set `OMP_WORKER_OMP_COMMAND` to the absolute path of `omp` (e.g. `/opt/homebrew/bin/omp`).

## Tools

**Never invent tool slugs.** After connect, list tools from `omp-worker` and use only those names. Depending on package version you may see examples such as `omp_run_compact`, `omp_delegate`, or run/wait/cancel style tools — treat these as **illustrations only**.

## Install for local testing (Cursor)

1. Copy this directory to:

   ```text
   ~/.cursor/plugins/local/omp-worker-code
   ```

2. Reload Cursor (**Developer: Reload Window**).
3. Confirm skills appear; wire MCP as above.

**Grok Bot** only installs **marketplace** plugins. Local plugin folders are for Cursor local testing; publish to the marketplace for Grok Bot.

## Skills

| Skill | When to use |
| --- | --- |
| `omp-worker-setup` | Verify Node/omp, configure clients, confirm MCP |
| `omp-worker-delegate` | Delegate scans/reviews/bounded coding to OMP workers |

## Guardrails

- Require an **absolute** working directory for worker jobs.
- Keep architecture and final decisions in the parent agent.
- Never print secrets from job logs.
- Cancel long jobs with the cancel tools the server exposes (if any).

## Publishing

Cursor marketplace: <https://cursor.com/marketplace/publish>.
