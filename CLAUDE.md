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

## 1.0 and the freeze

Ruled by the owner 2026-10-30, from estate message
`9a03aeb1-bbb5-4951-8ae4-efbee8fa906b` (every active repository names a
1.0 and freezes after it). **1.0 is the nine steps of `docs/spec.md` as
written, nothing added**, done when step 9's "Done when" is true: the
report told aloud in three minutes, pushed. The freeze is on the
tooling around the practice, not the practice: no scripts, routines,
Alfred integrations or new convention documents. Only a bug fix or what
an incident forces is built (the VM's network, `SNAG-PBI-001`, counts).
A new idea is one line under "v2" in `docs/roadmap/ideas.md`. **On
2026-11-18** the owner reads that list and opens v2 or extends the
freeze.

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
blocks it writes name the wrong writer. Alfred built a `powerbi-ae`
surface (ADR-0176; estate message `ce1f83ff-6a9e-41ee-88e8-944ed27d7d81`),
so a session that needs the planner mints its own, only when it must
read the Learning blocks to check `docs/spec.md` dates:

    curl -s -X POST http://127.0.0.1:8100/auth/session \
      -H 'Content-Type: application/json' -d '{"surface":"powerbi-ae"}'

The reply is `{id, token}`; send the token as `Authorization: Bearer
<token>`. Revoke it when the session ends:
`curl -s -X DELETE http://127.0.0.1:8100/auth/device-tokens/<id> -H
'Authorization: Bearer <token>'`. Use is read-only by convention, not by
enforcement (every token can write every domain), so a session creates
no blocks and changes none it did not create; the owner moves the
dates, and a session then matches `docs/spec.md` to them.

## Where this came from

Costed in `~/projects/web/cv`, `docs/notes/strategy.md`, "The Power BI
gap, costed 2026-10-14", and agreed by the owner that day. Set up
2026-10-14 by the cv repository's session, which chose with him a new
public repository over a folder in `~/projects/ml/kaggle` (whose Sunday
check-in reads that repository), and the monthly A&E file over the
weekly one, because whole months make year-on-year comparisons line up.
