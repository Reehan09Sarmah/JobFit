# Progress

_Updated by Claude Code at the end of every session. Rex can edit freely._

**Current phase:** P2a (resume_folder server, slice 1). P0 Inspector and several P1 decisions are still open (see queue).
**Last session:** 2026-10-18, session 1 (Lessons 1 and 2 passed; decisions 0001 to 0004; venv; `.gitignore`).
**Today's approved target (3 hours):** slice 1 resume_folder tools; slice 2 resource + prompt; slice 3 small client that connects, lists and calls tools (no LLM yet); MCP Inspector if time is left.
**Next session starts with:** whatever of today's target is unfinished, then the open P1 decisions below.

## Phases and components
- [ ] **P0 Orientation**
  - [x] MCP first principles: host / client instance / server; tools (model-controlled) vs resources (application-driven) vs prompts (user-controlled). Lessons 1 and 2 passed 2026-10-18.
  - [x] Raw JSON-RPC trace over stdio (tools/list, tools/call; `initialize` does not exist in spec 2026-07-28). Throwaway scripts, not in the repo.
  - [ ] MCP Inspector against a trivial server (route not decided, see queue item 18)
- [ ] **P1 Scope**
  - [x] Project chosen (Decision 0001, refined by 0002)
  - [ ] Tool surface sketch and threat sketch (sketch proposed in chat, NOT decided; only the first two tools are decided, in 0004)
- [ ] **P2 Server**
  - [x] Data layer (local `.md` files in `resumes/`)
  - [x] Read-only tool(s): `list_resumes`, `read_resume` (slice 1, Rex ran it and did the break-it check, 2026-10-18)
  - [ ] Consequential tool(s)
  - [ ] Resource
  - [ ] Prompt
  - [ ] Error model
- [ ] **P3 Client and loop**
  - [ ] Client connects and lists capabilities
  - [ ] Hand-written agent loop
- [ ] **P4 Reliability**
  - [ ] Explicit state
  - [ ] Budgets and stop conditions
  - [ ] Failure recovery and idempotency
  - [ ] Human approval gate
  - [ ] Logging / trace
- [ ] **P5 Transport and trust** (proposed as a stretch goal; not decided)
  - [ ] Streamable HTTP
  - [ ] Auth basics
  - [ ] Untrusted server or content scenario (Google's Drive server, per 0002)
- [ ] **P6 Wrap-up**
  - [ ] README and architecture diagram
  - [ ] Defend-it questions answered
  - [ ] Notes for evals and red-teaming later

## Decision queue (Claude proposes, Rex decides)
Done: 1 project (0001), 2 language + SDK (0003), Drive sourcing (0002), task modes + first two tools (0004), 3 transport stdio + 4 layout (0005, why pending), 20 pinning (`requirements.txt`, Rex approved).
Open:
5. Rest of the tool surface: `list_jobs` / `read_job`, GitHub tools. (Resume resource and `score_resume` prompt done: 0006.)
6. LLM provider and model. Rex mentioned Grok's free API and also has Gemini (Pro via Jio). Unverified: whether a consumer Pro plan gives API access; Grok free-credit terms conflict across sources.
7. Agent loop design.
8. State representation and stop conditions.
9. Approval gate design.
10. Error model (tool errors vs protocol errors) beyond what 0004 implies.
11. Trace/log format.
12. Second transport and auth approach (P5).
13. Whether to port to a framework.
14. Job descriptions input (proposed: text files in `jobs/`, URL fetch parked).
15. Resumes and jobs in one server or two (untrusted job text next to trusted resumes).
16. Where the combined `fit_report` prompt lives: a server cannot see other servers, so a prompt mentioning both resumes and GitHub cannot live in either server.
17. Consequential action (proposed: `save_report`, approval required).
18. GitHub access method. Proposed earlier: `httpx2` (came in with `mcp`). Corrected recommendation: stdlib `urllib`, because relying on a dependency we did not choose is fragile. Read-only fine-grained token.
19. MCP Inspector route: `mcp dev` needs the `mcp[cli]` extra (new deps) and the docs show `uv`; alternative is the Node Inspector via `npx` (unverified for spec 2026-07-28).

## Decisions made
_See `decisions/README.md` for the index._

## In Rex's words
_One short entry per finished component: what it does and why we designed it this way._
- **resume_folder server, slice 1 (2026-10-18).** _Rex's words, polished by Claude at his request._ The real system here is just my `resumes/` folder; the MCP server is a thin front desk standing in front of it. It offers the client two tools: `list_resumes` gives back the names of every resume in the folder, and `read_resume` gives back the full text of the one the model picks. The model can only ask for a name from that list, never a path, so it can't wander outside the folder; a wrong name comes back as a fixable tool error that lists the valid names. No model is connected yet; this is the job it will do once the host exists.

## Open questions / parked
- Target spec revision: settled in practice by 0003 (SDK 2.x speaks 2026-07-28 and still serves older revisions). Rex can ask for an explicit decision.
- Before Phase 5: verify whether Google's Drive MCP server accepts a custom client on a personal account (setup guide mentions Workspace Developer Preview membership), and which spec revision it speaks.
- Local folder is `mcp-kit`, GitHub repo is `JobFit`. Harmless; the remote decides the repo name.
- (Resolved 2026-10-18) `.gitignore` personal-data entries are now anchored with a leading `/` so they only match the repo root.

## Minor calls log
_One line each: what Claude decided on its own. Rex can veto._
- `.gitignore` (Rex asked for all unnecessary files): venvs, Python caches, build output, `.env`/`.env.*` (but `.env.example` is allowed), root-anchored `/resumes/ /jobs/ /reports/ /data/ /logs/`, `*.log`, `.vscode/`, `.idea/`, `.claude/settings.local.json`, OS junk. Tested in a throwaway repo: real files tracked, personal data ignored, test fixtures not ignored.
- venv at `.venv` (stdlib `venv` + `pip`, no `uv`); installed only `mcp` 2.3.0 plus its own dependencies; Python 3.14.7; no `[cli]` extra.
- Slice 2 built (resource `resume://{name}`, prompt `score_resume`; decision 0006); self-check passes; NOT ticked until Rex runs it and writes his lines. Minor calls: shared `_read()` helper so all three primitives use the same allow-list (this changed `read_resume` internally, behaviour unchanged); job text capped at 20,000 characters; the closing/opening `<job_description>` tags inside job text are replaced with `[tag removed]`; resource/prompt errors are `MCPError(-32602)` because no model is in the loop for them.
- Slice 1 built, run by Rex, ticked. Files: `servers/resume_folder/server.py`, `tests/test_resume_server.py` (plain asserts, no pytest), `tests/fixtures/fake_resumes/`. Minor calls: `RESUMES_DIR` env var lets tests use fake resumes; fixture folder named `fake_resumes` so the unanchored `.gitignore` rule `resumes/` does not hide it; only `*.md` files are listed; `requirements.txt` pins `mcp==2.3.0`.
- Throwaway wire-trace scripts (`toy_server.py`, `toy_client.py`) live in the session scratchpad, not the project; stdlib only. Not the language decision (that is 0003).

## Session log
_Date, what was covered, what Rex should be able to explain now, one thing to revisit._
- **2026-10-18 (session 1).** Read spec 2026-07-28 (overview, changelog, architecture, basic, stdio, tools, resources, prompts; via a summarising fetch tool). Lesson 1 (why MCP, roles, stdio framing, `tools/call`, `error` vs `isError`, statelessness) passed. Lesson 2 (tools / resources / prompts and who triggers each) passed. Read `mcp` 2.x docs: tools, handling errors (`ToolError` vs `MCPError`), running over stdio, client transports. Decisions 0001 to 0004. venv + `mcp` 2.3.0. Rex can now explain: what MCP standardises, one client instance per server, why stdout must stay clean, `isError` vs JSON-RPC `error`, and who controls tools, resources and prompts. Revisit: statelessness (handles instead of sessions).
