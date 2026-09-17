# a-aws

Status: **not started**. Placeholder only.

Provider: [`hashicorp/aws`](https://registry.terraform.io/providers/hashicorp/aws/latest/docs)

AWS is a sibling of the other clouds in this repo. The `a-` prefix is listing order, not a dependency.

This directory will own **AWS API objects**. Guest configuration stays in [idops-ansible-playbooks](https://github.com/bahman-farhadian/idops-ansible-playbooks). Handoff is Terraform outputs (IPs, IDs, names), not playbooks copied here.

When work starts, add a domain only if `hashicorp/aws` can manage that product. Do not add Ansible layers such as OS hardening or in-guest Docker.

## TODO

Cloud-control domains (managed AWS APIs):

- [ ] Pin `hashicorp/aws` in a first implementation project (copied from `terraform-template/`).
- [ ] `aa-identity-and-access` — IAM users, roles, policies, instance profiles.
- [ ] `ab-network-and-connectivity` — VPC, subnets, route tables, IGW/NAT, elastic IPs.
- [ ] `ac-security-groups` — security groups and rules.
- [ ] `ad-compute` — EC2 instances, key pairs, AMIs.
- [ ] `ae-block-storage` — EBS volumes and attachments.
- [ ] `af-object-storage` — S3 buckets and policies.
- [ ] `ag-traffic-management` — ALB/NLB, target groups, listeners.
- [ ] `ah-databases` — RDS (and later ElastiCache / brokers if needed).
- [ ] `ai-dns` — Route 53.
- [ ] `aj-container-orchestration` — EKS.
- [ ] `ak-snapshots-and-recovery` — snapshots, AWS Backup.
- [ ] Document remote state (S3 + DynamoDB lock) before any shared apply.
- [ ] Keep credentials in gitignored `*.tfvars` or the environment. Never commit keys.

Ansible still owns guest OS, packages, and anything self-hosted **on** EC2 instead of consumed as a managed AWS API.

Leave this directory as a README until the first AWS project is actually implemented.
