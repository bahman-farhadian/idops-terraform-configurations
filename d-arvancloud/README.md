# d-arvancloud

Status: **active**. Domain layout is in place; implementation projects come next.

Provider: [`arvancloud/arvan`](https://registry.terraform.io/providers/arvancloud/arvan/latest/docs) **0.6.4**

This is ArvanCloud **Cloud Server (IaaS)** only. VMs are called Abraks. Auth is a single API key from the ArvanCloud dashboard.

Terraform here creates the envelope (keys, network, firewall, VM, volume, snapshot). Ansible in [idops-ansible-playbooks](https://github.com/bahman-farhadian/idops-ansible-playbooks) configures the guest. No playbooks in this directory.

The public GitHub repo for this provider is archived. ArvanCloud also publishes a newer registry at `terraform.arvancloud.ir` (CDN + expanded IaaS). This tree is scoped to the public HashiCorp Registry provider above. Do not mix the two in one project.

## What This Provider Can Manage

Mapped from `ResourcesMap` / `DataSourcesMap` in `arvancloud/arvan` 0.6.4 (`internal/provider/provider.go`). One folder per resource **group**, not per resource.

| Domain | Resources |
|---|---|
| `aa-identity-and-access` | `arvan_iaas_sshkey` |
| `ab-network-and-connectivity` | `arvan_iaas_subnet`, `arvan_iaas_network_attach`, `arvan_iaas_network_detach`, `arvan_iaas_floatip`, `arvan_iaas_ptr` |
| `ac-security-groups` | `arvan_iaas_security_group`, `arvan_iaas_security_group_rule`, `arvan_iaas_abrak_assign_security_group`, `arvan_iaas_abrak_remove_security_group` |
| `ad-compute` | `arvan_iaas_abrak`, `arvan_iaas_abrak_action`, `arvan_iaas_abrak_change_flavor`, `arvan_iaas_abrak_change_disk_size`, `arvan_iaas_abrak_rebuild`, `arvan_iaas_abrak_rename` |
| `ae-block-storage` | `arvan_iaas_volume`, `arvan_iaas_volume_attach`, `arvan_iaas_volume_detach` |
| `af-snapshots-and-recovery` | `arvan_iaas_abrak_snapshot` |
| `ag-tags` | `arvan_iaas_tag`, `arvan_iaas_tag_attach`, `arvan_iaas_tag_detach`, `arvan_iaas_tag_replace_batch` |

Data sources (used inside those domains, not extra folders): `arvan_iaas_abrak`, `arvan_iaas_image`, `arvan_iaas_network`, `arvan_iaas_options`, `arvan_iaas_quota`, `arvan_iaas_security_group`, `arvan_iaas_sshkey`, `arvan_iaas_tag`, `arvan_iaas_volume`.

## In The Provider, Not A Domain

| Resource | Why it has no folder |
|---|---|
| `arvan_iaas_cdn_security_group` | Registered, but schema is only `region`. CDN leftover, not Cloud Server policy. |
| `arvan_iaas_floatip_attach` / `_detach` | Commented out in the provider (`# TODO`). Not implemented. Public IP changes go through `arvan_iaas_abrak_action`. |

## What It Cannot Manage

Managed Kubernetes, object storage, DBaaS, CDN/DNS product, load balancers, account IAM. Those are **not** missing Terraform folders. They are Ansible domains on top of Abraks (or a different provider later).

## Domain Layout (Dependency Order)

1. `aa-identity-and-access` — SSH keys
2. `ab-network-and-connectivity` — private subnets, floating IPs, PTR, attach/detach
3. `ac-security-groups` — groups and rules (needed at VM create)
4. `ad-compute` — Abraks and lifecycle
5. `ae-block-storage` — extra volumes
6. `af-snapshots-and-recovery` — VM snapshots
7. `ag-tags` — labels and attach/detach on instances

Physical servers and hypervisor hosts are cloud-owned. There is no `aa-physical-*` / `ab-hypervisor-*` here.

## Handoff To Ansible

After Terraform apply, export data. The Ansible repo consumes it. Do not SSH from Terraform.

| Terraform domain | Outputs for Ansible | Ansible repo (self-hosted / guest) |
|---|---|---|
| `aa-identity-and-access` | SSH key **name** (public key only in TF) | SSH as the operator; private key never in this repo |
| `ab-network-and-connectivity` | network UUID, CIDR, floating IP | in-guest DNS/NTP clients; self-hosted DNS if needed (`ae-internal-services`) |
| `ac-security-groups` | group names | in-guest firewall only if you add one; cloud SG stays here |
| `ad-compute` | Abrak name, UUID, addresses | `ag-os-baseline-and-hardening` and everything above |
| `ae-block-storage` | volume id, attachment | mkfs, mount, fstab |
| `af-snapshots-and-recovery` | snapshot id | app-level backup / `as-backup-and-disaster-recovery` |
| `ag-tags` | none | — |
| *(no API here)* | — | `ah-container-runtime`, `ai-container-orchestration`, `aj-traffic-management`, `ak-databases`, `al-data-caching`, `am-message-brokers`, … |

`init_script` on an Abrak stays empty or a one-shot bootstrap. Hardening and services are Ansible.

## How To Add A Project

Copy `../../terraform-template/` into the matching domain. Pin:

```hcl
terraform {
  required_providers {
    arvan = {
      source  = "arvancloud/arvan"
      version = "0.6.4"
    }
  }
}

provider "arvan" {
  api_key = var.api_key
}
```

Pass `api_key` through `TF_VAR_api_key` or a gitignored `terraform.tfvars`. The Arvan API expects the key in the form documented by the provider (`Apikey <token>`).

## Next

First implementation project: SSH keys under `aa-identity-and-access/`, then a private subnet under `ab-network-and-connectivity/`.
