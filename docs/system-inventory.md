# System Inventory

## Component Inventory

| Component | Hosting model | Routing domain | Status | Function |
|---|---|---|---|---|
| `UNSC-DN42-EDGE02` | MikroTik CHR in Vultr, New Jersey | `dn42` VRF | Operational | Public WireGuard and eBGP DN42 edge, registered-prefix origination, and path selection |
| Headscarf175 | External transit peer | WireGuard/eBGP | Operational | Full-table DN42 transit |
| Kioubit | External transit peer | WireGuard/eBGP | Operational | Full-table DN42 transit |
| iEdon | External transit peer | WireGuard/eBGP | Operational | Full-table DN42 transit |
| SD-WAN VPN `442` | Catalyst SD-WAN transport | DN42 external transport | Defined architecture | Carries untrusted DN42 traffic toward protected enclaves |
| SD-WAN VPN `42` | Catalyst SD-WAN transport | Trusted intersite transport | Defined architecture | Direct NY, NJ, and MD routing without hub dependence |
| Cerberus | Palo Alto PA-5220 | `dn42-ext` and protected DN42 domains | Policy boundary | DN42 ingress inspection and controlled service access |
| Chimera | Firewall boundary | `dn42-ext` and protected DN42 domains | Policy boundary | DN42 ingress inspection and controlled service access |
| NY, NJ, and MD DN42 enclaves | Protected service environments | DN42 service routing domains | Protected enclave | Hosts approved DN42-facing services behind firewall policy |

## Address Resources

| Resource | Status | Function |
|---|---|---|
| `172.23.105.192/27` | Registered | Transit, peer-facing, and infrastructure addressing |
| `172.23.46.0/26` | Pending DN42 registry PR #7253 | Dedicated service-enclave addressing |
| `fd16:2e38:95d2::/48` | Registered | DN42 IPv6 addressing |

## Hosting Dependency

The CHR is hosted in Vultr and uses a licensed 1 Gbps RouterOS data plane. The Internet-facing WireGuard transport is separate from the `dn42` routing domain.

VPN 442 and VPN 42 serve different purposes. VPN 442 carries untrusted external DN42 traffic to `dn42-ext` firewall boundaries. VPN 42 carries trusted intersite traffic among NY, NJ, and MD. Both remain subject to security policy where traffic crosses a firewall boundary.
