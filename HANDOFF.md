# Handoff — 2026-11-03

## Next action

In the VM, resume step 2 at question 1: check one site and month's All against Unplanned plus New planned, then save as report/ae.pbip and push. *(For: owner, ~45 min)*

## Session 2026-10-19: the VM, up to the first push

Step 1 of `docs/spec.md` is not ticked: its "Done when" needs `report/ae.pbip` pulled on the host, and the VM's first push (`1b12980`, "empty report") is a 17 KB `report/ae.pbix` only, so no TMDL text yet. The `.gitignore` already carried the two per-machine lines, so nothing to add there.

What is working: the Windows 11 Enterprise evaluation VM (`win11` in libvirt, 8 CPUs, 16 GB, 64 GB qcow2, no GPU), Power BI Desktop and Git for Windows installed, the repository public at `DarrenMcG1/powerbi-ae` and cloned in the VM, the VM online.

Problems met, which git does not show:

- **Secure boot.** virt-manager's "Customize" step gave the VM the plain `OVMF_CODE.4m.fd` firmware, and Arch's `OVMF_VARS.4m.fd` has no Microsoft keys, so Windows setup said secure boot was off. Fixed in the libvirt definition, not by recreating the VM: the loader is `OVMF_CODE.secboot.4m.fd` with `secure='yes'` and `<smm state='on'/>`, and the variable store is `~/vm/OVMF_VARS.4m.msft.fd`, built with `virt-fw-vars --enroll-redhat --secure-boot` (package `virt-firmware`). Auto-selection (`firmware='efi'`) is off because no Arch firmware descriptor advertises enrolled keys. Rejected: recreating the VM; the original XML is only in that session's scratchpad.
- **No network.** The VM got a DHCP lease, but Docker's `forward` chain has `policy drop`, which blocked libvirt's NAT replies. Fixed by hand with two rules in `DOCKER-USER` (accept from `virbr0`; accept established replies to it). They are **lost on reboot** (snag below, task added). Also started and autostarted libvirt's `default` network.
- `win11-clone` is a leftover with the old broken firmware; the owner deletes it in virt-manager, or says so.

## Decisions taken

- **1.0 is the nine spec steps; the tooling is frozen; v2 review 2026-11-18**, owner 2026-10-30, from estate-manager's message `9a03aeb1-bbb5-4951-8ae4-efbee8fa906b`. Recorded in `CLAUDE.md`, "1.0 and the freeze". Rejected: dropping the PDF from 1.0, and ending 1.0 at the overview page (that loses the told-aloud finish the cv session waits on). Rejected dates: 2026-12-02 and 2027-01-04 (project-manager's date), both long for a 15-hour build.

- **Start 2026-10-19, finish 2026-11-04**, owner 2026-10-18. The steps, their order and their sizes are unchanged; only the dates moved.
- **A new public repository**, owner 2026-10-14. Rejected: a folder in `~/projects/ml/kaggle`, whose Sunday check-in reads that repository's milestones; a private repository, since the point is a report he can show.
- **Public Health Scotland's monthly A&E file**, owner 2026-10-14, with PHS's hospital and health board code lists as the dimensions. Rejected: the weekly file the costing first suggested, because weeks do not line up across years and year on year is one of the DAX steps.
- **A `.pbip` project, not only a `.pbix`**, by the session: the M and DAX save as text, so each commit shows what he wrote. Whether the save option is still a preview is checked in step 1, not assumed.
- **The data is committed**, unlike kaggle's: the Open Government Licence allows it with a credit, which the README carries.
- **No weekly check-in routine.** The planner's blocks are the accountability; a claude.ai routine like kaggle's was not asked for.

## Not done

The Learning area on Alfred's planner lists the repositories it covers (kaggle, Athenaeum, InvestingAssistant); this one is not added, since an area's settings are the owner's.

## Session 2026-10-20: Alfred's token surface

Alfred closed our request `38d6bafe-ab21-46f2-90b9-ac9e4bbde5a7` with a note and filed `ce1f83ff-6a9e-41ee-88e8-944ed27d7d81`: the `powerbi-ae` surface is live (ADR-0176). `CLAUDE.md`, "Alfred's planner tokens", now carries the mint and revoke commands and the read-only convention. No token was minted: nothing this session needed the planner. The earlier message `8a043b98-4d99-443a-9eb6-df348d0f9be0` ("mint none until built") was met by the build and closed as superseded. Whether a session may change a block it did not create is open on Alfred's side (their row 387); until it is ruled, a session here changes none.

## Session 2026-10-30: the 1.0 and the freeze

Estate-manager asked every active repository to name a 1.0 and a freeze; the owner ruled (see Decisions taken), and the message was closed. Found while checking: only step 1 is done, with no `report/ae.pbip`, `data/` or `notes.md` on the host, so the 2026-11-04 finish cannot hold; a task asks the owner to move the planner blocks. The working tree carries the owner's uncommitted tick on step 1 in `docs/spec.md`, whose "Done when" (the `.pbip` pulled on the host) is not yet true; this session left it uncommitted.

## Session 2026-10-30: a v2 line from learning-manager

Estate message `110d2309-6956-4833-ad08-a6ef1b6734bf` from learning-manager carried the owner's ruling for one v2 line: Azure Data Factory or Fabric feeding the existing report. It is now the first line under "v2" in `docs/roadmap/ideas.md`, for the 2026-11-18 review to open or not; it is not a 1.0 step and leaves the freeze as it was. No task was added, since the 2026-11-18 review task already covers reading the list. The message was closed with a note naming the line. The owner's uncommitted tick on step 1 in `docs/spec.md` was again left alone.

## Session 2026-11-03: how to query in Power Query, and the 45-minute blocks

The owner asked how to query the three step 2 files once loaded. Answered in words only (`CLAUDE.md`, "The one rule"): a Power Query query is a saved chain of steps, not a `SELECT`; the questions in step 2 are answered with the column filter, column profiling over the whole data set, and Merge Queries' join kinds. No M written.

Estate message `fe0ccb46-0795-4534-88e8-ab6cc9140e38` from learning-manager: from 2026-11-02 the Learning blocks are 45 minutes each weekday, and the nine steps end about 2026-12-01, after the 2026-11-18 v2 review. Logged in `docs/roadmap/tasks.md` (the move-blocks task ticked; a session task to match `docs/spec.md`; an owner task to rule on the review date) and closed. `docs/spec.md` was not edited: the owner's uncommitted tick on step 1 is still in it.

Where the owner stopped, 2026-11-03: step 2, question 1. The three files are loaded in the VM. The column profile showed 40,227 rows, with `All` and `Unplanned` near 19,022 each and `New planned` near 2,200, so row counts do not add; whether attendances do (`All` = `Unplanned` + `New planned` for one site and month) is unchecked. The owner stepped back there and asked for the hints in writing: they are now "Deeper dive" under step 2 in `docs/spec.md`. Rejected: a new supplementary document, since `CLAUDE.md` limits a session to the spec and the convention documents, and the freeze bars new documents. If the check shows `All` is not the sum, the spec's column notes are wrong and a session corrects them. On the host, `report/` still holds only `ae.pbix`, so step 1's `.pbip` is not pushed yet.
