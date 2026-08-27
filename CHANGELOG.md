# Engineering Change Log

This log records material changes to the public DN42 architecture and the engineering reason behind them. Git commits remain the authoritative version history; this document provides the operational narrative that a diff alone cannot capture.

For material changes, commit messages should record both **what changed** and **why**, plus validation evidence when available.

## 2026-08-26 - Route-flap remediation and public-edge hardening

**Before**

- `UNSC-DN42-EDGE02` originated `172.23.105.192/27` while the matching active static route pointed at `C8000V-NJ01` (`172.23.105.221`).
- Saved advertisements showed that internal forwarding address being exported as BGP NEXT_HOP to public peers.
- The historical `C8000V-MD02` public-DN42 path still existed as a separate design path.

**Changed**

- Added an independent high-distance blackhole anchor for `172.23.105.192/27`.
- Separated aggregate origination from forwarding toward `C8000V-NJ01`.
- Corrected public NEXT_HOP behavior so external peers receive the CHR peering address.
- Enabled and validated RFC 8950 Extended Next Hop for IPv4 NLRI over IPv6-link-local sessions where required.
- Disabled the no-longer-required Palo Alto `/27` export associated with the former `C8000V-MD02` public-edge path.
- Standardized public DN42 eBGP on CHR only.

**Why**

External operators reported severe route churn for `172.23.105.192/27`. The investigation showed that public prefix origination was coupled to an internal forwarding route and that the wrong NEXT_HOP could be exported beyond the CHR.

**Validation**

- RoutedBits showed the corrected direct CHR path using next hop `fe80::8123`.
- Kioubit showed one imported route update and zero withdrawals on the corrected session.
- Saved RouterOS BGP advertisements validated NEXT_HOP and AS_PATH for all four peers.

Related: [2026-08-26 DN42 Route Flap Incident](docs/incidents/2026-08-26-route-flap-postmortem.md)

## 2026-08-26 - Router nomenclature correction

**Changed**

- `C8000V-MD02` is documented as the original home public DN42 edge and GRE/iEdon router.
- `C8000V-MD01` is documented as the former Maryland SD-WAN edge.
- `c4331-md01` is documented as the current Maryland ISR4331 SD-WAN edge.
- `C8000V-NJ01` remains the New Jersey SD-WAN handoff behind the CHR.

**Why**

Multiple C8000V routers exist in the production environment. Generic names created ambiguity during the incident review and led to an incorrect router identity in the first draft of the postmortem.

**Validation**

Cross-checked the original `C8000V-MD02` onboarding record and the private homelab Git history that established `c4331-md01` as the current Maryland SD-WAN edge.

## 2026-08-25 - Maryland VPN 442 / VPN 42 service insertion validated

**Changed**

- Confirmed that one physical ISR4331, `c4331-md01`, carries both Maryland DN42 service-VPN routing contexts.
- VPN 442 and VPN 42 remain separate VRFs but terminate on the same SD-WAN edge.
- Cerberus remains the explicit inspection and route-transfer boundary between them.

**Why**

The design needed a single, verified source of truth for the Maryland service insertion rather than treating the two routing contexts as separate physical routers.

## 2026-08-24 - CHR becomes the public DN42 edge

**Before**

`C8000V-MD02` was the original home public DN42 edge using GRE toward iEdon and a direct Cerberus handoff.

**Changed**

- Deployed `UNSC-DN42-EDGE02` in Vultr New Jersey.
- Moved public DN42 peering toward WireGuard/eBGP on MikroTik CHR.
- Established the CHR-to-`C8000V-NJ01` internal handoff for SD-WAN transport into the enclave.

**Why**

The CHR provided a simpler and more resource-efficient public routing platform while supporting WireGuard-based DN42 peering and a clean separation between public edge routing and internal SD-WAN transport.
