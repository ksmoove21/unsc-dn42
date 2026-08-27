# 2026-08-26 DN42 Route Flap Incident

## Executive Summary

On 2026-08-26, external DN42 operators reported severe instability for the POWOW95 prefix `172.23.105.192/27`. Independent observers measured hundreds of route changes per second, including approximately 345 changes/s in one network and 473 changes/s in Kioubit's network.

The investigation initially focused on BGP session instability because historical logs on `C8000V-MD01` showed repeated session resets toward iEdon. That was a valid indicator, but it was not sufficient to explain the complete event.

The decisive finding was on `UNSC-DN42-EDGE02`, the MikroTik CHR public edge. The CHR originated `172.23.105.192/27` using a static route whose next hop was `172.23.105.221`, the internal address of `C8000V-NJ01`. Packet captures of the CHR's actual BGP advertisements showed that this internal forwarding next hop was also being exported toward public DN42 peers. The public prefix therefore was not cleanly anchored to the CHR itself.

The corrective action separated route origination from downstream forwarding. A dedicated blackhole route was added as a stable aggregate-origin anchor, public peer sessions were configured to advertise the CHR itself as NEXT_HOP where appropriate, Extended Next Hop was enabled for IPv4 NLRI over IPv6-link-local BGP sessions, and the no-longer-required firewall export of the /27 toward `dn42-ext` was disabled.

Post-change validation from Kioubit and RoutedBits showed stable route import, zero observed withdrawals on the corrected Kioubit session, and the expected CHR next-hop values.

## Device Identity

This incident involved multiple C8000V routers. They are identified by hostname throughout this document because the production environment contains four C8000V instances.

| Device | Role in this incident |
|---|---|
| `UNSC-DN42-EDGE02` | MikroTik CHR in Vultr New Jersey; authoritative public DN42 edge for `AS4242421995` |
| `C8000V-NJ01` | New Jersey SD-WAN router; internal CHR handoff at `172.23.105.221` |
| `C8000V-MD01` | Original home/Maryland C8000V that previously terminated the GRE/BGP peering toward iEdon |
| Cerberus | Palo Alto firewall and `dn42-ext` security boundary |

`C8000V-MD01` and `C8000V-NJ01` are different routers and must not be described interchangeably. Generic descriptions such as "the C8000V" or "legacy C8000V" are intentionally avoided because they are ambiguous in this topology.

## Impact

The affected prefix was:

```text
172.23.105.192/27
Origin: AS4242421995
```

Observed external symptoms included:

- heavy route flapping for the /27;
- approximately 345 route changes/s reported by one DN42 operator;
- approximately 473 route changes/s observed in Kioubit's network;
- large numbers of alternate paths visible at external looking glasses;
- operator escalation specifically asking that the `AS4242421995 <-> AS4242420207` relationship be checked.

The event was a control-plane instability problem. It was not evidence that AS4242421995 leaked the entire DN42 routing table.

## Architecture Before the Fix

The CHR had an active static route:

```text
172.23.105.192/27 -> 172.23.105.221@dn42
```

where `172.23.105.221` is `C8000V-NJ01`.

The same /27 was also learned back from `C8000V-NJ01` through internal eBGP. RouterOS used the active static route to satisfy `output.network=DN42-OWN` and originate the /27 externally.

The resulting control-plane dependency was effectively:

```text
AS4242421995 public prefix
        |
UNSC-DN42-EDGE02
        |
static /27 route
        |
172.23.105.221
        |
C8000V-NJ01
        |
SD-WAN / enclave routing
```

This coupled two separate functions:

1. proving that the CHR owns and should originate the aggregate; and
2. forwarding traffic from the CHR toward internal services.

Those functions should not have depended on the same static route.

## Key Evidence

### 1. RoutedBits session itself was not the original flap

The CHR log showed the RoutedBits BGP session establishing at `2026-08-26 00:53:23` and remaining established until the operator's later configuration changes around 22:00. This ruled out a continuously bouncing `AS4242421995 <-> AS4242420207` TCP/BGP session as the original mechanism.

This distinction was important: a BGP session can remain Established while individual NLRI are repeatedly changed or withdrawn.

### 2. CHR advertisement exposed `C8000V-NJ01` as NEXT_HOP

A saved-advertisement packet capture toward RoutedBits showed:

```text
NLRI:     172.23.105.192/27
NEXT_HOP: 172.23.105.221
AS_PATH:  4242421995 4242421995
```

The prefix was originated by the CHR, but the exported NEXT_HOP pointed past the CHR toward `C8000V-NJ01`.

### 3. Historical `C8000V-MD01` instability was real, but not enough by itself

`C8000V-MD01`, the original home router that previously peered with iEdon over GRE, had historical BGP logs showing:

- Connection Collision Resolution events;
- Hold Timer expiration;
- peer closures and re-establishment;
- a Router ID change event.

These were legitimate fault indicators and explained why the investigation initially focused on that adjacency. They are retained as a contributing historical condition, not as the sole proven cause of the 2026-08-26 event.

### 4. External validation after remediation

After correcting NEXT_HOP handling, the RoutedBits advertisement showed:

```text
NEXT_HOP: fe80::8123
AS_PATH:  4242421995 4242421995
```

Kioubit's corrected session negotiated Extended Next Hop and showed:

```text
Routes imported:   1
Import updates:    1
Import withdraws:  0
```

The Kioubit advertisement from the CHR showed:

```text
NEXT_HOP: fe80::ade1
AS_PATH:  4242421995 4242421995 4242421995
```

iEdon's direct IPv4 session showed:

```text
NEXT_HOP: 172.23.105.219
AS_PATH:  4242421995 4242421995 4242421995 4242421995
```

These captures confirmed both stable origination and the intended outbound traffic-engineering hierarchy.

## Investigation Path and Indicators

The investigation changed direction several times as evidence eliminated possible causes. That sequence is important because the incident was not solved by a single `show` command.

| Question / hypothesis | Indicator examined | What the indicator meant | Disposition |
|---|---|---|---|
| Was RoutedBits itself flapping? | CHR BGP session history | RoutedBits stayed Established through the original incident window | Not the initiating session failure |
| Was `C8000V-MD01` unstable toward iEdon? | IOS-XE BGP logs | Collision Resolution, Hold Timer expiration, peer closures, Router ID change | Confirmed instability, but insufficient as sole RCA |
| Was AS4242421995 leaking the full DN42 table? | Outbound prefix filters and peer advertisements | Only the owned `/27` was permitted outbound | Ruled out |
| Were AS_PATH prepends causing the fault? | Saved advertisements and external looking glasses | Prepend counts matched intended TE policy | Ruled out as root cause |
| Was the CHR origin dependent on another router? | `/routing/route print detail` | Active `/27` static pointed at `172.23.105.221` on `C8000V-NJ01` | Confirmed design defect |
| What NEXT_HOP was actually exported? | RouterOS saved-advertisement PCAP | RoutedBits received `NEXT_HOP=172.23.105.221` | Decisive evidence |
| Did IPv6-link-local BGP have the required capability? | Kioubit session capabilities and PCAP | `0.0.0.0` appeared until Extended Next Hop was enabled correctly | Remediation issue identified |
| Did the fix work externally? | Kioubit and RoutedBits looking glasses / peer telemetry | Correct CHR NEXT_HOP; Kioubit showed one import update and zero withdrawals | Validated |

### Why the external path count was misleading

RoutedBits showed dozens of available paths to the `/27`. That looked severe, but many entries were RoutedBits' own internally imported representations of valid paths. The high path count was an indicator of topology and propagation, not proof that AS4242421995 was originating dozens of routes or dozens of direct advertisements.

The useful evidence was the directly learned path and its attributes.

## Root Cause

The primary design defect was improper coupling of public-prefix origination and internal forwarding on `UNSC-DN42-EDGE02`.

`172.23.105.192/27` was being originated because an active static route existed toward `C8000V-NJ01`. RouterOS then selected that internal forwarding address as the public BGP NEXT_HOP on at least some external advertisements. The public edge was therefore originating the aggregate while pointing peers beyond itself toward an internal SD-WAN router.

This was corrected by separating the aggregate-origin anchor from the forwarding path and explicitly controlling external NEXT_HOP behavior.

Historical BGP instability on `C8000V-MD01` toward iEdon was a valid contributing signal and complicated the investigation, but it should not be conflated with `C8000V-NJ01` or treated as the sole root cause.

## Corrective Actions

### Stable aggregate origin

A dedicated blackhole route was added in the `dn42` routing table:

```text
172.23.105.192/27
blackhole
distance 254
comment: DN42 aggregate origin anchor
```

The existing forwarding route was temporarily retained during remediation so traffic would not be blackholed while forwarding design was being separated from origination.

### Correct public NEXT_HOP

For IPv6-link-local public BGP sessions that negotiated Extended Next Hop, RouterOS `nexthop-choice=force-self` was used so the CHR advertises itself as the forwarding next hop.

Validated examples:

| Peer | Expected AS_PATH | Validated NEXT_HOP |
|---|---|---|
| Headscarf175 | `4242421995` | CHR link-local `fe80:842::2:55eb` |
| RoutedBits | `4242421995 4242421995` | CHR link-local `fe80::8123` |
| Kioubit | `4242421995 4242421995 4242421995` | CHR link-local `fe80::ade1` |
| iEdon | four copies of `4242421995` | CHR IPv4 `172.23.105.219` |

Kioubit required Extended Next Hop to be enabled before IPv4 NLRI could be correctly advertised using the IPv6 link-local BGP session.

### Remove unnecessary return advertisement

The Palo Alto export rule advertising `172.23.105.192/27` toward `dn42-ext` was disabled because that route advertisement was no longer required for the retired public-DN42 peering function on `C8000V-MD01`.

## Remediation Sequence

The repair was intentionally staged to preserve forwarding while separating control-plane functions:

1. Disable the Palo Alto `/27` export toward `dn42-ext` that was no longer required for `C8000V-MD01`'s former public-DN42 peering function.
2. Preserve the active `/27 -> C8000V-NJ01` forwarding route temporarily.
3. Add a high-distance blackhole route as an independent aggregate-origin anchor.
4. Inspect saved BGP advertisements rather than relying only on the local RIB.
5. Configure CHR NEXT_HOP self behavior for the IPv6-link-local peers that support Extended Next Hop.
6. Enable and validate Extended Next Hop on the Kioubit session after a `0.0.0.0` NEXT_HOP exposed the capability mismatch.
7. Confirm iEdon's IPv4 session already advertised the correct local CHR next hop.
8. Validate all four peer advertisements by packet capture.
9. Validate externally through Kioubit, RoutedBits, and iEdon looking glasses / peer telemetry.
10. Stop making changes and observe session stability and withdrawal counters.

## Why We Did Not Catch This During Initial Design

The original design review focused on several individually valid objectives:

- originate the registered /27 from `AS4242421995`;
- keep internal/private ASNs out of the public DN42 AS_PATH;
- provide a forwarding route from the CHR toward enclave services;
- use `output.network` with a matching route in the `dn42` RIB;
- make the CHR the preferred public edge.

The mistake was treating those checks independently instead of validating the complete exported UPDATE.

The static route satisfied RouterOS's requirement for `output.network`, and forwarding toward `C8000V-NJ01` worked. Local routing therefore looked correct. What was not validated during implementation was the actual BGP UPDATE sent to every public peer, especially the NEXT_HOP attribute.

The missing acceptance test was simple:

```text
For every originated public prefix and every peer:
  verify NLRI
  verify AS_PATH
  verify NEXT_HOP
  verify communities
  verify external looking-glass result
```

Once saved-advertisement PCAPs were inspected, the defect became immediately visible.

This is the central engineering lesson from the event: a design can be locally coherent and still be wrong from the neighboring autonomous system's perspective. Validation must cross the AS boundary.

## Investigation Lessons

### Indicator: many paths at a looking glass

A high path count does not automatically mean the origin is leaking many routes. RoutedBits showed dozens of paths because its network imported multiple valid external paths into its internal routing system. The useful question was not "How many paths exist?" but "What changed repeatedly, and what attributes does the directly learned path contain?"

### Indicator: BGP session uptime

A stable session does not prove stable route advertisements. RoutedBits remained Established during the original incident window, which forced the investigation away from simple session flapping and toward NLRI and path-attribute behavior.

### Indicator: AS_PATH prepending

Prepending was not the root cause. It changed path preference as intended. External looking glasses later showed the hierarchy working, including paths that preferred Headscarf or Kioubit rather than the heavily prepended direct iEdon path.

### Indicator: `0.0.0.0` NEXT_HOP

During remediation, Kioubit briefly received `0.0.0.0` as NEXT_HOP when `force-self` was applied before Extended Next Hop was correctly negotiated. That directly demonstrated the importance of RFC 8950 capability negotiation for IPv4 NLRI carried over an IPv6-link-local BGP session.

## Permanent Engineering Controls

1. Public DN42 prefix origination must use a stable local aggregate anchor independent of downstream router availability.
2. Every external BGP peer must have a peer-specific export policy with an explicit final reject.
3. Every public prefix must be validated from saved advertisements or packet capture before a peer is considered production-ready.
4. External looking glasses must be used to validate the route as other autonomous systems actually see it.
5. BGP NEXT_HOP is an acceptance criterion, not an implementation detail.
6. IPv4 NLRI over IPv6 BGP sessions requires validated Extended Next Hop capability negotiation.
7. Device names must be used in incident documentation. Generic labels such as "the C8000V" are insufficient in a topology containing multiple C8000V routers.

## References

- [RFC 4271: A Border Gateway Protocol 4 (BGP-4)](https://www.rfc-editor.org/rfc/rfc4271.html)
- [RFC 8950: Advertising IPv4 NLRI with an IPv6 Next Hop](https://www.rfc-editor.org/rfc/rfc8950.html)
- [MikroTik RouterOS BGP documentation](https://help.mikrotik.com/docs/spaces/ROS/pages/331612228/routing%2Bbgp)
- [DN42 MikroTik documentation](https://wiki.dn42.us/howto/mikrotik)

## Resume-Level Takeaway

This incident required correlating operator-reported route-change rates, RouterOS RIB state, BGP session history, peer capability negotiation, saved BGP UPDATE packet captures, SD-WAN route exchange, firewall route export, and multiple external looking glasses.

The most important lesson was that a route can be locally valid and forwarding can appear functional while the exported BGP attributes are still architecturally wrong. The final fix came from validating the route from the perspective of the neighboring autonomous system, not only from the local RIB.
