# ac-security-groups

Status: scaffolded. No implementation project yet.

## Purpose

Instance **cloud** firewall policy. Placed **before** compute because Abrak create takes a list of security group names.

## Terraform Owns

- `arvan_iaas_security_group`
- `arvan_iaas_security_group_rule`
- `arvan_iaas_abrak_assign_security_group` / `arvan_iaas_abrak_remove_security_group`
- `arvan_iaas_security_group` (data source)

`arvan_iaas_cdn_security_group` is unused here (CDN leftover, not Cloud Server policy).

## Ansible Owns

In-guest nftables/iptables only if you add a host firewall later (`at-security-and-compliance` / hardening). The cloud security group remains the primary network policy for Abraks.

## Handoff

Output group names (and IDs if the API gives them). Compute attaches them at create. Ansible does not recreate these rules.

## Dependencies

- Upstream: none
- Downstream: `ad-compute` attaches groups at create time; assign/remove resources are for later changes

## Planned Project

- `instance-firewall/` — baseline group (SSH in, established out) plus extra groups per role

Rules stay least-privilege. Do not open 0.0.0.0/0 except where a public service is the point of the group.

## Out Of Scope

WAF, CDN firewall, account-level security products.
