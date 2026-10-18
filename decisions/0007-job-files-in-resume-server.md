# 0007: Job descriptions as .md files in the same server

- **Status:** accepted (partly supersedes 0006: the prompt's second argument changes from `job_text` to `job_name`)
- **Date:** 2026-10-18
- **Phase / component:** P2a resume_folder server / job descriptions

## The decision needed
How do job descriptions reach the agent: where do they live, and does the prompt take a job name or the job text?

## Context
Job text is copied from the web by the user, so it is untrusted. Resumes are the user's own files, so they are trusted. Servers cannot see each other (spec 2026-07-28 Architecture, design principle 3), so a prompt can only combine files that its own server can read. Brief: at least one source of untrusted text.

## What was taught first
How the delimiter defence works (a tag in the job text can close our box early; we remove such tags). Isolation: a prompt is built by one server only.

## Options considered
### A: Same server, `jobs/*.md`
New tools `list_jobs` and `read_job`; prompt becomes `score_resume(resume_name, job_name)`; job text is wrapped as untrusted wherever it appears. Easy: matches how the user wants to use it, one safety check for every file, least code. Hard: trusted and untrusted files live in one server. Fails if: the wrapping is skipped on a new path.

### B: Separate `job_folder` server
Same tools in their own server. Easy: clean separation, one more connection to learn. Hard: isolation means the prompt cannot read jobs, so it must keep taking `job_text`; more plumbing. Fails if: time runs out.

### C: Keep `job_text` argument
Host reads `jobs/*.md` and passes text in. Easy: no server change. Hard: the host repeats path-safety work; the model cannot list jobs itself.

## Claude's recommendation
A, with `read_job` also wrapping its output as untrusted. Recommendation only.

## Decision (Rex)
A. Followed recommendation: yes.

## Why
Rex, original: "to keep the job text in the same server rather than different servers. So that within the server we could wrap text into safe or unsafe category."

Polished by Claude, with a correction. Keeping both in one server lets the prompt read the resume and the job itself, which is the real reason (a prompt can only use files its own server can read), and it means one shared set of safety checks and no extra connection. Correction: labelling text as untrusted does not depend on being in the same server. A separate job server could also label its output. Also, "safe vs unsafe" is better put as "trusted vs untrusted": resumes are trusted because they are the user's own files, not because they are safe in any absolute sense, and wrapping job text marks it as data without making it harmless. The labels reduce risk; the human approval step (Phase 4) is the real safeguard.

## Dissent, if any
None. Note: because both file types sit in one server, a bug in the shared path-safety helper would affect both, so it is covered by tests for resumes and jobs.

## Consequences
Server now has four tools (`list_resumes`, `read_resume`, `list_jobs`, `read_job`), one resource template, one prompt. Job files are `.md` in `jobs/` (git-ignored). The tag-removal pattern now also catches tags with attributes. Size limit (20,000 characters) applies to every job read. How the agent hands job names to the prompt, and the combined resume-plus-GitHub prompt, remain open.

## Revisit when
Job text starts arriving from URLs automatically, or red-teaming shows the labels being escaped.

## 30-second defence (Rex's voice)
_To be drafted by Claude at the end of the phase._
