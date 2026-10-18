# 0005: Transport for v1 and repo layout

- **Status:** accepted (why drafted by Claude at Rex's request, awaiting his OK)
- **Date:** 2026-10-18
- **Phase / component:** P2 Server / transport and layout

## The decision needed
How our own servers talk to the host in v1, and how the repo is laid out.

## Context
Two own servers (resume_folder, GitHub) and one hand-written host. Phase 5 covers Streamable HTTP and auth. `mcp` 2.x runs a server over stdio by default (`mcp.run()`), and `Client(StdioServerParameters(...))` launches a server as a subprocess (SDK docs, read via summarising fetch tool).

## What was taught first
Lesson 1: stdio framing (one JSON message per line, logs to stderr, shutdown by closing stdin). Host / client instance / server roles.

## Options considered
### Transport
- stdio: subprocess, newline-delimited JSON. Easy to run and trace; SDK default. Fails: only works on the same machine.
- Streamable HTTP now: local web servers. Closer to remote use; adds ports, HTTP and later auth on day one. Fails: setup eats the time budget.

### Layout
- Per-server folders: `servers/resume_folder/server.py`, `servers/github/server.py`, `host/`, `tests/`. Mirrors the roles. Fails: a little more nesting.
- Flat: `resume_server.py`, `github_server.py`, `host.py`. Fewer folders. Fails: roles blur as files grow.

## Claude's recommendation
stdio for v1 (Streamable HTTP in Phase 5); per-server folders. Recommendation only.

## Decision (Rex)
stdio; per-server folders. Followed recommendation: yes.

## Why (in Rex's words)
_Drafted by Claude at Rex's request ("you explain in simple terms and write the words for 0005 yourself"); Rex to confirm or edit._
- stdio: "Our servers run on my own laptop, so the host can just start them as child programs and talk through stdin/stdout. No ports, no web server, no login to set up, and I can see every message. HTTP and auth are a Phase 5 lesson, not a day-one cost."
- Per-server folders: "Each folder matches one role from the MCP picture: one folder per server, one for the host. When something breaks I know which box to open."

## Dissent, if any
None.

## Consequences
Each server is one runnable Python file. The host launches servers as subprocesses, so it controls their lifetime and their environment (a stdio child gets only a minimal environment, so anything it needs must be passed explicitly).

## Revisit when
Phase 5, or if a server must run on another machine.

## 30-second defence (Rex's voice)
_To be written by Rex._
