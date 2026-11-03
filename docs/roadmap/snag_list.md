# Snag List

Defects found and deliberately left unfixed. `SNAG-PBI-000` entries
under "Open"; move to "Fixed" with a date rather than deleting.

## Open

- **SNAG-PBI-001** (2026-10-19): the VM loses its network on host reboot.
  Docker's `forward` chain drops libvirt's NAT traffic; the two
  `DOCKER-USER` rules for `virbr0` that fix it were added by hand and
  are not persistent. Left unfixed: a unit in `/etc/systemd/system/` is
  the owner's to approve. Task added under "Host".

## Fixed

None.
