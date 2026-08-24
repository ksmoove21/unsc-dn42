# Patch Management Architecture

## Purpose

The enclave uses a local Windows Server Update Services (WSUS) instance to distribute approved Microsoft update content to Windows workloads within the authorization boundary.

## Architecture

The WSUS service stores approved update files locally. Enclave Windows workloads obtain update metadata and content from the local WSUS service and do not require direct Internet access for update installation.

The WSUS service uses a dedicated update-egress interface connected to an approved external update-service domain. It is the only enclave workload authorized to initiate outbound update synchronization and approved-content download traffic to Microsoft Update services.

## Boundary Controls

- The DN42 administrative interface receives WSUS client and management traffic only.
- The update-egress interface is used for outbound Microsoft Update synchronization and content download only.
- IP forwarding is disabled on the WSUS server; the server is not a router between the DN42 administrative domain and the external update-service domain.
- WSUS listener services are bound only to the DN42 administrative interface.
- Inbound access on the update-egress interface is denied except for stateful return traffic.
- The external update-service domain applies approved malware analysis, traffic inspection, and egress policy.
- All other enclave workloads are denied direct Internet egress by default.

## Security Control Implementation

| Control | Implementation statement |
|---|---|
| SC-7, Boundary Protection | Egress is limited to the WSUS update function, and the dual-homed WSUS workload is configured to prevent routing between security domains. |
| SI-2, Flaw Remediation | Approved update content is obtained by WSUS and distributed to enclave Windows workloads. |
| CM-6, Configuration Settings | WSUS products, classifications, languages, interface bindings, and synchronization behavior are limited to authorized requirements. |
| AU-2, Event Logging | Update-egress, firewall, and WSUS events are logged to support review and troubleshooting. |

## Validation Evidence

1. Verify that an enclave Windows workload reaches the local WSUS service.
2. Verify that the WSUS service downloads approved content through its designated update-egress interface.
3. Verify that the workload installs an approved update without direct Internet egress.
4. Verify that the WSUS service does not forward traffic between its two connected security domains.
5. Verify that non-WSUS enclave workloads are denied direct Internet egress.
6. Review firewall and WSUS logs for the approved update flow.
