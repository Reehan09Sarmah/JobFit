# Decision records

One file per major decision, named `NNNN-short-slug.md` (copy `0000-template.md`). A decision is written **after Rex chooses and before the code is written**.

Rules:
- The "Why" section is written by Claude in simple language from what Rex said (his short quote is kept). If Rex's reasoning is wrong, Claude corrects it in the file and explains the fix. Rex does not have to write or approve the wording (standing instruction, 2026-10-18).
- Claude's recommendation and whether Rex followed it are always recorded.
- Decisions are never edited away. If one changes, write a new file and mark the old one `superseded by NNNN`.

## Index

| # | Title | Status | Date |
|---|-------|--------|------|
| 0001 | Project choice and scope (personal resume-fit agent, Drive + GitHub) | accepted | 2026-10-18 |
| 0002 | Drive side: local folder now, Google's server in Phase 5 | accepted | 2026-10-18 |
| 0003 | Language Python, official `mcp` 2.x SDK | accepted | 2026-10-18 |
| 0004 | Task modes (rank all + score one) and first two tools (`list_resumes`, `read_resume`) | accepted | 2026-10-18 |
| 0005 | Transport v1 stdio; per-server folder layout | accepted | 2026-10-18 |
| 0006 | Resume resource `resume://{name}` and prompt `score_resume` | accepted (partly superseded by 0007) | 2026-10-18 |
| 0007 | Job descriptions as `.md` files in the same server (`list_jobs`, `read_job`; prompt takes `job_name`) | accepted | 2026-10-18 |
