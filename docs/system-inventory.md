# System Inventory

## Component Inventory

| Component | Hosting model | Routing domain | Status | Function |
|---|---|---|---|---|
| `UNSC-DN42-EDGE02` | MikroTik CHR in Vultr, New Jersey | `dn42` VRF | Operational edge | Authoritative public WireGuard/eBGP DN42 edge, route selection, peering identity, and registered-prefix origination |
| `C8000V-NJ01` | Cisco Catalyst 8000V, New Jersey | CHR-to-SD-WAN handoff | Operational | Internal DN42 handoff between `UNSC-DN42-EDGE02` and SD-WAN VPN 442 at `172.23.105.221`; not the authoritative public DN42 origin |
| `C8000V-MD02` | Cisco Catalyst 8000V, Maryland/home | Historical public DN42 edge | Public DN42 role retired | Original home DN42 edge that terminated the GRE/BGP peering toward iEdon and the direct Cerberus DN42 handoff |
| `C8000V-MD01` | Cisco Catalyst 8000V, Maryland/home | Former Catalyst SD-WAN edge | Decommissioned from SD-WAN role | Former Maryland SD-WAN router; separate device from `C8000V-MD02` |
| `c4331-md01` | Cisco ISR4331, Maryland/home | Catalyst SD-WAN site 12 | Operational | Current Maryland SD-WAN edge carrying DN42 VPN 442 and VPN 42 routing contexts |
| Headscarf175 | External transit peer | WireGuard/eBGP | Operational | Full-table DN42 transit |
| RoutedBits | External transit peer | WireGuard/eBGP | Operational | Full-table DN42 transit |
| Kioubit | External transit peer | WireGuard/eBGP | Operational | Full-table DN42 transit |
| iEdon | External transit peer | WireGuard/eBGP | Operational | Full-table DN42 transit |
| SD-WAN VPN `442` | Catalyst SD-WAN transport | DN42 external transport | Operational in current path | Carries untrusted DN42 traffic toward protected enclaves |
| SD-WAN VPN `42` | Catalyst SD-WAN transport | Trusted intersite transport | Operational in verified MD/NJ path | Carries inspected DN42 routes between sites |
| Cerberus | Palo Alto PA-5220 | `dn42-ext` and protected DN42 domains | Policy boundary | DN42 ingress inspection, public-service routing, and controlled transfer between VPN 442 and VPN 42 |
| Chimera | Firewall boundary | `dn42-ext` and private DN42 domains | Policy boundary | NY DN42-facing transit, inspection, and NAT for private/admin systems |
| MD `dn42-public` | Protected public-service environment | Native DN42 IPv4 | Planned | Hosts approved MD DN42-facing services |
| NJ `dn42-public` | Protected public-service environment | Native DN42 IPv4 | Planned | Hosts approved NJ DN42-facing services |
| NY admin/private | Protected private environment | Private IPv4 | Planned / internal | Administrative and private workloads; DN42 consumption through NAT where required |

### C8000V naming rule

The production environment contains multiple C8000V routers. DN42 documentation identifies a C8000V by exact hostname whenever the specific device matters.

For the 2026-08-26 route-flap investigation:

- `C8000V-MD02` is the original home public DN42 edge that peered with iEdon over GRE.
- `C8000V-MD01` is a different router: the former Maryland SD-WAN edge, now replaced in that role by `c4331-md01` (ISR4331).
- `C8000V-NJ01` is the New Jersey SD-WAN handoff at `172.23.105.221` behind the CHR.

Generic descriptions such as "the C8000V" or "legacy C8000V" are avoided because they are ambiguous in this topology.

## Address Resources

| Resource | Status | Function |
|---|---|---|
| `172.23.105.192/27` | Registered | Native DN42 public services and required DN42-facing routing functions |
| `172.23.105.200/29` | Planned | Maryland `dn42-public` service LAN |
| `172.23.105.208/29` | Planned | New Jersey `dn42-public` service LAN |
| Private IPv4 | Internal | Administrative, client, and internal-only addressing |
| `fd16:2e38:95d2::/48` | Registered; enclave implementation deferred | DN42 IPv6 addressing |

The previous separate `/26` service-network concept is no longer part of the target architecture. The existing `/27` is retained and used selectively in accordance with DN42 IPv4 conservation guidance.

## Hosting Dependency

The CHR is hosted in Vultr and uses a licensed 1 Gbps RouterOS data plane. The Internet-facing WireGuard transport is separate from the `dn42` routing domain. The shared Vultr `DMZ` firewall group remains attached to the CHR as its upstream Internet-interface filter by operator decision.

VPN 442 and VPN 42 serve different purposes. VPN 442 carries untrusted external DN42 traffic to `dn42-ext` firewall boundaries. VPN 42 carries trusted intersite traffic among NY, NJ, and MD. Both remain subject to security policy where traffic crosses a firewall boundary.
