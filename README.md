# Enterprise Hybrid Infrastructure & Next-Generation Firewall (NGFW) Engineering Lab

## Project Overview
This project documents the end-to-end architectural design, multi-vendor deployment, and advanced security engineering of a global hybrid enterprise network. Built entirely within a nested virtualization environment (EVE-NG), this lab simulates a realistic multi-site corporate footprint. It features a centralized corporate headquarters executing high-availability security boundaries, segmented campus LANs, dynamic multi-protocol WAN paths, secure provider-managed transit, and isolated international branch offices.

The core objective of this engineering project is to deploy, validate, and document advanced Next-Generation Firewall (NGFW) operations, identity-centric access control models, policy-based traffic engineering, and comprehensive threat mitigation fabrics designed to secure enterprise attack surfaces.

---

## Core Infrastructure & Topology Blueprint
The lab environment spans multiple distinct logical trust zones and physical geographic footprints:

* **Sydney Headquarters (HQ):** The central hub of corporate operations. Core perimeter defense and zone isolation are orchestrated via an Active/Passive High-Availability (HA) cluster of Palo Alto Networks NGFWs (`PaloAlto1` and `PaloAlto2`). The HQ contains a dedicated Active Directory DMZ segment, a legacy legacy campus block, and a high-density Layer 3 switching fabric.
* **Dallas Branch Office:** A remote international branch footprint secured by a standalone Palo Alto Networks NGFW, managing localized internal user segments and local switching topologies.
* **London Data Center:** A high-throughput remote data center node housing core application services, integrated via a dedicated Customer Edge (CE) routing perimeter.
* **New York Site:** A regional branch office executing localized switching and user edge operations.
* **Service Provider Core (WAN):** A multi-node simulated service provider infrastructure (ISP1/ISP2) executing advanced Provider-Edge (PE) transport systems, Multiprotocol Label Switching (MPLS), and core provider routing to bridge global enterprise sites.

---

## Technical Implementations & Engineering Disciplines

### 1. Next-Generation Firewall (NGFW) Core Security Operations
* **High Availability (HA) Clustering:** Engineered and synchronized an Active/Passive failover group between the Sydney HQ firewalls. Provisioned and validated dedicated HA1 (Control Link) and HA2 (Data Link) interfaces to achieve real-time session synchronization and sub-second stateful convergence during failover scenarios.
* **Advanced NAT Engineering:** Deployed dual-direction Network Address Translation mechanisms:
  * **Source NAT (SNAT):** Implemented Dynamic IP and Port (DIPP) translation to securely masquerade internal corporate subnets behind public-facing untrust interfaces for outbound internet transit.
  * **Destination NAT (DNAT):** Configured inbound port-forwarding policies allowing external entities to safely access internal destination targets (e.g., local application environments) while hiding actual backend IP structures.
* **Zone-Based Interface Architectures:** Designed a robust multi-zone security matrix mapping physical interfaces and logical 802.1Q sub-interfaces to explicit perimeters: `Inside`(LAN - Trust), `Outside`(WAN - Untrust), `DMZ`, and custom branch transit networks.

### 2. Identity Awareness & Access Control (User-ID & Visibility)
* **Directory Integration & Service Routing:** Customized internal Palo Alto Networks Service Route Configurations to shift operational directory traffic from default management ports onto active data interfaces (`ethernet1/2`). Established out-of-band communication paths targeting the internal Windows Server Active Directory Domain Services (AD DS) controller.
* **Identity Mapping Frameworks:** Deployed **LDAP** integration profiles to read and parse the structural hierarchy of Active Directory user objects and Organizational Units (OUs). Enabled **Kerberos** authentication profiles for secure ticket-based user validation.
* **User-ID (UID) Agent Deployment:** Provisioned and integrated a Palo Alto User-ID Agent to continuously audit Windows Server security logs. The agent maps dynamic IP-to-username allocations in real-time, allowing the firewall to enforce granular security policies based on human identity and group membership (e.g., `Marketing-Group` vs. `IT-Admin`) rather than volatile IP assignments.
* **Captive Portal Integration:** Implemented an internal Captive Portal framework to capture, authenticate, and map unmanaged endpoint traffic across corporate perimeters, ensuring complete user attribution across all sessions.

### 3. Traffic Engineering & Resiliency
* **Policy-Based Routing (PBR):** Configured Policy-Based Routing policies to bypass standard destination-based routing tables. Engineered intelligent traffic distribution models across dual upstream internet service providers (ISP1 and ISP2), routing business-critical production applications over premium links while offloading non-critical web traffic to secondary circuits.
* **Quality of Service (QoS):** Developed end-to-end QoS profiles and class-based bandwidth allocations on egress interfaces. Implemented strict prioritization and rate-limiting rules to guarantee predictable bandwidth for critical data streams (e.g., VoIP, corporate database replication) during periods of link saturation.

### 4. Advanced Threat Prevention & Edge Defense
* **App-ID & Content-ID Deep Inspection:** Migrated legacy port-based rules into deterministic Next-Generation security policies utilizing App-ID to inspect traffic layers natively, ensuring protocols like SSH or HTTP cannot be spoofed on non-standard ports. Enforced Content-ID profiles to block known malware variants and command-and-control (C2) signatures.
* **SSL Decryption (Forward Proxy):** Implemented an SSL Decryption strategy using a custom enterprise Certificate Authority (CA) certificate. Engineered Forward Proxy decryption rules to decrypt, inspect, and re-encrypt outbound HTTPS sessions, eliminating blind spots within encrypted traffic paths to block hidden malware payloads.
* **Infrastructure Edge Protection:** Provisioned edge-defense mechanisms to protect corporate infrastructure:
  * **DDoS Protection:** Configured Zone Protection Profiles and DoS Protection rules leveraging packet-rate thresholds (SYN, UDP, and ICMP flood protections) to mitigate volumetric flooding attacks at the untrust edge.
  * **Vulnerability Protection & WildFire:** Attached threat profiles to block active server-side and client-side vulnerability exploits. Integrated Palo Alto Networks **WildFire** profiles to send zero-day suspicious files to the cloud sandbox for dynamic analysis and automated signature generation.

### 5. Multi-Protocol Network Infrastructure & Core Routing
* **Layer 3 Switch Virtual Interfaces (SVIs):** Built a high-density switching fabric within the core infrastructure (`Sw` Switch). Configured internal Switch Virtual Interfaces (SVIs) for localized segments (VLANs 50, 60, 70, 80), activating the switch's hardware-based processing engine via the `ip routing` command to terminate Layer 2 boundaries and perform native local inter-VLAN routing.
* **Router-on-a-Stick (802.1Q Sub-Interfaces):** Implemented legacy campus trunks on access blocks (`Building1`), utilizing IEEE 802.1Q tagged frame encapsulation across physical links to transport distinct VLAN signals up to centralized sub-interfaces on the security gateway.
* **Multi-Protocol Dynamic Routing Framework:**
  * **OSPF (Open Shortest Path First):** Engineered multi-area OSPF adjacencies across Layer 3 switch platforms and Customer Edge (CE) routers to dynamically advertise local SVI networks and maintain network reachability tables.
  * **RIP (Routing Information Protocol):** Deployed legacy distance-vector protocols within isolated test segments to handle edge-routing tasks.
  * **Route Redistribution:** Configured two-way route redistribution boundaries on transition routers, leveraging route maps to safely inject OSPF paths into RIP domains (and vice versa) without introducing routing loops.
  * **MPLS VPN Transport Core:** Modeled a multi-node service provider transport backbone executing Multiprotocol Label Switching (MPLS) and Provider/Provider-Edge (P/PE) routing metrics to preserve client routing isolation across shared wide-area fabrics.
  * **Secure Site-to-Site & Remote Access VPNs:** Configured IPsec Site-to-Site VPN tunnels to bind distinct branch topologies (e.g., Dallas Branch) over untrusted public WAN paths. 

---

## Technical Toolset & Engineering Compendium
* **Security Platforms:** Palo Alto Networks Next-Generation Firewalls (PAN-OS Architecture).
* **Network Infrastructure:** Cisco Systems Enterprise Routing & Multilayer Switching Imagery.
* **Directory & Management Systems:** Windows Server Active Directory Domain Services (AD DS), DNS, LDAP, Kerberos Integration.
* **Emulation & Lab Environments:** EVE-NG (Emulated Virtual Environment Next Generation), QEMU Virtualization, Advanced Wireshark Packet Analysis.




Topology
<img width="2504" height="1273" alt="image" src="https://github.com/user-attachments/assets/cde3b734-4a7d-473b-9e70-586d2b8cc474" />







App ID Filtering - Blocking Facebook


https://github.com/user-attachments/assets/750f094e-35a4-4abe-a9cc-660c3391f278



Configuring captive portal for inbound web traffic by redirecting the traffic to specific login screen


https://github.com/user-attachments/assets/d104c004-ec18-437d-801c-d0933711d320

