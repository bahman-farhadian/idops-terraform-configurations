# aa-identity-and-access

Status: scaffolded. No implementation project yet.

## Purpose

Register an SSH **public** key in the Cloud Server API so Abraks can be created with `key_name`. On this provider that is **SSH keys only** (`arvan_iaas_sshkey`). There is no IAM, users, roles, or projects API in `arvancloud/arvan` 0.6.4.

## Terraform Owns

- `arvan_iaas_sshkey` (resource)
- `arvan_iaas_sshkey` (data source)

## Ansible Owns

Operator SSH to the guest, sudo, and later identity stacks (`ao-identity-and-access` in the Ansible repo). The private key never enters this repository.

## Handoff

Output the key **name**. Compute consumes it. Ansible uses the matching private key on the operator machine, not a Terraform provisioner.

## Dependencies

- Upstream: none
- Downstream: `ad-compute` (pass `key_name` into an Abrak)

## Planned Project

- `ssh-keys/` — register an operator public key in a region, output the key name

Copy from `terraform-template/` when implementing.

## Out Of Scope

Account IAM, API tokens, SSO. Those are dashboard/account concerns, not this provider.
