# b-gcp

Status: **not started**. Placeholder only.

Provider: [`hashicorp/google`](https://registry.terraform.io/providers/hashicorp/google/latest/docs)

GCP is a sibling of the other clouds in this repo. The `b-` prefix is listing order, not a dependency.

This directory will own **GCP API objects**. Guest configuration stays in [idops-ansible-playbooks](https://github.com/bahman-farhadian/idops-ansible-playbooks). Handoff is Terraform outputs, not playbooks copied here.

When work starts, add a domain only if `hashicorp/google` can manage that product. Do not add Ansible layers such as OS hardening or in-guest Docker.

## TODO

Cloud-control domains (managed GCP APIs):

- [ ] Pin `hashicorp/google` in a first implementation project (copied from `terraform-template/`).
- [ ] `aa-identity-and-access` — service accounts, IAM bindings.
- [ ] `ab-network-and-connectivity` — VPC, subnets, Cloud NAT, external IPs.
- [ ] `ac-security-groups` — VPC firewall rules.
- [ ] `ad-compute` — Compute Engine VMs, instance templates.
- [ ] `ae-block-storage` — persistent disks.
- [ ] `af-object-storage` — Cloud Storage buckets.
- [ ] `ag-traffic-management` — Cloud Load Balancing.
- [ ] `ah-databases` — Cloud SQL (and later Memorystore / Pub/Sub if needed).
- [ ] `ai-dns` — Cloud DNS.
- [ ] `aj-container-orchestration` — GKE.
- [ ] `ak-snapshots-and-recovery` — disk snapshots, backup policy.
- [ ] Document remote state (GCS + lock) before any shared apply.
- [ ] Keep credentials in gitignored `*.tfvars` or Application Default Credentials. Never commit keys.

Ansible still owns guest OS, packages, and anything self-hosted **on** GCE instead of consumed as a managed GCP API.

Leave this directory as a README until the first GCP project is actually implemented.
