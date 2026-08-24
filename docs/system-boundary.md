# System Boundary

## System Name

UNSC Delta November 42 Service Enclave

## System Purpose

The system provides controlled DN42-reachable services and a deliberately segmented DN42 transport design. It supports external eBGP peering, protected route carriage, firewall inspection, and service delivery to approved enclaves.

## Authorization Boundary

The boundary includes `UNSC-DN42-EDGE02`, its `dn42` VRF and WireGuard peers, registered POWOW95 DN42 prefixes, SD-WAN VPN 442 DN42 transport, SD-WAN VPN 42 trusted intersite transport, enclave routing constructs, and the firewall policy and logging functions that control entry to protected services.

The public Internet underlay used for WireGuard is a supporting transport, not part of the DN42 routing plane. External transit peers terminate at the CHR boundary and do not receive access to non-DN42 homelab routing domains.

## Addressing Boundary

| Prefix | Role | Status |
|---|---|---|
| `172.23.105.192/27` | DN42 transit and routing infrastructure | Registered |
| `172.23.46.0/26` | DN42 service enclave | Pending DN42 registry PR #7253 |
| `fd16:2e38:95d2::/48` | DN42 IPv6 allocation | Registered |

The prior `172.23.105.192/26` expansion is not part of the authorization boundary because that registry change was reverted.

## External Interfaces and Interconnections

| Interface | Purpose | Trust role |
|---|---|---|
| CHR Internet interface | WireGuard outer transport to external peers | External underlay |
| CHR `dn42` VRF | Decrypted peer traffic and eBGP control plane | DN42 routing domain |
| SD-WAN VPN `442` | DN42 external-to-enclave route carriage | Untrusted |
| Cerberus and Chimera `dn42-ext` zones | DN42 ingress termination, inspection, and policy enforcement | Boundary enforcement |
| SD-WAN VPN `42` | Direct NY, NJ, and MD intersite routing | Trusted intersite |
| Enclave firewall boundaries | Policy enforcement for protected service access | Boundary enforcement |
| BlueLine SD-WAN VPN `300` | CHR administrative access path | Management |

## Internal Segmentation

DN42 is isolated from BlueLine, GreenLine, RedLine, management, household, and other non-DN42 domains. Entry to a protected enclave from DN42 requires VPN 442 route carriage plus explicit firewall authorization through a `dn42-ext` zone.

VPN 42 provides trusted intersite transport among NY, NJ, and MD but does not remove firewall inspection requirements where traffic crosses a security boundary.

There is no implicit route leaking between DN42 and other routing domains.
