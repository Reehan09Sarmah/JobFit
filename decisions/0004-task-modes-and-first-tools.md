# 0004: Task modes and the first two tools (resume_folder server)

- **Status:** accepted (file restored 2026-10-18 after a rewind deleted it; content unchanged)
- **Date:** 2026-10-18
- **Phase / component:** P1 Scope / P2a resume_folder server, tool surface slice 1

## The decision needed
Does the agent rank all resumes or score one, and which tools does the resume_folder server expose first?

## Context
Rex has 3 resumes in `resumes/` (`Resume_01.md`, `Resume_02.md`, `Resume_N.md`). Brief requires multi-step autonomous execution plus at least 3 tools, one resource, one prompt. Tools are model-controlled; resources are application-driven; prompts are user-controlled (spec 2026-07-28, read via summarising fetch tool). A server cannot see other servers, so a prompt that mentions both resumes and GitHub cannot live in either server (open, PROGRESS queue item 16).

## What was taught first
Lesson 2: tools vs resources vs prompts, who triggers each. Path safety rule: servers MUST sanitize file paths (Resources page, verified).

## Options considered
### Task mode
- Rank all: agent lists, reads each, scores, ranks. Genuinely multi-step.
- Score one: user names a resume; closer to a single prompt.
- Both: rank all by default, `/fit_report <resume> <job>` as the named shortcut.

### Tool surface
- A: `list_resumes()` and `read_resume(name)`.
- B: one `get_resumes()` returning everything. Output grows with every resume.
- C: `search_resumes(query)`. Over-built for 3 files.

## Claude's recommendation
Both task modes. Tool Option A, with `read_resume` taking only a name from the list (no paths); unknown names return `isError: true` listing the valid names. Recommendation only.

## Decision (Rex)
Both task modes. Tool Option A (two tools). Followed recommendation: yes.

## Why (in Rex's words)
- Both modes: "much better project and we'll learn both building a simple task as well as a multi step task design."
- Option A: "In order to skip the part or waste tokens where the LLM decides the next step of choosing a resume to read by going through each, we make the task easier for it to choose from. We return the names of resumes and then based on their names it decides which one to go with."

## Dissent, if any
Claude (note, not a dispute): names alone cannot tell the model which resume fits; for "rank all" it will still read every resume. The token saving applies to "score one" and to larger resume sets, and the main gain of A is small, traceable steps. Rex's decision stands.

## Consequences
Server exposes two read-only tools first. Resource and prompt come in the next slice.

## Revisit when
Resume count grows large (listing everything gets heavy), or tracing shows the model skipping `list_resumes`.

## 30-second defence (Rex's voice)
_To be written by Rex._
