# Requirements

## Mission

Provide a small, controlled DN42 service enclave for learning inter-domain routing, security architecture, service publication, and multi-site transport without extending trust to the broader homelab.

## Security requirements

1. DN42 clients must not initiate connections to MagmaNet, BlueLine, GreenLine, RedLine, household, or management networks.
2. All DN42 ingress must cross an explicit firewall policy-enforcement point before reaching an approved service.
3. No route leaking is permitted between DN42 and other routing domains unless documented and explicitly authorized.
4. Service administration is separate from DN42 client access.
5. DNS, web, and future services must expose only documented ports and protocols.
6. The initial system processes public or lab-operational information only. It does not authorize CUI, PII, credentials, production configurations, or management-plane data.

## Initial authorized services

| Service | Intended function | Exposure |
|---|---|---|
| Authoritative DNS | Publish service records for the enclave | TCP/UDP 53 only, no recursion |
| Web service | Landing page or technical documentation | TCP 443 only; HTTP redirects where used |

## Routing requirements

- The DN42 edge advertises only explicitly approved prefixes to external peers.
- The firewall exports only approved DN42 service prefixes toward the edge.
- Remote service prefixes are carried only across an explicitly scoped transport domain.
- No default route or non-DN42 color-network routes are introduced into the DN42 routing domain.
