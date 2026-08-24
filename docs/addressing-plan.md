# Addressing Plan

## Registered Aggregates

| Resource | Value | Use |
|---|---|---|
| DN42 ASN | AS4242421995 | Public route origin |
| IPv4 aggregate | 172.23.105.192/26 | DN42 addressing and controlled external origination |
| IPv6 aggregate | fd16:2e38:95d2::/48 | DN42 addressing and controlled external origination |

## Current and Planned Infrastructure

| Prefix or address | Role | Status |
|---|---|---|
| 172.23.105.224/29 | DN42 public-service and WireGuard tunnel-address pool | Available for peer and service use |
| 172.23.105.248/31 | UNSC-DN42-EDGE02 to C8000V-NJ01 DN42 transit | Planned |
| 172.23.105.248 | CHR transit address | Planned |
| 172.23.105.249 | C8000V-NJ01 transit address | Planned |
| 172.23.105.250/31, .252/31, .254/31 | Reserved point-to-point infrastructure links | Reserved |
| fd16:2e38:95d2::/48 | IPv6 aggregate | Operational registration |

## Addressing Principles

1. The CHR advertises only the IPv4 /26 and IPv6 /48 aggregates externally.
2. /31 prefixes are reserved for routed infrastructure point-to-point links.
3. Peer tunnel addresses and service addressing use explicit routes over the CHR transit, never automatic route leaking.
4. Component prefixes are internal constructs. External advertisement remains limited to the registered aggregates.
