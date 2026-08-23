# UNSC Delta November 42

A public, sanitized architecture record for a DN42 service enclave.

This repository documents the design logic, security boundary, routing policy, and validation approach for a DN42-connected lab environment. It is intentionally an engineering and assurance record, not a configuration repository.

## Initial operating capability

- Authoritative DNS service
- HTTPS web service
- DN42 is treated as untrusted external transport
- Access is permitted only to explicitly authorized services

## Public-documentation standard

This repository deliberately excludes credentials, private keys, tunnel endpoints, firewall configuration, management paths, complete topology, and exact addressing.

- IPv4 addresses retain only the first two octets: `172.23.x.x`
- ASNs redact their final two digits: `AS42424219xx`
- IPv6 prefixes are represented generically
- Full DN42-only FQDNs may be documented after they exist

See [the boundary statement](docs/system-boundary.md), [requirements](docs/requirements.md), and [redaction standard](docs/redaction-standard.md).
