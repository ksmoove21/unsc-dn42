# UNSC Delta November 42

## System Security Documentation

UNSC Delta November 42 is a DN42-connected service enclave operated to provide controlled network services and to exercise secure interconnection, routing, boundary protection, and service-delivery practices.

The public DN42 edge is `UNSC-DN42-EDGE02`, a MikroTik CHR in Vultr New Jersey. Its three WireGuard/eBGP transit sessions are established. `172.23.105.192/27` is the registered POWOW95 transit network, but owned-prefix origination is intentionally deferred until the CHR-to-C8000V SD-WAN transit exists. A separate `172.23.46.0/26` service-network allocation is pending DN42 registry PR #7253.

DN42 external-to-enclave traffic will use SD-WAN VPN `442` and terminate at `dn42-ext` firewall policy boundaries. SD-WAN VPN `42` is the separate trusted intersite transport planned for NY, NJ, and MD. Both paths remain subject to inspection where they cross a security boundary. The initial enclave implementation is IPv4-focused; IPv6 enclave routing is deferred.

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
