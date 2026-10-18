# Decision records

One file per major decision, named `NNNN-short-slug.md` (copy `0000-template.md`). A decision is written **after Rex chooses and before the code is written**.

Rules:
- The "Why" section is in Rex's words. Claude asks, Rex answers, Claude writes it down.
- Claude's recommendation and whether Rex followed it are always recorded.
- Decisions are never edited away. If one changes, write a new file and mark the old one `superseded by NNNN`.

## Index

| # | Title | Status | Date |
|---|-------|--------|------|
| 0001 | Project choice and scope (personal resume-fit agent, Drive + GitHub) | accepted | 2026-10-18 |
| 0002 | Drive side: local folder now, Google's server in Phase 5 | accepted | 2026-10-18 |
| 0003 | Language Python, official `mcp` 2.x SDK | accepted | 2026-10-18 |
| 0004 | Task modes (rank all + score one) and first two tools (`list_resumes`, `read_resume`) | accepted | 2026-10-18 |
| 0005 | Transport v1 stdio; per-server folder layout | accepted (why drafted, awaiting Rex's OK) | 2026-10-18 |
| 0006 | Resume resource `resume://{name}` and prompt `score_resume` | accepted | 2026-10-18 |
