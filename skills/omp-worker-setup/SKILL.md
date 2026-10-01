---
name: omp-worker-setup
description: Verify Node.js 22+, Oh My Pi (omp) CLI auth, and wire omp-worker-mcp into Claude Desktop, Claude Code, or Cursor on macOS. Use before first OMP delegation or when MCP fails to connect.
---

# OMP Worker setup

Use this skill before the first delegated OMP job, or when the `omp-worker` MCP is missing or unhealthy. **Target platform: macOS.**

## Prerequisites checklist

1. `node -v` → **≥ 22**.
2. `which omp` → path to the Oh My Pi CLI. Install/auth only via **[https://omp.sh](https://omp.sh)** — do **not** invent install commands beyond pointing the user there.
3. Confirm the CLI is authenticated (use whatever status/login the installed `omp` documents — do not invent flags).
4. `npx` available to the **GUI app** PATH (see PATH tip below).

## Configure MCP

Prefer `npx -y omp-worker-mcp` (npm package). Upstream repos for reference (prefer npx over cloning): [divenire990/omp-worker-mcp](https://github.com/divenire990/omp-worker-mcp), [elijah7x/omp-worker-mcp](https://github.com/elijah7x/omp-worker-mcp).

### Claude Desktop

File: `~/Library/Application Support/Claude/claude_desktop_config.json`

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

Quit Claude Desktop fully and reopen.

### Claude Code

```bash
claude mcp add omp-worker -- npx -y omp-worker-mcp
```

Ensure `OMP_WORKER_OMP_COMMAND` points at `omp` (absolute path if needed). Verify with `claude mcp --help` if flags differ.

### Cursor

Merge the same server block into `~/.cursor/mcp.json`, then reload MCP / window.

## macOS PATH tip

If the client cannot find `npx` or `omp`, set `command` and `OMP_WORKER_OMP_COMMAND` to **absolute** Homebrew paths from a login shell (`which npx`, `which omp`).

## Verify

1. Client shows `omp-worker` connected.
2. List tools — use **only** exposed names (illustrations only: `omp_run_compact`, `omp_delegate`, run/wait/cancel — **may differ by version**).
3. Never invent tool slugs or fake omp install one-liners.

## Next

Delegate work with the `omp-worker-delegate` skill.
