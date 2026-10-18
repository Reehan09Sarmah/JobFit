# Project brief

**Status:** chosen in Decision 0001: a personal resume-fit agent (resumes from a local folder, GitHub as evidence; Google's Drive server is a Phase 5 stretch per 0002). See `decisions/README.md`.

## Goal
Build an MCP-powered agent in about 4 to 5 days: an MCP server for a real system, plus a client and an agent that uses the server's capabilities for multi-step tasks. Rex has never built an MCP integration, so understanding the protocol itself is a primary outcome, not an implementation detail.

## Learning outcomes (what Rex must be able to explain and defend at the end)
- Why MCP exists, and how it differs from plain tool/function calling.
- Host, client and server roles. Tools vs resources vs prompts: who controls each and when to use which.
- The lifecycle in spec 2026-07-28: no `initialize` handshake (removed in this revision); every request carries its protocol version and client capabilities in `_meta`; optional `server/discover`; requests, notifications, shutdown (stdio: close stdin). Older revisions used `initialize`. What the JSON-RPC messages look like.
- Transports (stdio and Streamable HTTP), what changes between them, and how authorization works for remote servers.
- Tool design: names, descriptions, schemas, errors, result size. Why tool descriptions are part of the prompt.
- The agent loop written by hand: decide, act, observe, stop. State, budgets, stop conditions, recovery, idempotency.
- Trust boundaries: tool output and server content are untrusted input. Read-only vs consequential tools. Human approval.

## Candidate projects (Rex picks, or proposes his own)
- **A. Job-pipeline MCP server.** A SQLite-backed tracker of applications (company, role, stage, deadlines, prep notes), plus a tool that fetches a job-description page (untrusted web content). Agent task example: "prepare me for tomorrow's interview at X". Consequential actions: changing stage, drafting follow-ups.
- **B. Codebase MCP server.** Exposes a local git repo: search, read files, git log, run tests in a sandbox. Agent task example: "find where X is handled and propose a change". Consequential actions: applying a patch, running commands.
- **C. Experiment and eval ledger MCP server.** Stores prompt versions, test cases and run results; the agent can run a small eval set and compare with a baseline. Feeds directly into later LLM-evals study.

Whatever is chosen should include some read-only tools, at least one consequential tool, and at least one source of untrusted text. These are needed later to practise agent evals and red-teaming on this same project.

## Fixed requirements (acceptance criteria)
- At least **3 useful tools**, plus at least one **resource** and one **prompt** so all three primitives are exercised.
- **Multi-step autonomous execution**: the agent chooses its own sequence of tool calls.
- **Explicit state** the agent carries between steps (not just the chat history).
- **Stopping conditions**: max steps, repeat detection, budget/time limit, explicit "done".
- **Failure recovery**: tool errors are handled deliberately (retry, alternative, replan, or escalate).
- **Logs and visible progress**: every step, tool call and result is traceable.
- **At least one human approval point** for a consequential action.
- **The agent loop is hand-written first** and explained from first principles. A framework port (for example LangGraph) is optional and only after Rex decides.
- A short README with an architecture diagram and the decision index.

## Suggested phases (Rex confirms or changes the plan; each phase starts with teaching)
- **P0 Orientation:** MCP from first principles. Read the spec overview, trace raw JSON-RPC by hand over stdio, use the MCP Inspector against a trivial server.
- **P1 Scope:** choose the project, tool surface sketch, threat sketch. Rex may run `/grill-me` here if it is installed.
- **P2 Server:** data layer, tools, resource, prompt, error model.
- **P3 Client and loop:** connect a client, write the agent loop by hand.
- **P4 Reliability:** state, budgets, stop conditions, recovery, idempotency, approval gate, logging.
- **P5 Transport and trust:** Streamable HTTP, auth basics, second server or untrusted server scenario.
- **P6 Wrap-up:** README, diagram, defend-it questions, notes on how to evaluate and red-team this agent later.

## Out of scope unless Rex decides otherwise
Frontend UI, multi-user support, deployment/infra, fine-tuning, multi-agent orchestration.

## Environment facts to confirm with Rex in Phase 1 (do not assume)
Language and SDK: decided in 0003 (Python, official `mcp` 2.x). OS: Windows 11, runs locally. Still open: LLM provider and model.
