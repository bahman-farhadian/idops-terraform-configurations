# idops-terraform-configurations

Terraform monorepo for the **idops** brand (`interdisciplinary + ops`).

This repository is the **cloud control plane**. It creates, updates, and destroys objects a cloud Terraform provider actually exposes.

It is **not** configuration management. Guest OS, packages, services, and in-guest policy live in [idops-ansible-playbooks](https://github.com/bahman-farhadian/idops-ansible-playbooks). Do not copy playbooks, roles, or inventories into this tree.

Goals:

1. Learn Terraform by repeating the same cloud-control ideas on multiple providers.
2. Demonstrate cloud-computing skill at the API boundary of each cloud.

## Role Split

| Concern | This repo (Terraform) | Ansible repo |
|---|---|---|
| Cloud API objects (VPC/subnet, VM, volume, SG, managed DB, managed k8s, object storage, DNS zone) | Yes, when **this** provider can manage it | No |
| Physical servers, hypervisor hosts | No (cloud-owned, or Ansible on metal) | `aa-*`, `ab-*` |
| Guest OS baseline, packages, runtime, self-hosted k8s/DB/cache/broker | No | From `ag-*` upward |
| Connecting the two | **Outputs only**: names, UUIDs, addresses | Inventory / vars consumed from those outputs |

The same *capability name* can be Terraform on one cloud and Ansible on another:

- AWS RDS → a Terraform domain under `a-aws/`
- Postgres on an ArvanCloud Abrak → Ansible `ak-databases` (this provider has no DBaaS)

Do not copy the Ansible 21-layer tree under every cloud. A domain exists here only when the provider has an API for it.

## Operating Sequence

1. Terraform apply in this repo (cloud objects).
2. Read outputs (IPs, UUIDs, names).
3. Ansible in the other repo against those hosts.

Handoff is **data**, not code. No `remote-exec`, `file`, or SSH provisioners. No cloud-init/`init_script` grown into a substitute for Ansible.

## Structure Policy

- Root directories are **cloud providers**. They are siblings, not a stack.
- Prefixes `a`, `b`, `c`, `d` are listing order only. AWS is not “below” GCP.
- Names are kebab-case.
- Under a cloud, domain directories use `aa`, `ab`, `ac`, ... for **dependency order** of that provider’s resources.
- Every cloud directory and every domain directory has its own `README.md`.
- Implementation projects live inside a domain directory, one Terraform root each, scaffolded from `terraform-template/`.

## Root Layout

| Directory | Provider | Status |
|---|---|---|
| `a-aws/` | `hashicorp/aws` | Placeholder. README + TODO only. |
| `b-gcp/` | `hashicorp/google` | Placeholder. README + TODO only. |
| `c-digital-ocean/` | `digitalocean/digitalocean` | Placeholder. README + TODO only. |
| `d-arvancloud/` | `arvancloud/arvan` 0.6.4 | Active. Domain layout ready. |

## Active Work

Current implementation target: **ArvanCloud Cloud Server (IaaS)**.

That provider covers VMs (“Abrak”), private networks, floating IPs, security groups, SSH keys, volumes, and snapshots. It does not cover Kubernetes, object storage, managed databases, CDN, or account IAM. Those absences are Ansible’s job on top of Abraks, not missing folders in this repo.

See `d-arvancloud/README.md` for domain order, resource mapping, and the Ansible handoff.

## Implementation Projects

Copy `terraform-template/` into a domain directory. Each project is its own Terraform root (`versions.tf`, `main.tf`, `Makefile`). Real credentials and per-deployment values live in gitignored `*.tfvars` or `TF_VAR_*`, never in tracked files.

## Ignore Policy

- Never commit `.terraform/`, `*.tfstate`, or `*.tfvars`.
- Commit `*.tfvars.example` and `.terraform.lock.hcl` for a project once it exists.
- Never commit macOS `.DS_Store` files.
