# Data Flow Architecture

## Current implementation status

External WireGuard/eBGP peerings on `UNSC-DN42-EDGE02` are operational. The CHR receives DN42 routes from Headscarf175, Kioubit, and iEdon. The CHR-to-C8000V handoff, VPN 442 enclave path, VPN 42 enclave use, service-prefix origination, and IPv6 enclave carriage remain planned.

## Flow DF-01: External DN42 Transit to Protected Enclave

**Status:** Planned, not yet operational end to end.

| Field | Definition |
|---|---|
| Source | Any DN42 participant |
| Destination | Authorized DN42 service in a protected enclave |
| Purpose | Reach an approved DN42-published service |
| Direction | DN42 participant initiates; return traffic is statefully permitted |
| Enforcement | CHR route policy, SD-WAN VPN 442, `dn42-ext` firewall policy, and destination service policy |

### Path

```mermaid
flowchart TB
  A["DN42 participant and transit"] --> B["CHR and NJ VPN 442"]
  B --> C["OMP to MD VPN 442"]
  C --> D["Cerberus firewall handoff"]
  D --> E["MD VPN 42 and OMP"]
  E --> F["Authorized DN42 service"]
```

The detailed Maryland handoff is `ISR4331 VPN 442 -> eBGP -> Cerberus -> eBGP -> ISR4431 VPN 42`. Cerberus uses one virtual router but separate external and internal DN42 security zones. VPN 442 is the untrusted DN42 external-to-enclave transport. External DN42 traffic does not enter VPN 42 or a protected enclave without traversing Cerberus inspection. This is a firewall-mediated exchange, not a direct SD-WAN route leak.

## Flow DF-02: Enclave-Originated DN42 Traffic

**Status:** Planned, pending the CHR-to-C8000V transit and VPN 442 service path.

| Field | Definition |
|---|---|
| Source | Authorized DN42 enclave workload |
| Destination | DN42 destination |
| Purpose | DN42 service consumption or controlled operational traffic |
| Direction | Enclave workload initiates; return traffic is statefully permitted |
| Route selection | Headscarf175, Kioubit, then iEdon local-preference order on the CHR |

### Path

```mermaid
flowchart TB
  W["Authorized enclave workload"] --> A["VPN 42 and OMP to MD"]
  A --> F["Cerberus firewall handoff"]
  F --> V["VPN 442 and OMP to NJ"]
  V --> E["UNSC-DN42-EDGE02"]
  E --> D["Selected DN42 transit peer"]
```

The ordered transit peers use observed CHR underlay latency as a static preference input. The CHR is not used as unrestricted learned-route transit.

## Flow DF-03: Trusted Intersite DN42 Communication

**Status:** Planned for DN42 enclave use.

| Field | Definition |
|---|---|
| Source | Authorized NY, NJ, or MD enclave workload |
| Destination | Authorized workload at another approved site |
| Purpose | Direct intersite DN42 enclave communication |
| Transport | SD-WAN VPN 42 |
| Trust classification | Trusted intersite transport |
| Enforcement | Firewall inspection where a security boundary is crossed |

### Path

```mermaid
flowchart LR
  A["NY / NJ / MD enclave"] --> F1["Local security boundary"]
  F1 --> V["SD-WAN VPN 42"]
  V --> F2["Remote security boundary"]
  F2 --> B["Remote enclave service"]
```

VPN 42 prevents the intersite design from depending on one location as a hub. Trusted transport does not remove inspection requirements at security boundaries.

## Flow DF-04: Administrative Access

Administrative access to the CHR uses the separated management path through NY102 and BlueLine SD-WAN VPN 300. The management interface is not used as the DN42 transport path or as a general default route for the `dn42` VRF.
