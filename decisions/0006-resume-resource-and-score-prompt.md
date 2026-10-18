# 0006: Resume resource and score_resume prompt (resume_folder server, slice 2)

- **Status:** accepted (prompt argument `job_text` replaced by `job_name` in 0007; the rest stands)
- **Date:** 2026-10-18
- **Phase / component:** P2a resume_folder server / resource and prompt

## The decision needed
What resource and what prompt does the resume_folder server expose, given that a server cannot see other servers?

## Context
Brief requires at least one resource and one prompt. Resources are application-driven; prompts are user-controlled (spec 2026-07-28, resources and prompts pages). A prompt cannot mention GitHub, because servers are isolated from each other, so this prompt covers the resume side only (the combined prompt is PROGRESS queue item 16). The job text is the project's first untrusted input.

## What was taught first
Lesson 2 (who triggers each primitive). SDK docs read via summarising fetch tool: `@mcp.resource("scheme://{param}")` makes a resource template; `@mcp.prompt()` turns parameters into named string arguments; a missing required prompt argument becomes JSON-RPC `-32602`. The SDK resources page does not say how to signal "resource not found"; the spec says `-32602` (verified, resources page).

## Options considered
### Resource
- A1: template `resume://{name}`, one resource per resume. Lets the host attach a resume the user already named. Duplicates `read_resume` on purpose (different caller).
- A2: one fixed `resumes://index` listing names. Teaches less; it is `list_resumes` again.

### Prompt
- B1: `score_resume(resume_name, job_text)`: returns one message with scoring instructions, the resume, and the job text inside `<job_description>` markers labelled as untrusted data. First untrusted input; markers reduce but do not remove injection risk.
- B2: `score_resume(resume_name)` only. Safer, less useful.

## Claude's recommendation
A1 and B1.

## Decision (Rex)
A1 and B1. Followed recommendation: yes.

## Why (in Rex's words, then polished)
Original, A1: "it would skip the part to let model decide to choose instead the host app would attach the required resume according to the user."
Polished (Claude, at Rex's standing request): The resource lets the host attach a resume the user has already named, so the model does not have to spend a tool-call round trip finding and reading it. Correction: the model is not skipped; it still receives the text. What is skipped is the model's decision to ask. The resource also does not replace `read_resume`: in "rank all" mode nobody has named a resume, so the model must still list and read them itself.

Original, B1: "It is more useful to score a resume against a selected job text. If we go down this route, we also get to solve untrusted input problem that adds to my learning - maybe we'll need to handle the job description text more carefully - either through a judge (llm or regex) or we'll see."
Polished (Claude): Scoring one resume against one chosen job is the "score one" mode from 0004, and passing the job text in makes the prompt useful on its own. It also gives us our first real untrusted-input case to defend, which is part of the learning goal.
Correction on the defence idea: a regex filter or an LLM judge should not be the main defence. A regex for "ignore previous instructions" is easily bypassed by rewording, and an LLM judge reads the attacker's text too, so it can be injected itself. Sturdier layers, in order: (1) label and delimit the job text as data and neutralise anything that could close the delimiter; (2) cap its size; (3) least privilege, so the step that reads job text cannot trigger anything consequential without human approval (Phase 4 approval gate); (4) only then optional detectors such as a judge, as a signal, not a wall.

## Dissent, if any
None. Note for later: the markers do not make the prompt injection-proof; the approval gate is the real backstop.

## Consequences
Server now has two tools, one resource template, one prompt. Allow-list helper is shared by all three. Job text input and size limit (20,000 characters, a minor call) are enforced in the prompt. Where the combined resume-plus-GitHub prompt lives, and how job files reach the host, are still open.

## Revisit when
Job text arrives from the web automatically (URL fetching), or red-teaming shows the markers are being escaped.

## 30-second defence (Rex's voice)
_To be written by Rex._
