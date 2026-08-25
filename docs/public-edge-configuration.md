# Public Edge Configuration

| Metadata | Value |
|---|---|
| **Device** | `UNSC-DN42-EDGE02` |
| **Platform** | MikroTik CHR, RouterOS v7 |
| **Location** | Vultr New Jersey |
| **Last verified** | 2026-08-25 |
| **Role** | Public DN42 edge and protected-enclave ingress |

## Routing Identity

| Item | Current value |
|---|---|
| Public ASN | `AS4242421995` |
| BGP router ID | `172.23.105.222` |
| Registered IPv4 | `172.23.105.192/27` |
| Registered IPv6 | `fd16:2e38:95d2::/48` |
| DN42 routing domain | RouterOS VRF `dn42` |
| SD-WAN handoff | `172.23.105.220/31` to NJ C8000V at `172.23.105.221` |

## External Transit

The edge maintains three established WireGuard/eBGP sessions. Each transit peer terminates on a separate WireGuard interface inside the `dn42` VRF.

| Peer | ASN | Transport | Operational role |
|---|---:|---|---|
| Headscarf175 | `4242420842` | WireGuard and eBGP | Preferred external transit |
| Kioubit | `4242423914` | WireGuard and eBGP | Second external preference |
| iEdon | `4242422189` | WireGuard and eBGP | Third external preference |

The CHR also peers internally with the NJ C8000V using private enclave ASN `4200102442`. That adjacency imports the external DN42 table into SD-WAN VPN 442 and supplies forwarding reachability for POWOW95-owned services.

## Route Policy

1. External inbound policy accepts valid DN42 routing information from the approved transit peers.
2. External outbound policy permits only prefixes registered to POWOW95.
3. The CHR originates `172.23.105.192/27` locally rather than exposing enclave-private ASNs in the public AS path.
4. Headscarf175 is advertised the shortest public path. Kioubit and iEdon receive progressively prepended paths.
5. Learned external routes are carried inward through VPN 442 but are never exported back to the public mesh through the enclave return path.

## Routing-Domain Separation

| Domain | Interfaces | Purpose |
|---|---|---|
| `main` | Internet underlay | Vultr connectivity used by WireGuard outer transport |
| `dn42` | Three WireGuard interfaces and the C8000V transit | Decrypted DN42 traffic and BGP control plane |
| `management` | Dedicated management interface | Administrative access, monitoring, time, and logging |

The Internet underlay does not carry decrypted DN42 routes. Management services use the separated management routing domain. Detailed internal management addresses, monitoring destinations, community values, and authentication material are maintained only in the private homelab repository.

## Monitoring and Administration

The CHR uses centrally provided NTP, remote syslog, SNMP traps, SSH, and WinBox through the management VRF. Upstream Internet filtering is provided by the shared Vultr `DMZ` firewall group, with RouterOS input policy providing the device-local enforcement point.

## IPv6 Status

Provider IPv6 and tunnel link-local addressing are present at the public edge. The registered `fd16:2e38:95d2::/48` is retained, but enclave IPv6 carriage remains deferred while the active service design is IPv4-first.

## Verification Snapshot

On 2026-08-25, the CHR showed all three external peerings and the NJ C8000V handoff established. The external sessions supplied full DN42 routing views, and the internal adjacency carried the controlled route exchange required by the SD-WAN design.

## Related Documentation

- [Network Architecture](network-architecture.md)
- [Addressing Plan](addressing-plan.md)
- [System Boundary](system-boundary.md)
- [Data Flow Architecture](data-flows.md)
