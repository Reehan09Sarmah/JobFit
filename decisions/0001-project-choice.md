# 0001: Project choice and scope

- **Status:** accepted (Rex OK'd the file, 2026-10-18)
- **Date:** 2026-10-18
- **Phase / component:** P1 Scope / project choice

## The decision needed
What to build, and how wide to make it.

## Context
Brief requires: at least 3 tools, one resource, one prompt, multi-step autonomous execution, explicit state, stop conditions, failure recovery, logs, one human approval point, hand-written loop, 4 to 5 days. Rex wants a personal tool he will actually use. Rex's original idea (GitHub + LeetCode progress tracker) was changed during the discussion.

## What was taught first
Lesson 1: why MCP exists, host / client instance / server roles, one client instance per server, stdio framing, `tools/call` on the wire, protocol error vs `isError`, statelessness. Read spec 2026-07-28 (overview, changelog, architecture, basic, stdio, tools) via a summarising fetch tool, not raw. Checked via web search (summaries only, not primary pages): LeetCode has no official public API and its terms forbid scraping (verify at https://leetcode.com/terms); Google documents an official remote Drive MCP server at drivemcp.googleapis.com (connect path for a custom client NOT yet read); the old reference `server-gdrive` is archived.

## Options considered
### Option 1: Personal, local, command-line only
- What it is: Rex's own accounts and data, no web UI.
- Makes easy: fits 4 to 5 days, focus on protocol, loop and reliability.
- Makes hard later: not shareable.
- How it fails: a weak data source (e.g. LeetCode) undermines the demo.

### Option 2: Personal plus a web page
- What it is: Option 1 with a browser front end.
- Makes easy: nicer demo.
- Makes hard later: frontend is out of scope in the brief and competes with loop/reliability time.
- How it fails: time runs out before approval gate and tracing.

### Option 3: General multi-user product with login
- What it is: anyone signs in and connects their own accounts.
- Makes easy: resume value.
- Makes hard later: multi-user, deployment and OAuth are out of scope; weeks of work.
- How it fails: infrastructure is half done and no defensible loop exists.

## Claude's recommendation
Option 1 (personal, local, no web UI), with the LeetCode side taken from manual or file input to avoid depending on a scraper that LeetCode's terms forbid. Recommendation only.

## Decision (Rex)
Personal only (Option 3 of Claude's decision brief with the multi-user system removed; NOT candidate C in PROJECT_BRIEF.md). LeetCode dropped. Project is a resume-fit agent: reads Rex's resumes from Google Drive plus a job description, with GitHub as evidence of Rex's work. Two MCP servers (so the host runs two client instances). Followed recommendation: **modified** (personal scope as recommended; wider content than recommended: two servers, Drive added).

Refined by 0002: resumes come from a local folder now; Google's Drive server only in Phase 5.

Out of this decision, still open at the time: build vs use Google's Drive server, language and SDK, tool list, which action is consequential, source of untrusted text.

## Why (in Rex's words)
"Because it will be a great project to learn from and use."

## Dissent, if any
Claude: this is the widest option available within personal scope. (1) Google Drive OAuth/connection may take a large share of the 4 to 5 days; connect path unread. (2) Two servers double the plumbing (namespacing, errors, tracing) before the interesting work starts. (3) Resumes are personal data and will go to whichever LLM provider is chosen, so the provider choice is also a privacy decision. Rex confirmed the scope. A smaller first slice (one server end to end, then the second) remains compatible with this decision.

## Consequences
Commits us to two servers and one hand-written host with two client instances (tool-name namespacing becomes our job). Drops LeetCode and any UI or multi-user work. Makes the LLM provider choice privacy-relevant.

## Revisit when
Drive connection takes more than about one day, or by the end of P2 if the second server is not working.

## 30-second defence (Rex's voice)
_To be written by Rex._
