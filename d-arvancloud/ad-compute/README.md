# ad-compute

Status: scaffolded. No implementation project yet.

## Purpose

Abrak lifecycle as a cloud object: create, size, image, power, rebuild, rename.

This is the handoff point to Ansible. Terraform stops when the instance exists and has an address.

## Terraform Owns

- `arvan_iaas_abrak` — VM create (flavor, image, disk, region, HA, networks, security groups, SSH key)
- `arvan_iaas_abrak_action` — power and rescue actions (on, shutdown, reboot, rescue, unrescue, reset password, add/change public IP)
- `arvan_iaas_abrak_change_flavor`
- `arvan_iaas_abrak_change_disk_size`
- `arvan_iaas_abrak_rebuild`
- `arvan_iaas_abrak_rename`
- Data sources: `arvan_iaas_abrak`, `arvan_iaas_image`, `arvan_iaas_options`, `arvan_iaas_quota`

`init_script` stays empty or a one-shot bootstrap. Do not put package installs, hardening, or services there.

## Ansible Owns

From first SSH onward: `ag-os-baseline-and-hardening`, then runtime, orchestration, traffic, data, identity, observability, and CI/CD in the Ansible repo. No `remote-exec` from this project.

## Handoff

Output Abrak name, UUID, and addresses. Those become Ansible inventory/vars in the other repo.

## Dependencies

- Upstream: `aa-identity-and-access` (SSH key name), `ab-network-and-connectivity` (network UUID), `ac-security-groups` (group names)
- Downstream: `ae-block-storage` (attach extra disks), `af-snapshots-and-recovery`, `ag-tags` (attach needs instance id)

## Planned Project

- `abrak-instance/` — one or more VMs from a distribution image, wired to the network, SG, and key from upstream domains

## Notes

Getting the Abrak UUID often requires the `arvan_iaas_abrak` data source after create. Plan for that when wiring attach/snapshot resources.
