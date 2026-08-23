# System Boundary

## System Name

UNSC Delta November 42 Service Enclave

## System Purpose

The system provides controlled, DN42-reachable network services. The enclave supports service publication, inter-domain routing, protected transport, and security-policy enforcement.

## Authorization Boundary

The authorization boundary comprises the logical DN42 routing instances, associated interfaces and controlled handoffs, Nexus and firewall routing constructs supporting the DN42 enclave, boundary firewall policy and logging functions, approved service networks, and workloads providing authorized DN42 services.

DN42 peer tunnels and their underlying external transport terminate at the system boundary but are not components of the system.

## External Interfaces and Interconnections

DN42 connectivity terminates on the enclave edge-routing function. Traffic traverses Cerberus policy enforcement before entering a headquarters service routing domain or approved SD-WAN transport.

Headquarters DN42 administrative and public-service routing domains terminate at Nexus default gateways and exchange routes with Cerberus through separate eBGP handoffs. Cybertron hosts corresponding routing domains behind its local firewall.

## Internal Segmentation

The enclave comprises four defined security zones:

| Zone | Function |
|---|---|
| DN42-EXT | External DN42 edge connectivity |
| DN42-ADMIN | Administrative systems and service-management access |
| DN42-PUBLIC | DN42-consumable services |
| DN42-WAN | Authorized SD-WAN transport |

The DN42 enclave is logically isolated from MagmaNet, BlueLine, GreenLine, RedLine, household, management, and other homelab routing domains. Routes and traffic are exchanged only through explicitly authorized security and routing policy.
