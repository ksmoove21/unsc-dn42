# UNSC Delta November 42

## System Security Documentation

UNSC Delta November 42 is a DN42-connected service enclave operated to provide controlled network services and to exercise secure interconnection, routing, boundary protection, and service-delivery practices.

The public DN42 edge is `UNSC-DN42-EDGE02`. `172.23.105.192/27` remains the registered POWOW95 IPv4 allocation and is now used selectively for public DN42 services, peering identities, and required DN42-facing transit links. Administrative, client, and internal-only networks use private IPv4 and may use NAT when consuming DN42 services.

Maryland and New Jersey each retain a native `dn42-public` `/29` for services intentionally reachable from DN42. New York remains private/admin-only for IPv4 and uses a DN42-facing transit for NAT and controlled DN42 access.

DN42 external-to-enclave traffic uses SD-WAN VPN `442` and terminates at `dn42-ext` firewall policy boundaries. SD-WAN VPN `42` is the separate trusted intersite transport for NY, NJ, and MD. Both paths remain subject to inspection where they cross a security boundary.

This repository contains the system-level documentation supporting the enclave's security architecture, requirements, addressing plan, and operational authorization evidence.

## Documentation Set

- [System Security Plan](docs/system-security-plan.md)
- [System Boundary](docs/system-boundary.md)
- [Network Architecture](docs/network-architecture.md)
- [Addressing Plan](docs/addressing-plan.md)
- [Data Flow Architecture](docs/data-flows.md)
- [Security Requirements](docs/requirements.md)
- [Patch Management Architecture](docs/patch-management.md)

## References

- [DN42 Resource Allocation Policy](https://www.dn42.dev/Policies)
- [DN42 Address Space](https://dn42.dev/Address-Space)
- [NIST SP 800-37 Rev. 2](https://csrc.nist.gov/pubs/sp/800-37/r2/final)
- [NIST SP 800-53 Rev. 5.2](https://csrc.nist.gov/pubs/sp/800-53/r5/upd1/final)
