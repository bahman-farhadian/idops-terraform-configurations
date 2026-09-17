# ab-network-and-connectivity

Status: scaffolded. No implementation project yet.

## Purpose

Private addressing, public reachability, and reverse DNS as **cloud API objects**.

## Terraform Owns

- `arvan_iaas_subnet` — private network + subnet (CIDR, DHCP, DNS, gateway)
- `arvan_iaas_network_attach` / `arvan_iaas_network_detach`
- `arvan_iaas_floatip` — floating (public) IP
- `arvan_iaas_ptr` — PTR record
- `arvan_iaas_network` (data source)

## Ansible Owns

In-guest network clients (resolvers, NTP). Self-hosted DNS/DHCP/NTP if you run them on Abraks (`ae-internal-services` in the Ansible repo). This provider’s subnet DHCP is the only DHCP API you get.

## Handoff

Output network UUID, CIDR, gateway, and floating IP address. Compute and Ansible inventory consume those. Do not configure netplan/interfaces from Terraform.

## Dependencies

- Upstream: none (can be created before VMs)
- Downstream: `ad-compute` consumes network UUIDs; floating IP and PTR attach to an Abrak

## Planned Projects

- `private-subnet/` — one isolated network with DHCP/gateway
- `floating-ip/` — allocate a public IP (attach happens with compute)
- `ptr/` — reverse DNS for a public address

## Out Of Scope

Managed DNS zones, CDN, load balancers. Not in this provider.
