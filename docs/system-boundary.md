# System Boundary

## System Name

UNSC Delta November 42 Service Enclave

## System Purpose

The system provides controlled DN42-reachable services and a deliberately segmented DN42 transport design. It supports external eBGP peering, protected route carriage, firewall inspection, and service delivery to approved enclaves.

## Authorization Boundary

The current operational boundary includes `UNSC-DN42-EDGE02`, its `dn42` VRF, three established WireGuard/eBGP peers, and registered POWOW95 DN42 resources. The target boundary also includes SD-WAN VPN 442 DN42 transport, SD-WAN VPN 42 trusted intersite transport, enclave routing constructs, and the firewall policy and logging functions that control entry to protected services. Those SD-WAN and enclave portions are not yet operational.

The public Internet underlay used for WireGuard is a supporting transport, not part of the DN42 routing plane. External transit peers terminate at the CHR boundary and do not receive access to non-DN42 homelab routing domains.

## Addressing Boundary

| Prefix | Role | Status |
|---|---|---|
| `172.23.105.192/27` | DN42 transit and routing infrastructure | Registered |
| `172.23.46.0/26` | DN42 service enclave | Pending DN42 registry PR #7253 |
| `fd16:2e38:95d2::/48` | DN42 IPv6 allocation; enclave implementation deferred | Registered |

The prior `172.23.105.192/26` expansion is not part of the authorization boundary because that registry change was reverted.

## External Interfaces and Interconnections

| Interface | Purpose | Trust role |
|---|---|---|
| Vultr shared `DMZ` firewall group | Upstream filtering for the CHR Internet interface; retained by operator decision | External inherited control |
| CHR Internet interface | WireGuard outer transport to external peers | External underlay |
| CHR `dn42` VRF | Decrypted peer traffic and eBGP control plane | DN42 routing domain |
| Planned SD-WAN VPN `442` | DN42 external-to-enclave route carriage; not yet operational | Untrusted |
| Cerberus and Chimera `dn42-ext` zones | DN42 ingress termination, inspection, and policy enforcement | Boundary enforcement |
| Planned SD-WAN VPN `42` use | Direct NY, NJ, and MD intersite routing for the enclave | Trusted intersite |
| Enclave firewall boundaries | Policy enforcement for protected service access | Boundary enforcement |
| BlueLine SD-WAN VPN `300` | CHR administrative access path | Management |

## Internal Segmentation

DN42 is isolated from BlueLine, GreenLine, RedLine, management, household, and other non-DN42 domains. Entry to a protected enclave from DN42 requires VPN 442 route carriage plus explicit firewall authorization through a `dn42-ext` zone.

VPN 42 provides trusted intersite transport among NY, NJ, and MD but does not remove firewall inspection requirements where traffic crosses a security boundary.

## Maryland Firewall Handoff

Maryland is the deliberate exchange point between the external DN42 transport in VPN 442 and trusted enclave transport in VPN 42:

```text
ISR4331 VPN 442 -> eBGP -> Cerberus -> eBGP -> ISR4431 VPN 42
```

Cerberus uses one virtual router for both handoff interfaces. The VPN 442-facing interface is in `dn42-ext`; the VPN 42-facing interface uses a separate internal DN42 security zone. This keeps the routing table shared while forcing traffic through explicit interzone inspection. No direct SD-WAN route leak between VPN 442 and VPN 42 is authorized.

The associated private ASNs are MD VPN 442 `AS4200012442`, Cerberus `AS4200001995`, and MD VPN 42 `AS4200012042`. The confirmed site IDs are MD `12`, NJ `102`, and NY `100`.

There is no implicit route leaking between DN42 and other routing domains. The registered IPv4 `/27` is not currently originated because its CHR-to-C8000V downstream transit and route are not yet installed.
