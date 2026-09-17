# terraform-template

Reusable starter for a new Terraform project in this repository.

A project here manages **cloud API objects only**. Configuration management belongs in [idops-ansible-playbooks](https://github.com/bahman-farhadian/idops-ansible-playbooks). Handoff to Ansible is outputs (names, UUIDs, addresses), not provisioners.

## How To Use

1. Copy this directory into a cloud domain, for example `d-arvancloud/aa-identity-and-access/ssh-keys/`.
2. Rename it to kebab-case that names the project, not the cloud.
3. Set `required_providers` in `versions.tf` to the cloud you are targeting.
4. Replace the empty `main.tf` with real resources.
5. Copy `terraform.tfvars.example` to `terraform.tfvars` (gitignored) and fill real values.
6. Export anything Ansible or a downstream domain needs from `outputs.tf`.
7. Keep every operator command in `make help`.

## Required Files

- `versions.tf` — Terraform and provider version pins
- `providers.tf` — provider configuration (no secrets)
- `variables.tf` — inputs
- `outputs.tf` — outputs (the Ansible handoff)
- `main.tf` — resources
- `terraform.tfvars.example` — documented dummy values
- `Makefile` — `help`, `init`, `fmt`, `validate`, `plan`
- `README.md` — scope, dependencies, runbook, handoff

Do not add `apply` / `destroy` Make targets until the project is ready to mutate real cloud state, and then keep them explicit in `make help`.

## Forbidden

- Ansible playbooks, roles, inventories, or `ansible-playbook` wrappers
- `provisioner "remote-exec"`, `provisioner "file"`, and SSH `connection` blocks
- Hardcoded credentials, tokens, or keys
- Using `init_script` / user-data as a replacement for Ansible

## Secrets

API keys, tokens, and real resource names go in `terraform.tfvars` or environment variables (`TF_VAR_*`). Never hardcode them.
