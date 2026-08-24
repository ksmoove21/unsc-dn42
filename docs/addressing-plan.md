# Addressing Plan

## Registry Resources

| Resource | Value | Use | Status |
|---|---|---|---|
| DN42 ASN | `AS4242421995` | Public route origin | Registered |
| IPv4 transit prefix | `172.23.105.192/27` | Transit, peer-facing, and infrastructure addressing | Registered |
| IPv4 service prefix | `172.23.46.0/26` | DN42-facing service enclave addressing | Pending DN42 registry PR #7253 |
| IPv6 aggregate | `fd16:2e38:95d2::/48` | DN42 IPv6 addressing and controlled external origination | Registered |

The attempted expansion of `172.23.105.192/27` to `172.23.105.192/26` was reverted because `172.23.105.224/27` was allocated to another DN42 participant immediately before the expansion merged. The transit prefix therefore remains `172.23.105.192/27`.

## Addressing Roles

The IPv4 design intentionally separates infrastructure from services.

### Transit network

`172.23.105.192/27` is reserved for DN42 routing infrastructure, peer-facing addressing, and routed handoffs. It is not used as the general service-address pool.

### Service network

`172.23.46.0/26` is the dedicated DN42 service-network request. It is not treated as registered or externally originated until DN42 registry PR #7253 is merged.

### IPv6

`fd16:2e38:95d2::/48` remains the registered DN42 IPv6 allocation.

## Addressing Principles

1. Transit and service addressing remain separate so routing infrastructure does not consume service-space assignments.
2. Only prefixes registered to `POWOW95-MNT` may be originated externally.
3. Point-to-point and peer-facing addresses are drawn from the registered transit network.
4. Service workloads use the dedicated service prefix after registry approval.
5. Internal component prefixes are not automatically leaked into other homelab routing domains.
