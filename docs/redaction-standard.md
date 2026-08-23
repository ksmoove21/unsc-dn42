# Public Redaction Standard

This repository is public. It documents decisions and technical patterns without publishing operational details that would make targeting the environment easier.

## Allowed

- Logical architecture and trust-boundary diagrams
- Vendor and platform families
- Service purpose, protocols, and high-level policy intent
- Redacted route and ASN examples
- DN42-only FQDNs after the names are operational

## Redacted or excluded

- Credentials, private keys, certificate material, tokens, and recovery data
- Public IP addresses, tunnel endpoints, and provider-facing peer details
- Firewall rules, NAT rules, management interfaces, and remote-access paths
- Exact internal topology and full routing tables
- Exact IPv4 host and subnet addressing: retain only the first two octets
- Exact ASN identity: redact the final two digits
- Exact IPv6 allocations and host addresses unless specifically approved for publication

## Examples

| Operational value | Public form |
|---|---|
| IPv4 host or subnet | `172.23.x.x/26` |
| ASN | `AS42424219xx` |
| IPv6 allocation | `fdxx:xxxx:xxxx::/48` |
| DN42 service name | Full FQDN permitted once operational |
