# Addressing Plan

## Registry Resources

| Resource | Value | Use | Status |
|---|---|---|---|
| DN42 ASN | `AS4242421995` | Public route origin | Registered |
| IPv4 allocation | `172.23.105.192/27` | Public DN42 services and required DN42-facing infrastructure | Registered |
| IPv6 allocation | `fd16:2e38:95d2::/48` | DN42 IPv6 addressing | Registered |

The attempted expansion of `172.23.105.192/27` to `172.23.105.192/26` was reverted because the adjacent `/27` was allocated to another DN42 participant immediately before the expansion merged.

A later separate `/26` service-network request was based on an earlier design assumption that most enclave infrastructure should receive native DN42 IPv4. After review of the DN42 allocation policy, that assumption was removed from the target architecture. The existing `/27` is sufficient when DN42 IPv4 is concentrated on public services and required DN42-facing routing functions.

## Addressing Policy

DN42 IPv4 is treated as scarce public-like community address space rather than general-purpose enclave addressing.

Native DN42 IPv4 is assigned to:

- public DN42 service zones;
- peering identities;
- required DN42-facing routed handoffs.

Private IPv4 is used for:

- administrative networks;
- client networks;
- internal-only services;
- infrastructure that does not require direct DN42 reachability.

Private/admin systems may use NAT when they need to consume DN42 services. Public DN42 services remain natively routed.

Policy reference: https://www.dn42.dev/Policies

## IPv4 Allocation Table

| Prefix / address | Location or function | Purpose | State |
|---|---|---|---|
| `172.23.105.192/31` | MD ISR4331 ↔ Cerberus, VPN 442 | Untrusted DN42 transit handoff | Operational |
| `172.23.105.194/31` | MD Cerberus ↔ ISR4331, VPN 42 | Trusted return/intersite transit handoff | Operational |
| `172.23.105.196/31` | NY edge ↔ Chimera | DN42-facing transit for private/admin access and NAT | Planned |
| `172.23.105.198/31` | Cerberus ↔ Nexus | Routed handoff for the MD public-service zone | Planned |
| `172.23.105.200/29` | MD `dn42-public` | Native DN42 IPv4 public-service LAN | Planned |
| `172.23.105.208/29` | NJ `dn42-public` | Native DN42 IPv4 public-service LAN | Planned |
| `172.23.105.219/32` | CHR peering identity | iEdon WireGuard/eBGP address | Operational |
| `172.23.105.220/31` | CHR ↔ C8000V-NJ01 | Public-edge to SD-WAN DN42 transit | Operational |
| `172.23.105.222` | CHR BGP router ID | BGP identifier; not an interface or service-host allocation | Operational identifier |
| `172.23.105.216-.218` | Unassigned | Infrastructure reserve | Available |
| `172.23.105.223` | Unassigned | Infrastructure reserve | Available |

The routed/public allocation consumes 27 of the 32 IPv4 addresses when the two public `/29` service LANs, five `/31` transit links, and one `/32` peering identity are counted. The CHR router ID is tracked separately because it is a BGP identifier rather than an interface allocation.

## Location Model

| Location | Native DN42 public IPv4 | Private/admin IPv4 |
|---|---|---|
| Maryland | `172.23.105.200/29` public-service LAN | Private IPv4 for administrative and internal-only systems |
| New Jersey | `172.23.105.208/29` public-service LAN | Private IPv4 for administrative and internal-only systems |
| New York | No native DN42 public-service `/29` | Private/admin-only IPv4; DN42 access uses the Chimera-facing transit and NAT where required |

## IPv6

`fd16:2e38:95d2::/48` remains the registered DN42 IPv6 allocation. IPv6 carriage into the enclave is deferred while the initial implementation remains IPv4-focused.

## Addressing Principles

1. DN42 IPv4 is not assigned to a segment solely because that segment participates in the enclave.
2. Public DN42 service zones receive native DN42 IPv4.
3. Administrative and client networks use private IPv4 and may NAT when consuming DN42 services.
4. Required DN42-facing routed handoffs may use `/31` addressing from the registered `/27`.
5. New York remains private/admin-only for IPv4 and does not receive a native `dn42-public` `/29` in the current design.
6. Only prefixes registered to `POWOW95-MNT` may be originated externally.
7. Non-DN42 routing-domain prefixes are not implicitly leaked into the DN42 routing domain.
