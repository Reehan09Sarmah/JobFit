# 0002: Where the Drive / resume side comes from

- **Status:** accepted
- **Date:** 2026-10-18
- **Phase / component:** P1 Scope / server sourcing

## The decision needed
Do we build the resume-reading server ourselves, or connect to Google's Drive MCP server, and when?

## Context
Rex first picked "use Google's server, build GitHub" to avoid OAuth plumbing. Reading Google's setup guide showed that premise was wrong: connecting needs a Cloud project, consent screen, OAuth web client, Workspace Developer Preview Program membership, and a client that speaks Streamable HTTP plus OAuth. The page lists Antigravity and Claude as supported apps and does not say a custom client is supported. It documents 8 tools and no resources or prompts. Brief requires one resource and one prompt, and Phase 5 covers Streamable HTTP and auth basics.

## What was taught first
Host vs client instance vs server; why Google's server needs OAuth in the client. Sources read: https://developers.google.com/workspace/drive/api/guides/configure-mcp-server (via summarising fetch tool). The old reference `server-gdrive` is archived (search summary only, not relied on).

## Options considered
### Option A: Own server over a local resume folder, never Google's
- What it is: our server reads files from a local folder (resumes synced by Drive for desktop or copied).
- Makes easy: no OAuth, full control of tools/resource/prompt.
- Makes hard later: never exercises a real remote third-party server.
- How it fails: resumes are Google Docs, not downloadable files.

### Option B: Google's Drive server from the start
- What it is: our client does OAuth + Streamable HTTP now.
- Makes easy: real remote server.
- Makes hard later: heavy setup first; Phase 5 work pulled forward; eligibility unknown.
- How it fails: Developer Preview access or custom-client support blocks us.

### Option C: Local folder now, Google's server in Phase 5
- What it is: A first, then connect Google's server as the remote-server and auth exercise.
- Makes easy: project moves now; Phase 5 gets a real use case.
- Makes hard later: two steps; a second server to wire in.
- How it fails: Google's server is still inaccessible at Phase 5.

## Claude's recommendation
Option C (revised after reading the setup guide; the earlier recommendation of B was withdrawn). Recommendation only.

## Decision (Rex)
Option C. Followed recommendation: yes.

## Why (in Rex's words)
"I want to get on with building the project first and then add on features in the future. I learn first and excel later."

## Dissent, if any
None.

## Consequences
Phase 2 builds two of our own servers (local resume folder, GitHub). Google's Drive server enters in Phase 5 as a consumed, untrusted-by-default remote server; this adds a third client instance then.

## Revisit when
Phase 5 starts, or if Google's server is not reachable for a personal account or custom client (verify before Phase 5, not yet checked).

## 30-second defence (Rex's voice)
_To be written by Rex._
