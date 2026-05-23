# Mid-Size-Business-Enterprise-Network
Full multi-branch corporate enterprise network in Cisco Packet Tracer — multi-area OSPF, redundant routed-access design, ASA firewall with DMZ, NAT, complete server room (DHCP/DNS/FTP/NTP/Syslog), simulated WAN, and a central IoT system with automation.



Enterprise Network Design & Implementation (Cisco Packet Tracer)


A full-scale, multi-site enterprise network built from the ground up in Cisco Packet Tracer. The project covers the entire stack of a modern business network: a multi-area OSPF routed core, redundant distribution layer, routed-access offices, a hardened internet edge with an ASA firewall and DMZ, a complete server room of network services, and a centrally-managed IoT system with automation.

This was built device-by-device with a focus on why each design choice was made, not just how — emphasizing real-world best practices in routing, redundancy, security, and segmentation.

Topology Overview
                         INTERNET (simulated)
                    Router13 — Router5 — Router2
                                 |
                         [ ASA 5506-X Firewall ]   <-- NAT, DMZ, ACLs, ASBR
                                 |
                          [ Core Router ]          <-- OSPF Area 0 backbone
              ___________________|___________________
             |              |              |          |
          MLS1 ===== MLS2 (L3 EtherChannel)      Server_Router
        (Distribution, dual ABRs, Area 0/2)       (ABR, Area 0/1)
             |              |                          |
     ________|______________|________            [ Server Room ]
    |     |     |     |     |        |          DHCP, DNS, FTP, NTP,
  Office1 ... Office5 (routed access)           Syslog, HTTPS/DMZ,
  data / voice VLANs, dual uplinks              IoT Registration Server,
  webcams via Wi-Fi, IP phones                  CME (telephony)

Key Technologies & Features
Routing & Core

Multi-area OSPFv2 design with three areas:

Area 0 (backbone): Core, Firewall, distribution switches, Server_Router
Area 1: server room
Area 2: the five branch offices


ABR/ASBR roles: distribution switches and the Server_Router act as Area Border Routers; the firewall acts as an ASBR injecting a default route into OSPF (default-information originate).
auto-cost reference-bandwidth tuned consistently across all devices for accurate path selection.
Point-to-point OSPF network type on all /30 router links to eliminate unnecessary DR/BDR elections.

Distribution & Access (Layer 2/3)

Routed-access design: each office multilayer switch performs its own inter-VLAN routing via local SVIs; uplinks are Layer 3 routed ports running OSPF.
Redundancy through OSPF over dual uplinks from each office to both distribution switches (no single point of failure).
Layer 3 EtherChannel (LACP) between the two distribution switches.

Per-office VLAN segmentation: separate data and voice VLANs, with a structured IP addressing scheme.

Security

Cisco ASA 5506-X firewall at the internet edge using the security-level model (inside 100 / outside 0).
NAT/PAT so internal users share a single public IP for outbound internet access.
DMZ policy: only the internal HTTPS server is reachable from the internet, and only on TCP 443 — enforced with static NAT + an explicit inbound ACL. All other inbound traffic is denied.
Stateful inspection plus explicit ACLs for return traffic control.
Hardened devices: SSH-only management (no Telnet), login local, service password-encryption, enable secret, exec-timeouts, MOTD banners, and disabled/shutdown unused ports.
Port security with sticky MAC addresses on sensitive access ports.
Login logging (login on-success/on-failure log) for an audit trail.

Server Room Services

DHCP with relay (ip helper-address) serving every office subnet, including DHCP Option 150 for IP phones.
DNS, FTP (used for centralized config backups), NTP (network-wide time sync), and Syslog (centralized logging) servers.
HTTPS server in a dedicated DMZ VLAN, published to the internet through the firewall.

WAN / Internet Edge

Simulated multi-hop internet (Router2 → Router5 → Router13) using static routing, demonstrating the correct boundary between an internal IGP (OSPF) and the external static-routed edge.
Correct return-path routing for the public IP block so inbound DMZ and outbound NAT traffic both complete.

IoT (with automation)

Central IoT Registration Server to which all IoT devices register across the routed network.
Wireless IoT: office webcams and server-room sensors connect via Wi-Fi (WPA2-PSK/AES) through access points into the appropriate VLANs.
Automation conditions:

If smoke is detected → trigger the siren.
If temperature exceeds threshold → turn on the air conditioner.



Telephony (Cisco Unified Communications Manager Express)

CME configured on a dedicated voice router: telephony-service, an extension dial plan for all office phones, DHCP Option 150, and voice VLANs.
Note: phone registration is subject to a known Cisco Packet Tracer limitation with the 7960 SCCP cnf-file handshake. The configuration is production-correct and would register on physical hardware or Cisco CML.


Design Decisions Worth Noting

Routed access over a traditional trunk + first-hop redundancy — chosen to push Layer 3 to the access edge, shrink failure domains, and rely on OSPF for sub-second-style reconvergence over dual uplinks.
Firewall as the OSPF ASBR — rather than statically pointing the whole network at the edge, the firewall advertises the default route into OSPF, so every device dynamically learns the path to the internet.
DMZ as a controlled internal subnet — the single public service (HTTPS) is exposed via static NAT + a tightly-scoped ACL, keeping the rest of the network invisible from the outside.
Consistent OSPF network types — all point-to-point links explicitly typed to avoid DR election issues and speed adjacency formation.


Addressing & Segmentation (high level)
SegmentPurposeBackbone /30 linksRouter-to-router OSPF Area 0Per-office data VLANsUser PCs, wireless, webcamsPer-office voice VLANsIP phones (Option 150 → CME)Server room VLANsServers, DMZ, admin, IoT, managementPublic /30 + DMZ NATInternet edge and published HTTPS

Skills Demonstrated
OSPF (multi-area) · VLANs & inter-VLAN routing · Routed access design · Layer 3 EtherChannel · Network redundancy · Cisco ASA firewall · NAT / PAT · DMZ & ACLs · DHCP + relay (Option 150) · DNS / FTP / NTP / Syslog · WAN static routing · Device hardening & SSH · Port security · IoT registration & automation · Wireless (WPA2) · CME / VoIP · Structured troubleshooting

Files in This Repository

*.pkt — the complete Cisco Packet Tracer project file
README.md — this document
/screenshots — topology and verification screenshots (see below)


How to Open
Open the .pkt file in Cisco Packet Tracer 8.x or later. Allow OSPF a minute to converge after loading. Device credentials follow a consistent lab convention documented in the configs.

Built as a hands-on study project to demonstrate end-to-end enterprise network design — routing, redundancy, security, services, and automation — in a single cohesive topology.
