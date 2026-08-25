# System Security Plan

## System Identification

| Field | Value |
|---|---|
| System name | UNSC Delta November 42 Service Enclave |
| System type | DN42-connected network service enclave |
| System owner | System owner |
| Operating environment | Virtualized and cloud-hosted service environment |

## System Description

The system provides controlled DNS and web services to DN42 participants. The architecture separates external DN42 transport, public service zones, private administrative networks, firewall policy enforcement, service workloads, and enclave-local patch distribution.

The public DN42 edge is `UNSC-DN42-EDGE02`. External WireGuard/eBGP peerings are operational. The registered IPv4 `/27` is reserved for native DN42 public services and required DN42-facing routing functions rather than general-purpose addressing for every enclave segment.

External DN42 traffic is carried toward protected enclaves over SD-WAN VPN 442 and terminates at `dn42-ext` firewall zones before service access is permitted. SD-WAN VPN 42 is the separate trusted intersite transport for NY, NJ, and MD. Trusted transport does not bypass firewall inspection where traffic crosses a security boundary.

## Addressing Architecture

| Resource | Function | Status |
|---|---|---|
| `172.23.105.192/27` | Native DN42 public services and required DN42-facing infrastructure | Registered |
| `172.23.105.200/29` | Maryland `dn42-public` service LAN | Planned |
| `172.23.105.208/29` | New Jersey `dn42-public` service LAN | Planned |
| Private IPv4 | Administrative, client, and internal-only networks | Internal |
| `fd16:2e38:95d2::/48` | DN42 IPv6 addressing; enclave implementation deferred | Registered |

The earlier assumption that each enclave infrastructure segment should receive native DN42 IPv4 was removed after review of DN42 allocation policy. Administrative/private systems use private IPv4 and may NAT when consuming DN42 services. Public DN42 service zones remain natively routed.

## Shared Infrastructure and Inherited Controls

In-boundary DNS, web, WSUS, and Greenbone workloads are hosted as virtual machines on shared virtualization infrastructure. The shared hypervisor, its management plane, and supporting storage are external supporting services rather than dedicated components of the authorization boundary. Logical network isolation, workload access controls, platform administration, backup, and recovery capabilities are treated as inherited protections for the hosted system components.

## Security Architecture

`UNSC-DN42-EDGE02` terminates external WireGuard peerings and performs DN42 route selection. The CHR does not provide unrestricted learned-route transit into protected homelab domains.

VPN 442 is the untrusted DN42 external-to-enclave transport. Cerberus and Chimera provide `dn42-ext` zones that terminate this path and enforce inspection and explicit policy before DN42 traffic reaches protected services.

MD and NJ maintain native DN42-public IPv4 zones for services intentionally reachable by the DN42 community. NY remains private/admin-only for IPv4 and uses NAT at its DN42-facing boundary when internal systems need to consume DN42 services.

VPN 42 provides trusted intersite connectivity among NY, NJ, and MD without requiring a hub site. Firewall enforcement remains in the path wherever traffic crosses a security boundary.

The DN42 routing domain is isolated from BlueLine, GreenLine, RedLine, management, household, and other non-DN42 routing domains.

## Authorized Services

- Authoritative DNS
- HTTPS web service
- Enclave-local Windows update distribution
- Vulnerability assessment and reporting

## Control Implementation Summary

| Security objective | Implementation approach |
|---|---|
| Boundary protection | `dn42-ext` firewall policy enforcement for VPN 442 ingress and egress |
| Least privilege | Explicit service and route-exchange policy; deny-by-default access |
| IPv4 conservation | Native DN42 IPv4 limited to public services and required DN42-facing routing functions |
| Private access | Private/admin networks use internal IPv4 and NAT only when consuming DN42 services |
| Network segmentation | Dedicated DN42 VRFs, zones, VPN roles, and restricted route exchange |
| Intersite transport | VPN 42 provides trusted NY/NJ/MD connectivity while retaining inspection at security boundaries |
| Flaw remediation | Enclave-local WSUS service distributes approved update content to Windows workloads |
| Vulnerability monitoring | Greenbone scanning service assesses authorized enclave components and records findings |
| Audit and accountability | Logging at defined routing and policy-enforcement points |

## Authorization Evidence

System authorization evidence consists of current architecture documentation, security requirements, routing and firewall validation records, service and update configuration validation, and documented risk disposition.
