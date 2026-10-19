# CLAUDE.md

Guidance for Claude Code in this repository.

## The one rule

**Do not write, fix, review or complete the M or DAX here.** The owner
builds the report himself so that he can walk through every measure in
an interview; that is the repository's only purpose (`docs/spec.md`,
"The rules"). Explaining a concept in words is fine. Writing a measure
or a query, even a snippet "to show the idea", is not, and neither is
editing anything under `report/`. If asked to, say so and point at this
file.

What a session may touch: the convention documents (`HANDOFF.md`,
`docs/roadmap/`, this file, `README.md` until the owner's write-up
replaces it) and `docs/spec.md`, whose dates match the blocks on
Alfred's planner.

## Shape

- `docs/spec.md`: the steps, their dates and the rules. Each step is a
  block on Alfred's planner in the Learning area; change a date in both.
- `data/`: Public Health Scotland's CSVs, committed (Open Government
  Licence; the README credits the source).
- `report/`: the owner's Power BI project (`ae.pbip` and its folders),
  the PDF export and a `.pbix` copy.
- `notes.md`: the owner's notes, checks and stuck points.

Power BI Desktop runs in a Windows 11 evaluation VM on KVM, because it
is Windows-only and this machine runs Arch. The owner commits from the
VM; pull on the host before editing.

## Alfred's planner tokens

A session here never borrows another consumer's `POST /auth/session`
surface (`cv`, `claude`, `web`): the token's label and, for `cv`, the
blocks it writes name the wrong writer. This repository asked Alfred
for a `powerbi-ae` surface (estate message
`38d6bafe-ab21-46f2-90b9-ac9e4bbde5a7`); until it exists, mint nothing
and leave the planner dates unchecked.

## Where this came from

Costed in `~/projects/web/cv`, `docs/notes/strategy.md`, "The Power BI
gap, costed 2026-10-14", and agreed by the owner that day. Set up
2026-10-14 by the cv repository's session, which chose with him a new
public repository over a folder in `~/projects/ml/kaggle` (whose Sunday
check-in reads that repository), and the monthly A&E file over the
weekly one, because whole months make year-on-year comparisons line up.
