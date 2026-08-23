# Addressing Plan

## Allocation Strategy

The enclave allocation is divided into a dedicated infrastructure transit pool and routed service segments. Point-to-point infrastructure handoffs use /31 addressing. Service and administrative segments use /29 addressing.

## Segment Plan

| Segment | Prefix length | Purpose |
|---|---:|---|
| DN42-TRANSIT | /28 parent allocation | Infrastructure point-to-point address pool |
| DN42-EXT | /31 | Cerberus to DN42 edge-router handoff |
| DN42-WAN-HQ | /31 | Cerberus to headquarters SD-WAN handoff |
| DN42-ADMIN-HQ | /29 | Headquarters administrative systems |
| DN42-PUBLIC-HQ | /29 | Headquarters DN42-consumable services |
| DN42-PUBLIC-NJ | /29 | New Jersey cloud service segment |
| DN42-ADMIN-CYBERTRON | /29 | Cybertron administrative systems |
| DN42-PUBLIC-CYBERTRON | /29 | Cybertron DN42-consumable services |
| Reserved | /29 | Future enclave expansion |

## Addressing Principles

1. Infrastructure point-to-point links use /31 prefixes.
2. Each service segment is associated with one defined DN42 zone and routing domain.
3. Nexus provides the default gateway for headquarters service segments.
4. The Cybertron firewall provides the default gateway for Cybertron service segments.
5. The external DN42 edge advertises the approved aggregate; component prefixes remain internal routing constructs.
