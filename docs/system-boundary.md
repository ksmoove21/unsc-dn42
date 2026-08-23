# System Boundary

## Purpose

The UNSC Delta November 42 Service Enclave provides intentionally published services to DN42 participants. It is not a general homelab network, a trusted-peer network, or a transit path into household infrastructure.

## In scope

- DN42 logical routing instance on the edge router
- Associated DN42-facing interfaces and controlled handoffs
- Firewall policy, logging, and routing constructs governing DN42 traffic
- Approved DN42 service networks and workloads
- Explicitly authorized transport to remote service locations

## Out of scope

- DN42 peer tunnels and their underlying external transport
- Edge-router functions unrelated to the DN42 logical routing instance
- BlueLine, GreenLine, RedLine, household, management, and other homelab networks
- MagmaNet and its trusted WireGuard community model
- Workloads that do not host an approved DN42 service

## Boundary protection model

DN42 reaches an external-facing edge routing instance, then crosses a Palo Alto policy-enforcement boundary before it can reach any approved internal or remote service. Routing reachability is not authorization.

DN42 is treated as untrusted transport. Any future enclave-to-enclave connection requires its own authenticated encryption overlay and explicit firewall policy.

## Initial addressing record

- IPv4 allocation: `172.23.x.x/26`
- DN42 origin ASN: `AS42424219xx`
- IPv6 allocation: redacted
