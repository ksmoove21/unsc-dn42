# System Inventory

## Component Inventory

| Component | Hosting model | Routing domain | Status | Function |
|---|---|---|---|---|
| UNSC-DN42-EDGE02 | MikroTik CHR in Vultr, New Jersey | dn42 VRF | Operational | Public WireGuard and eBGP DN42 edge, aggregate origination, and path selection |
| Headscarf175 | External transit peer | WireGuard/eBGP | Operational | Full-table DN42 transit |
| Kioubit | External transit peer | WireGuard/eBGP | Operational | Full-table DN42 transit |
| iEdon | External transit peer | WireGuard/eBGP | Operational | Full-table DN42 transit |
| C8000V-NJ01 | Virtual SD-WAN router | DN42 service VPN | Planned DN42 handoff | CHR eBGP neighbor and SD-WAN distribution point |
| C8000V-NY01 | Cybertron edge router | DN42 service VPN | Planned DN42 extension | NY enclave routing edge |
| ISR4331-MD01 | Cisco ISR | DN42 service VPN | Planned DN42 extension | MD enclave routing edge |
| Cerberus | Palo Alto PA-5220 | DN42 security domains | Policy boundary | Firewall inspection and controlled service access |

## Hosting Dependency

The CHR is hosted in Vultr and uses a licensed 1 Gbps RouterOS data plane. The Internet-facing WireGuard transport is separate from the dn42 routing domain. SD-WAN carries DN42 only after explicit eBGP and service-VPN policy are implemented.
