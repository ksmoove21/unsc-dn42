# System Boundary

## System Name

UNSC Delta November 42 Service Enclave

## System Purpose

The system provides controlled DN42-reachable services and a deliberately segmented DN42 transport design. It supports external eBGP peering, protected route carriage, firewall inspection, and service delivery to approved enclaves.

## Authorization Boundary

The boundary includes UNSC-DN42-EDGE02, its dn42 VRF and WireGuard peers, approved DN42 aggregates, the C8000V-NJ01 DN42 handoff when implemented, DN42 service-VPN route exchange, enclave routing constructs, and firewall policy/logging functions that control entry to protected services.

The public Internet underlay used for WireGuard is a supporting transport, not part of the DN42 routing plane. External transit peers terminate at the CHR boundary. They do not receive access to non-DN42 homelab routing domains.

## External Interfaces and Interconnections

| Interface | Purpose | Status |
|---|---|---|
| CHR Internet interface | WireGuard outer transport to external peers | Operational |
| CHR dn42 VRF | Decrypted peer traffic and eBGP control plane | Operational |
| CHR to C8000V-NJ01 172.23.105.248/31 | DN42 eBGP transit and SD-WAN handoff | Planned |
| SD-WAN service VPN | Restricted DN42 route carriage to approved enclaves | Planned |
| Enclave firewall boundaries | Policy enforcement for NY, MD, and cloud services | Required for service access |

## Internal Segmentation

DN42 is isolated from BlueLine, GreenLine, RedLine, management, household, and other non-DN42 domains. Entry to a protected enclave requires explicit route exchange and firewall authorization. There is no implicit route leaking between DN42 and other routing domains.
