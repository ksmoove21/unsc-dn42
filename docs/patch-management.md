# Patch Management Architecture

## Purpose

The enclave uses a local Windows Server Update Services (WSUS) instance to distribute approved Microsoft update content to Windows workloads within the authorization boundary.

## Architecture

The WSUS service stores approved update files locally. Enclave Windows workloads obtain update metadata and content from the local WSUS service and do not require direct Internet access for update installation.

The WSUS service is the only enclave workload authorized to initiate outbound update synchronization and approved-content download traffic to Microsoft Update services.

## Boundary Controls

- Outbound update traffic is limited to the WSUS service identity.
- All other enclave workloads are denied direct Internet egress by default.
- WSUS traffic is subject to boundary-policy enforcement, source NAT where required, and logging.
- The update service is administered through the designated administrative access path.
- No production-network interconnection is required for the patch-management capability.

## Security Control Implementation

| Control | Implementation statement |
|---|---|
| SC-7, Boundary Protection | Egress from the enclave is limited to the WSUS service for its authorized update function. |
| SI-2, Flaw Remediation | Approved update content is obtained by WSUS and distributed to enclave Windows workloads. |
| CM-6, Configuration Settings | WSUS products, classifications, languages, and synchronization behavior are limited to authorized requirements. |
| AU-2, Event Logging | Boundary and update-service events are logged to support review and troubleshooting. |

## Validation Evidence

1. Verify that an enclave Windows workload reaches the local WSUS service.
2. Verify that the WSUS service downloads approved content after approval.
3. Verify that the workload installs an approved update without direct Internet egress.
4. Verify that non-WSUS enclave workloads are denied direct Internet egress.
5. Review firewall and WSUS logs for the approved update flow.
