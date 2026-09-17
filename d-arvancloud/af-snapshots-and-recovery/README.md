# af-snapshots-and-recovery

Status: scaffolded. No implementation project yet.

## Purpose

Point-in-time VM snapshots via the Cloud Server API. This is the only backup primitive in `arvancloud/arvan` 0.6.4.

## Terraform Owns

- `arvan_iaas_abrak_snapshot`

## Ansible Owns

Application dumps, file-level backup, restore drills, and off-site copies (`as-backup-and-disaster-recovery` in the Ansible repo).

## Handoff

Output snapshot id and source Abrak. Restore/rebuild of the VM is a compute API action, not an Ansible play.

## Dependencies

- Upstream: `ad-compute` (Abrak UUID)
- Downstream: none in this provider

## Planned Project

- `abrak-snapshot/` — named snapshot of an existing Abrak

## Out Of Scope

Managed backup products and volume-only snapshot APIs that this provider does not expose.
