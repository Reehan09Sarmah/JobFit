# 0003: Language and MCP SDK

- **Status:** accepted
- **Date:** 2026-10-18
- **Phase / component:** P1 Scope / language and SDK

## The decision needed
Which language, and how do we speak MCP in it.

## Context
Rex knows Python best and can debug in it. Spec target under consideration is 2026-07-28 (stateless, no `initialize`). Python 3.14.7 is installed.

## What was taught first
What an SDK does: implements the rulebook (parts 2 and 3 of the pseudo-code) so only tool bodies are written. Read: PyPI metadata for `mcp` (latest 2.3.0, requires Python >=3.10; the SDK README says 3.11+, an inconsistency that does not matter on our 3.14.7) and GitHub release notes (2.0.0 on 28 Jul "supports the 2026-07-28 revision ... and serves every earlier revision"; 2.3.0 on 2 Oct; 1.x is maintenance; tasks extension not implemented yet). README examples use `MCPServer` and `Client`. Via summarising fetch tool, not raw. Not yet read: the 2.x client API for stdio in detail.

## Options considered
### Language: Python (chosen by Rex; alternatives not formally compared)

### SDK Option 1: official `mcp` 2.x
- Makes easy: matches current spec, little plumbing.
- Makes hard later: hides part of the wire; young release line.
- How it fails: a breaking change or bug in 2.x.

### SDK Option 2: official `mcp` 1.x
- Makes easy: more existing tutorials.
- Makes hard later: behind current spec, maintenance line, teaches the old handshake model.
- How it fails: version drift from the spec we are learning.

### SDK Option 3: raw protocol by hand
- Makes easy: full visibility.
- Makes hard later: much more code (errors, later HTTP/auth).
- How it fails: time runs out before the agent loop.

## Claude's recommendation
SDK Option 1. Recommendation only.

## Decision (Rex)
Python; official `mcp` 2.x. Followed recommendation: yes.

## Why (in Rex's words)
- Language: "I am most familiar with it - I understand and I can debug some regions myself."
- SDK: "It was purely based on Claude's recommendation and part of me said: it's the new version, why should I use the old, as I will use this project to help me."

## Dissent, if any
None.

## Consequences
Target spec is effectively 2026-07-28 (the SDK serves it and earlier revisions). Python virtual environment with `mcp` as the only approved dependency; every further dependency (GitHub client, LLM provider SDK, etc.) is its own decision. The agent loop is still hand-written.

## Revisit when
The 2.x client cannot do something we need, or a breaking release lands mid-project.

## 30-second defence (Rex's voice)
_To be written by Rex._
