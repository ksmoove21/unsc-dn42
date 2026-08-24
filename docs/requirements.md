# Security Requirements

## Information Types

The system is authorized to process, store, and transmit public and lab-operational information in support of DNS and web-service delivery. The system is not authorized to process, store, or transmit controlled information, personally identifiable information, credentials, production configurations, or management-plane data.

## Boundary Protection

1. DN42-originated traffic shall traverse an explicit firewall policy-enforcement point before reaching an authorized service.
2. SD-WAN VPN `442` shall be treated as the untrusted DN42 external-to-enclave transport.
3. DN42 traffic received through VPN `442` shall terminate in a `dn42-ext` firewall zone before entering a protected enclave.
4. Cerberus and Chimera shall enforce inspection and explicit policy for their respective `dn42-ext` boundaries.
5. Routing reachability shall not constitute authorization to access a service or routing domain.
6. Traffic from DN42 shall not be permitted to initiate sessions to MagmaNet, BlueLine, GreenLine, RedLine, household, management, or other non-DN42 homelab routing domains.
7. Administrative access to the CHR shall use the separated management path through NY102 and BlueLine SD-WAN VPN `300`, not the DN42 client transport.
8. A dual-homed update service shall not route traffic between the DN42 administrative domain and its external update-egress domain.

## Intersite Transport

1. SD-WAN VPN `42` shall provide trusted routed communication among NY, NJ, and MD without requiring a hub site.
2. Trusted intersite transport shall not bypass firewall inspection where traffic crosses a security boundary.
3. VPN `42` and VPN `442` shall remain logically distinct because they serve different trust and routing functions.

## Service Exposure

| Service | Function | Authorized exposure |
|---|---|---|
| Authoritative DNS | Publishes enclave service records | TCP/UDP 53; recursive resolution is not provided |
| Web service | Provides enclave landing-page and technical documentation | TCP 443; HTTP redirect may be used where required |

## Routing and Addressing

1. External route advertisement shall be limited to prefixes currently registered to `POWOW95-MNT`.
2. `172.23.105.192/27` shall be treated as the registered IPv4 transit and infrastructure prefix.
3. `172.23.46.0/26` shall not be treated as an active registered service prefix until DN42 registry PR #7253 is merged.
4. Internal routing exchange shall be limited to approved DN42 transit, service, and documented remote prefixes.
5. Default routes and non-DN42 routing-domain prefixes shall not be introduced into the DN42 routing domain.
6. Transit addressing and service addressing shall remain separated by function.
