# c-digital-ocean

Status: **not started**. Placeholder only.

Provider: [`digitalocean/digitalocean`](https://registry.terraform.io/providers/digitalocean/digitalocean/latest/docs)

DigitalOcean is a sibling of the other clouds in this repo. The `c-` prefix is listing order, not a dependency.

This directory will own **DigitalOcean API objects**. Guest configuration stays in [idops-ansible-playbooks](https://github.com/bahman-farhadian/idops-ansible-playbooks). Handoff is Terraform outputs, not playbooks copied here.

DigitalOcean sits between ArvanCloud (IaaS-only) and AWS/GCP: droplets plus managed Kubernetes, Spaces, load balancers, and managed databases. Add a domain only for products this provider can manage.

## TODO

Cloud-control domains (managed DigitalOcean APIs):

- [ ] Pin `digitalocean/digitalocean` in a first implementation project (copied from `terraform-template/`).
- [ ] `aa-identity-and-access` — SSH keys, projects.
- [ ] `ab-network-and-connectivity` — VPCs, floating IPs, firewalls.
- [ ] `ac-security-groups` — cloud firewalls and rules (if kept separate from network).
- [ ] `ad-compute` — droplets.
- [ ] `ae-block-storage` — volumes and attachments.
- [ ] `af-object-storage` — Spaces.
- [ ] `ag-traffic-management` — load balancers.
- [ ] `ah-databases` — managed databases.
- [ ] `ai-dns` — domains and records.
- [ ] `aj-container-orchestration` — DOKS.
- [ ] `ak-snapshots-and-recovery` — droplet snapshots.
- [ ] Document remote state before any shared apply.
- [ ] Keep the API token in gitignored `*.tfvars` or `DIGITALOCEAN_TOKEN`. Never commit it.

Ansible still owns guest OS, packages, and anything self-hosted **on** a droplet instead of consumed as a managed DigitalOcean API.

Leave this directory as a README until the first DigitalOcean project is actually implemented.
