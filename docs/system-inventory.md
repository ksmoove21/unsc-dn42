# System Inventory

## Component Inventory

| Component | Hosting model | Routing domain | Function |
|---|---|---|---|
| DN42 edge router | Virtual network appliance | DN42-EXT | External DN42 peering and approved route advertisement |
| Cerberus | Physical security appliance | DN42-EXT, DN42-WAN | Routing-policy and firewall enforcement |
| Nexus switching layer | Physical network infrastructure | DN42-ADMIN-HQ, DN42-PUBLIC-HQ | Headquarters VRF gateways and route exchange |
| Windows DNS server | Virtual machine | DN42-PUBLIC-HQ | Authoritative DNS |
| Ubuntu HestiaCP server | Virtual machine | DN42-PUBLIC-HQ | HTTPS web service |
| WSUS server | Virtual machine | DN42-ADMIN-HQ | Enclave-local Windows update distribution |
| Greenbone server | Virtual machine | DN42-ADMIN-HQ | Vulnerability assessment and reporting |

## Hosting Dependency

Virtual-machine workloads are hosted on shared virtualization infrastructure. The hypervisor, its management plane, and supporting storage are external supporting services. Workload isolation is achieved through defined virtual networking, DN42 routing domains, firewall policy, and workload-level access controls.
