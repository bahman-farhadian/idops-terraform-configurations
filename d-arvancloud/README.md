# d-arvancloud

Status: **active**. Domain layout is in place; implementation projects come next.

Provider: [`arvancloud/arvan`](https://registry.terraform.io/providers/arvancloud/arvan/latest/docs) **0.6.4**

This is ArvanCloud **Cloud Server (IaaS)** only. VMs are called Abraks. Auth is a single API key from the ArvanCloud dashboard.

Terraform here creates the envelope (keys, network, firewall, VM, volume, snapshot). Ansible in [idops-ansible-playbooks](https://github.com/bahman-farhadian/idops-ansible-playbooks) configures the guest. No playbooks in this directory.

The public GitHub repo for this provider is archived. ArvanCloud also publishes a newer registry at `terraform.arvancloud.ir` (CDN + expanded IaaS). This tree is scoped to the public HashiCorp Registry provider above. Do not mix the two in one project.

## What This Provider Can Manage

| Domain | Resources |
|---|---|
| Identity | `arvan_iaas_sshkey` |
| Network | `arvan_iaas_subnet`, `arvan_iaas_network_attach` / `_detach`, `arvan_iaas_floatip`, `arvan_iaas_ptr` |
| Security groups | `arvan_iaas_security_group`, `arvan_iaas_security_group_rule`, assign/remove on Abrak |
| Compute | `arvan_iaas_abrak`, actions, flavor/disk change, rename, rebuild |
| Block storage | `arvan_iaas_volume`, attach/detach |
| Snapshots | `arvan_iaas_abrak_snapshot` |

Useful data sources: images, networks, quotas, flavors/options, existing Abraks, volumes, SSH keys, security groups.

## What It Cannot Manage

Managed Kubernetes, object storage, DBaaS, CDN/DNS product, load balancers, account IAM. Those are **not** missing Terraform folders. They are Ansible domains on top of Abraks (or a different provider later).

## Domain Layout (Dependency Order)

1. `aa-identity-and-access` — SSH keys
2. `ab-network-and-connectivity` — private subnets, floating IPs, PTR, attach/detach
3. `ac-security-groups` — groups and rules (needed at VM create)
4. `ad-compute` — Abraks and lifecycle
5. `ae-block-storage` — extra volumes
6. `af-snapshots-and-recovery` — VM snapshots

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
