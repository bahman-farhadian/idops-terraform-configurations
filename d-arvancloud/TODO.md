# TODO

Delivery queue for `d-arvancloud/`. Layout is done. Implementation is not.

Provider: `arvancloud/arvan` **0.6.4** (HashiCorp Registry). Do not mix with `terraform.arvancloud.ir`.

Keep entries short. Design work belongs in the branch that picks the item up.

## Need From ArvanCloud (blocked until these exist)

Nothing below is a Terraform file. It is account and API fact we cannot invent.

- [ ] Cloud Server (IaaS) enabled on the account, with billing that can create a small Abrak.
- [ ] API key from the dashboard ([API keys](https://panel.arvancloud.ir/profile/api-keys)). Pass it as `TF_VAR_api_key` or gitignored `terraform.tfvars`. Never commit it. Provider expects the documented `Apikey <token>` form.
- [ ] Region code we will use (confirm in the panel; examples in provider docs include `ir-thr-c2`). All resources in 0.6.4 take `region`.
- [ ] Quota headroom in that region: at least 1 SSH key, 1 security group + rules, 1 private network, 1 Abrak, 1 extra volume, 1 floating IP, snapshots, tags. First read-only check: data source `arvan_iaas_quota`.
- [ ] Flavor ID valid in that region (from sizes API / `arvan_iaas_options`). Start with the smallest (docs examples use `g1-1-1-0` — verify, do not assume).
- [ ] Image that exists there (`image.type` + `image.name`, e.g. a Debian distribution). Confirm with `arvan_iaas_image`.
- [ ] One operator **public** SSH key to upload. Private key stays on the operator machine, never in this repo.
- [ ] Default/public network name or UUID in that region (data source `arvan_iaas_network`) so Abraks and floating IPs can reach the internet.
- [ ] Confirm we stay on registry provider 0.6.4 for this delivery. A newer Arvan registry is a different project.

## Delivery Roadmap

Implement in this order. Each domain is a Terraform root copied from `../../terraform-template/`. Outputs only; no provisioners, no Ansible in this tree.

### 0. Bootstrap

- [ ] Pin `arvancloud/arvan` 0.6.4 in the first project.
- [ ] `terraform.tfvars.example` with generic keys only (`region`, flavor, image name). Real values in gitignored `terraform.tfvars`.
- [ ] Decide state: local for learning vs remote (S3-compatible / other) before any shared apply.
- [ ] Read-only smoke: `terraform init` + data sources (`arvan_iaas_quota`, `arvan_iaas_options`, `arvan_iaas_image`, `arvan_iaas_network`). No apply until this works.

### 1. `aa-identity-and-access`

- [ ] Project `ssh-keys/`: `arvan_iaas_sshkey`, output key name.

### 2. `ab-network-and-connectivity`

- [ ] Project `private-subnet/`: `arvan_iaas_subnet` (CIDR, DHCP, gateway). Output `network_uuid`.
- [ ] Project `floating-ip/`: `arvan_iaas_floatip`. Attach via compute action (`arvan_iaas_abrak_action`); `floatip_attach` is not implemented in 0.6.4.
- [ ] Project `ptr/` only after a public IP exists.

### 3. `ac-security-groups`

- [ ] Project `instance-firewall/`: group + least-privilege rules (SSH in). Output group names for Abrak create.

### 4. `ad-compute`

- [ ] Project `abrak-instance/`: one small Abrak wired to key name, network UUID, security group names.
- [ ] Output name, UUID, addresses (Ansible inventory input).
- [ ] Leave `init_script` empty. No `remote-exec`.

### 5. `ae-block-storage`

- [ ] Project `data-volume/`: volume + attach to the Abrak. Do not mkfs/mount here.

### 6. `af-snapshots-and-recovery`

- [ ] Project `abrak-snapshot/`: snapshot of the Abrak. Output snapshot id.

### 7. `ag-tags`

- [ ] Project `instance-tags/`: create tags, attach to the Abrak.

### 8. Handoff (this repo stays cloud-only)

- [ ] Document outputs (key name, network UUID, CIDR, floating IP, Abrak UUID/addresses, volume id, snapshot id) so Ansible can consume them. Do not copy playbooks here.

## Out Of Scope For This Delivery

Not in `arvancloud/arvan` 0.6.4: DBaaS, object storage, Kubernetes, CDN/DNS, load balancer, account IAM, `arvan_iaas_cdn_security_group`, unimplemented `floatip_attach` / `floatip_detach`.
