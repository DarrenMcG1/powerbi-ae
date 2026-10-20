# Handoff — 2026-10-20

## Next action

In the VM, turn on the .pbip preview option, save the report as report/ae.pbip, git rm report/ae.pbix, then commit and push. *(For: owner, ~10 min)*

## Session 2026-10-19: the VM, up to the first push

Step 1 of `docs/spec.md` is not ticked: its "Done when" needs `report/ae.pbip` pulled on the host, and the VM's first push (`1b12980`, "empty report") is a 17 KB `report/ae.pbix` only, so no TMDL text yet. The `.gitignore` already carried the two per-machine lines, so nothing to add there.

What is working: the Windows 11 Enterprise evaluation VM (`win11` in libvirt, 8 CPUs, 16 GB, 64 GB qcow2, no GPU), Power BI Desktop and Git for Windows installed, the repository public at `DarrenMcG1/powerbi-ae` and cloned in the VM, the VM online.

Problems met, which git does not show:

- **Secure boot.** virt-manager's "Customize" step gave the VM the plain `OVMF_CODE.4m.fd` firmware, and Arch's `OVMF_VARS.4m.fd` has no Microsoft keys, so Windows setup said secure boot was off. Fixed in the libvirt definition, not by recreating the VM: the loader is `OVMF_CODE.secboot.4m.fd` with `secure='yes'` and `<smm state='on'/>`, and the variable store is `~/vm/OVMF_VARS.4m.msft.fd`, built with `virt-fw-vars --enroll-redhat --secure-boot` (package `virt-firmware`). Auto-selection (`firmware='efi'`) is off because no Arch firmware descriptor advertises enrolled keys. Rejected: recreating the VM; the original XML is only in that session's scratchpad.
- **No network.** The VM got a DHCP lease, but Docker's `forward` chain has `policy drop`, which blocked libvirt's NAT replies. Fixed by hand with two rules in `DOCKER-USER` (accept from `virbr0`; accept established replies to it). They are **lost on reboot** (snag below, task added). Also started and autostarted libvirt's `default` network.
- `win11-clone` is a leftover with the old broken firmware; the owner deletes it in virt-manager, or says so.

## Decisions taken

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
