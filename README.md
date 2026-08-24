# UNSC Delta November 42

## System Security Documentation

UNSC Delta November 42 is a DN42-connected service enclave operated to provide controlled network services and to exercise secure interconnection, routing, boundary protection, and service-delivery practices.

The public DN42 edge is `UNSC-DN42-EDGE02`, a MikroTik CHR in Vultr New Jersey. `172.23.105.192/27` is the registered POWOW95 transit network. A separate `172.23.46.0/26` service-network allocation is pending DN42 registry PR #7253.

DN42 external-to-enclave traffic uses SD-WAN VPN `442` and terminates at `dn42-ext` firewall policy boundaries. SD-WAN VPN `42` is a separate trusted intersite transport for NY, NJ, and MD. Both paths remain subject to inspection where they cross a security boundary.

This repository contains the system-level documentation supporting the enclave's security architecture, requirements, and operational authorization evidence.

## Documentation Set

- [System Security Plan](docs/system-security-plan.md)
- [System Boundary](docs/system-boundary.md)
- [Network Architecture](docs/network-architecture.md)
- [Addressing Plan](docs/addressing-plan.md)
- [Data Flow Architecture](docs/data-flows.md)
- [Security Requirements](docs/requirements.md)
- [Patch Management Architecture](docs/patch-management.md)

## References

- [NIST SP 800-37 Rev. 2](https://csrc.nist.gov/pubs/sp/800-37/r2/final)
- [NIST SP 800-53 Rev. 5.2](https://csrc.nist.gov/pubs/sp/800-53/r5/upd1/final)
