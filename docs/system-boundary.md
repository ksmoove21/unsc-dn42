# System Boundary

## System Name

UNSC Delta November 42 Service Enclave

## System Purpose

The system provides controlled DN42-reachable services and a deliberately segmented DN42 transport design. It supports external eBGP peering, protected route carriage, firewall inspection, native public-service addressing, and private administrative access.

## Authorization Boundary

The current operational boundary includes `UNSC-DN42-EDGE02`, its separated Internet, `dn42`, and management routing domains, three established WireGuard/eBGP peers, the NJ C8000V VPN 442 handoff, OMP carriage to Maryland, both ISR4331-to-Cerberus eBGP handoffs, and VPN 42 OMP propagation back to New Jersey. It also includes the registered POWOW95 DN42 resources and the firewall policy required to normalize routing attributes at the deliberate VPN 442-to-VPN 42 service boundary.

The remaining target boundary includes the MD and NJ native DN42-public service zones, NY private/admin access through Chimera, NAT for private DN42 consumers, and the associated service-level firewall policy and logging functions.

The public Internet underlay used for WireGuard is a supporting transport, not part of the DN42 routing plane. External transit peers terminate at the CHR boundary and do not receive access to non-DN42 homelab routing domains.

## Addressing Boundary

| Resource | Role | Status |
|---|---|---|
| `172.23.105.192/27` | Public DN42 services and required DN42-facing infrastructure | Registered |
| `172.23.105.200/29` | MD `dn42-public` service zone | Planned |
| `172.23.105.208/29` | NJ `dn42-public` service zone | Planned |
| Private IPv4 | Administrative, client, and internal-only networks | Internal |
| `fd16:2e38:95d2::/48` | DN42 IPv6 allocation; enclave implementation deferred | Registered |

The previous separate `/26` service-network concept is not part of the current target architecture. The existing `/27` is sufficient after aligning the design with DN42 IPv4 allocation policy.

## External Interfaces and Interconnections

| Interface | Purpose | Trust role |
|---|---|---|
| Vultr shared `DMZ` firewall group | Upstream filtering for the CHR Internet interface | External inherited control |
| CHR Internet interface | WireGuard outer transport to external peers | External underlay |
| CHR `dn42` VRF | Decrypted peer traffic and eBGP control plane | DN42 routing domain |
| SD-WAN VPN `442` | DN42 external-to-enclave route carriage | Untrusted |
| Cerberus and Chimera `dn42-ext` zones | DN42 ingress termination, inspection, and policy enforcement | Boundary enforcement |
| SD-WAN VPN `42` | Direct NY, NJ, and MD intersite routing | Trusted intersite |
| MD and NJ `dn42-public` zones | Native DN42 IPv4 public-service delivery | Public service boundary |
| NY private/admin zone | Private IPv4 with NAT for DN42 consumption | Internal/private |
| BlueLine SD-WAN VPN `300` | CHR administrative access path | Management |

## Internal Segmentation

DN42 is isolated from BlueLine, GreenLine, RedLine, management, household, and other non-DN42 domains. Entry to a protected service from DN42 requires explicit route exchange plus firewall authorization through a `dn42-ext` boundary.

Native DN42 IPv4 is not assigned to administrative or client segments solely because they participate in the enclave. Those networks use private IPv4 and may be translated when consuming DN42 services.

VPN 42 provides trusted intersite transport among NY, NJ, and MD but does not remove firewall inspection requirements where traffic crosses a security boundary.

## Maryland Firewall Handoff

Maryland is the deliberate exchange point between the external DN42 transport in VPN 442 and trusted enclave transport in VPN 42:

```text
ISR4331 VPN 442 -> eBGP -> Cerberus -> eBGP -> ISR4331 VPN 42
```

Cerberus uses one virtual router for both handoff interfaces. The VPN 442-facing interface is in `dn42-ext`; the VPN 42-facing interface uses a separate internal DN42 security zone. This keeps the routing table shared while forcing traffic through explicit interzone inspection. No direct SD-WAN route leak between VPN 442 and VPN 42 is authorized.
