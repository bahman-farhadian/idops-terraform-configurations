# ae-block-storage

Status: scaffolded. No implementation project yet.

## Purpose

Extra disks independent of the Abrak boot disk, as cloud volumes.

The boot disk size lives on `arvan_iaas_abrak` / `arvan_iaas_abrak_change_disk_size` in `ad-compute`. This domain is additional volumes.

## Terraform Owns

- `arvan_iaas_volume`
- `arvan_iaas_volume_attach` / `arvan_iaas_volume_detach`
- `arvan_iaas_volume` (data source)

Create and attach only. Do not format or mount from Terraform.

## Ansible Owns

`mkfs`, mount points, fstab, and anything that uses the filesystem (databases, artifact caches).

## Handoff

Output volume id and the Abrak it is attached to. Ansible performs in-guest storage setup.

## Dependencies

- Upstream: `ad-compute` (Abrak UUID for attach)
- Downstream: none in this repo

## Planned Project

- `data-volume/` — create a volume in-region and attach it to an Abrak

## Out Of Scope

Object storage (S3-compatible). This provider has no bucket resource.
