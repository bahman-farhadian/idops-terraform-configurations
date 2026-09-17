# ag-tags

Status: scaffolded. No implementation project yet.

## Purpose

Cloud-side labels on IaaS objects. The provider implements this as its own resources, not as an attribute on `arvan_iaas_abrak`.

## Terraform Owns

- `arvan_iaas_tag`
- `arvan_iaas_tag_attach` (needs `instance_id`)
- `arvan_iaas_tag_detach`
- `arvan_iaas_tag_replace_batch`
- `arvan_iaas_tag` (data source)

## Ansible Owns

Nothing. Tags are API metadata. Do not invent a parallel tagging scheme in playbooks.

## Handoff

None required for guest configuration. Optional output: tag names/ids if another Terraform project needs them.

## Dependencies

- Upstream: `ad-compute` for attach (`instance_id`)
- Downstream: none

Create can happen without a VM; attach cannot.

## Planned Project

- `instance-tags/` — create tags and attach them to Abraks

## Out Of Scope

Account labels, billing tags, or anything outside these four resources.
