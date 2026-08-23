# Security Requirements

## Information Types

The system is authorized to process, store, and transmit public and lab-operational information in support of DNS and web-service delivery. The system is not authorized to process, store, or transmit controlled information, personally identifiable information, credentials, production configurations, or management-plane data.

## Boundary Protection

1. DN42-originated traffic shall traverse an explicit firewall policy-enforcement point before reaching an authorized service.
2. Routing reachability shall not constitute authorization to access a service or routing domain.
3. Traffic from DN42 shall not be permitted to initiate sessions to MagmaNet, BlueLine, GreenLine, RedLine, household, management, or other homelab routing domains.
4. Administrative access shall use a management path separate from DN42 client access.

## Service Exposure

| Service | Function | Authorized exposure |
|---|---|---|
| Authoritative DNS | Publishes enclave service records | TCP/UDP 53; recursive resolution is not provided |
| Web service | Provides enclave landing-page and technical documentation | TCP 443; HTTP redirect may be used where required |

## Routing and Transport

1. External route advertisement shall be limited to explicitly approved service prefixes.
2. Internal routing exchange shall be limited to approved DN42 service prefixes and documented remote service prefixes.
3. Default routes and non-DN42 routing-domain prefixes shall not be introduced into the DN42 routing domain.
4. Enclave-to-enclave protected connectivity shall use authenticated encryption independent of DN42 transport.
