# Public Edge Configuration

| Metadata | Value |
|---|---|
| **Device** | `UNSC-DN42-EDGE02` |
| **Platform** | MikroTik CHR, RouterOS v7 |
| **Location** | Vultr New Jersey |
| **Last verified** | 2026-08-26 |
| **Role** | Authoritative public DN42 edge and protected-enclave ingress |

## Routing Identity

| Item | Current value |
|---|---|
| Public ASN | `AS4242421995` |
| BGP router ID | `172.23.105.222` |
| Registered IPv4 | `172.23.105.192/27` |
| Registered IPv6 | `fd16:2e38:95d2::/48` |
| DN42 routing domain | RouterOS VRF `dn42` |
| SD-WAN handoff | `172.23.105.220/31` to `C8000V-NJ01` at `172.23.105.221` |

## External Transit

The edge maintains four established WireGuard/eBGP sessions. Each transit peer terminates on a separate WireGuard interface inside the `dn42` VRF.

| Peer | ASN | Transport | Inbound LOCAL_PREF | IPv4 outbound path policy |
|---|---:|---|---:|---|
| Headscarf175 | `4242420842` | WireGuard + eBGP | 400 | shortest path |
| RoutedBits | `4242420207` | WireGuard + eBGP | 300 | two copies of `4242421995` |
| Kioubit | `4242423914` | WireGuard + eBGP | 200 | three copies of `4242421995` |
| iEdon | `4242422189` | WireGuard + eBGP | 100 | four copies of `4242421995` |

The CHR also peers internally with `C8000V-NJ01` using private enclave ASN `4200102442`. That adjacency carries DN42 reachability toward SD-WAN VPN 442. `C8000V-NJ01` is an internal transport/handoff router, not the authoritative public origin for the POWOW95 aggregate.

## Device Identity Note

The Maryland C8000Vs have separate histories:

- `C8000V-MD02` was the original home public DN42 edge and terminated the GRE/BGP relationship toward iEdon.
- `C8000V-MD01` was the former Maryland SD-WAN edge and was replaced in that role by `c4331-md01` (ISR4331).
- `C8000V-NJ01` is the current New Jersey SD-WAN handoff behind the CHR.

Exact hostnames are used whenever a specific C8000V is referenced.

## Route Origination

The CHR originates `172.23.105.192/27` as `AS4242421995`.

Following the 2026-08-26 route-flap incident, the aggregate has a dedicated blackhole origin anchor in the `dn42` table so BGP origination is not dependent on the internal forwarding route toward `C8000V-NJ01`.

Conceptual anchor:

```text
172.23.105.192/27
blackhole
distance 254
```

This route exists to stabilize aggregate origination. Actual forwarding toward enclave services is a separate routing function.

## NEXT_HOP Policy

Public NEXT_HOP behavior is explicitly validated per peer.

For IPv4 NLRI carried over IPv6-link-local BGP sessions, Extended Next Hop is required and the CHR uses itself as NEXT_HOP where appropriate.

Validated IPv4 advertisements:

| Peer | NEXT_HOP |
|---|---|
| Headscarf175 | `fe80:842::2:55eb` |
| RoutedBits | `fe80::8123` |
| Kioubit | `fe80::ade1` |
| iEdon | `172.23.105.219` |

Saved-advertisement packet captures are used as the acceptance test for NLRI, AS_PATH, NEXT_HOP, and peer-specific path policy.

## Route Policy

1. External inbound policy accepts approved DN42 routing information and sets peer-specific LOCAL_PREF.
2. External outbound policy permits only prefixes registered to POWOW95 and ends with an explicit reject.
3. The CHR originates `172.23.105.192/27` locally rather than exposing enclave-private ASNs in the public AS path.
4. AS_PATH prepending influences inbound traffic preference but does not replace stable route origination or correct NEXT_HOP handling.
5. Learned external routes are carried inward through VPN 442 but are not intentionally re-exported to the public DN42 mesh through the enclave return path.

## Routing-Domain Separation

| Domain | Interfaces | Purpose |
|---|---|---|
| `main` | Internet underlay | Vultr connectivity used by WireGuard outer transport |
| `dn42` | Four WireGuard interfaces and the `C8000V-NJ01` transit | Decrypted DN42 traffic and BGP control plane |
| `management` | Dedicated management interface | Administrative access, monitoring, time, and logging |

The Internet underlay does not carry decrypted DN42 routes. Management services use the separated management routing domain. Detailed internal management addresses, monitoring destinations, community values, and authentication material are maintained only in the private homelab repository.

## Monitoring and Administration

The CHR uses centrally provided NTP, remote syslog, SNMP traps, SSH, and WinBox through the management VRF. Upstream Internet filtering is provided by the shared Vultr `DMZ` firewall group, with RouterOS input policy providing the device-local enforcement point.

Operational verification includes:

- BGP session uptime and reset history;
- WireGuard handshake state;
- route counts;
- saved outbound BGP advertisements;
- external looking-glass validation;
- route-change and withdrawal observations from peers when available.

## IPv6 Status

Provider IPv6 and tunnel link-local addressing are present at the public edge. The registered `fd16:2e38:95d2::/48` is retained, but enclave IPv6 carriage remains deferred while the active service design is IPv4-first.

## Verification Snapshot

On 2026-08-26, all four public peerings were established after remediation. Saved BGP advertisements confirmed CHR-owned NEXT_HOP values for the public /27. Kioubit externally reported one imported route update and zero withdrawals on the corrected session, and RoutedBits externally showed the direct `AS4242421995` path using CHR link-local next-hop `fe80::8123`.

## Related Documentation

- [Network Architecture](network-architecture.md)
- [2026-08-26 DN42 Route Flap Incident](incidents/2026-08-26-route-flap-postmortem.md)
- [Addressing Plan](addressing-plan.md)
- [System Boundary](system-boundary.md)
- [Data Flow Architecture](data-flows.md)
