# System Security Plan

## System Identification

| Field | Value |
|---|---|
| System name | UNSC Delta November 42 Service Enclave |
| System type | DN42-connected network service enclave |
| System owner | System owner |
| Operating environment | Virtualized and cloud-hosted service environment |

## System Description

The system provides controlled DNS and web services to DN42 participants. The architecture separates external DN42 transport, routed service domains, firewall policy enforcement, service workloads, and enclave-local patch distribution.

The public DN42 edge is `UNSC-DN42-EDGE02`, a MikroTik CHR in Vultr New Jersey. Its three external WireGuard/eBGP peerings are operational. The registered IPv4 `/27` is not yet originated because the CHR-to-C8000V SD-WAN transit and matching downstream route are not installed.

In the target architecture, external DN42 traffic is carried toward protected enclaves over SD-WAN VPN 442 and terminates at `dn42-ext` firewall zones before service access is permitted. SD-WAN VPN 42 is the separate trusted intersite transport intended for direct NY, NJ, and MD communication. Trusted transport does not bypass firewall inspection where traffic crosses a security boundary.

## Addressing Architecture

| Prefix | Function | Status |
|---|---|---|
| `172.23.105.192/27` | DN42 transit and routing infrastructure | Registered |
| `172.23.46.0/26` | DN42 service-enclave addressing | Pending DN42 registry PR #7253 |
| `fd16:2e38:95d2::/48` | DN42 IPv6 addressing; enclave implementation deferred | Registered |

The attempted expansion of `172.23.105.192/27` to `/26` was reverted and is not part of the current architecture.

## Shared Infrastructure and Inherited Controls

In-boundary DNS, web, WSUS, and Greenbone workloads are hosted as virtual machines on shared virtualization infrastructure. The shared hypervisor, its management plane, and supporting storage are external supporting services rather than dedicated components of the authorization boundary. Logical network isolation, workload access controls, platform administration, backup, and recovery capabilities are treated as inherited protections for the hosted system components.

## Security Architecture

The CHR retains the shared Vultr `DMZ` firewall group as its upstream Internet-interface filter by operator decision.

`UNSC-DN42-EDGE02` terminates external WireGuard peerings and performs DN42 route selection. The CHR does not provide unrestricted learned-route transit into protected homelab domains.

VPN 442 is the untrusted DN42 external-to-enclave transport. Cerberus and Chimera provide `dn42-ext` zones that terminate this path and enforce inspection and explicit policy before DN42 traffic reaches protected services.

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
| Network segmentation | Dedicated DN42 VRFs, zones, VPN roles, and restricted route exchange |
| Intersite transport | VPN 42 provides trusted NY/NJ/MD connectivity while retaining inspection at security boundaries |
| Flaw remediation | Enclave-local WSUS service distributes approved update content to Windows workloads |
| Vulnerability monitoring | Greenbone scanning service assesses authorized enclave components and records findings |
| Secure communications | Authenticated encryption for protected enclave transport where required |
| Audit and accountability | Logging at defined routing and policy-enforcement points |

## Authorization Evidence

System authorization evidence consists of current architecture documentation, security requirements, routing and firewall validation records, service and update configuration validation, and documented risk disposition.
