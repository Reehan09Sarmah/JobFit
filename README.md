# JobFit

A personal learning project: an AI agent that helps me decide **which of my resumes fits a job best**, built on top of [MCP (Model Context Protocol)](https://modelcontextprotocol.io).

> **Status: work in progress.** This README only describes what exists today. The agent itself is not built yet.

## The idea in plain words

I keep several resumes. When I find a job, I want an agent that can:
1. look at my resumes,
2. read the job description,
3. check my GitHub projects as evidence,
4. tell me which resume fits best and what's missing.

MCP is the standard "plug" that lets an AI app talk to small helper programs (called *servers*) that give it tools and data. Each helper does one job. I'm building the helpers and the agent by hand to understand how every part works.

## What works today

A **resume server** (`servers/resume_folder/server.py`) that sits in front of a folder of resumes (`.md` files) and offers three kinds of things to an AI app:

| Kind | Name | What it does |
|---|---|---|
| Tool | `list_resumes` | Lists the names of the resumes in the folder |
| Tool | `read_resume` | Returns the text of one resume, by name |
| Tool | `list_jobs` | Lists the saved job descriptions (`jobs/*.md`) |
| Tool | `read_job` | Returns one job description, fenced in labelled tags as untrusted text |
| Resource | `resume://{name}` | Lets the app attach a resume directly when I've already chosen one |
| Prompt | `score_resume` | A ready-made request: score this resume (by name) against this job (by name) |

Safety built in:
- The server only accepts file **names** it has listed itself, never file paths, so nobody can ask it to read other files on the computer.
- Job text is treated as untrusted (it comes from the web): it is fenced off with tags, any tag inside it that could break out of the fence is removed, it is size-limited, and the model is told never to follow instructions found inside it.

A self-check script runs the server and tests all of the above using **fake** resumes and jobs.

## Run it

Needs Python 3.10 or newer (built and tested on 3.14, Windows).

```powershell
python -m venv .venv
.\.venv\Scripts\python.exe -m pip install -r requirements.txt
.\.venv\Scripts\python.exe tests\test_resume_server.py
```

You should see `all checks passed`. (A few "Tool 'read_resume' failed" lines before it are expected: they come from the checks that try wrong names on purpose.)

To use it with your own files, create a `resumes/` folder and a `jobs/` folder in the project root and put `.md` files in them. Both folders are ignored by Git on purpose.

## Project layout

```
servers/resume_folder/   the resume server (tools, resource, prompt)
tests/                   self-check and fake resumes
decisions/               one short file per design decision, with the reasons
docs/                    project brief and teaching notes
PROGRESS.md              where the project stands
```

## What's coming (not built yet)

- [ ] A small client that connects to the server, lists what it offers and calls it (no AI model yet)
- [ ] A **GitHub server** (read-only) so the agent can find evidence of my work
- [ ] An **agent loop** written by hand: decide, act, observe, stop
- [ ] An AI model provider for the loop (not chosen yet)
- [ ] Reliability: tracked state, step and time limits, retries, a log of every step
- [ ] A **human approval step** before anything is saved
- [ ] Later: remote servers over HTTP, login basics, and trying Google's Drive MCP server

## Built with

- Python
- The official MCP Python SDK (`mcp` 2.3.0), following MCP spec revision 2026-07-28
- Transport: stdio (the host starts each server as a child program)

## Decisions

The reasons behind each choice are in [`decisions/`](decisions/README.md).
