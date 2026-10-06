Cisco Network Troubleshooting Lab

Overview

This project documents a series of network troubleshooting scenarios completed in Cisco Packet Tracer. The objective was to diagnose connectivity problems using a structured troubleshooting methodology rather than immediately modifying device configurations.

The scenarios included routing problems, incorrect default gateways, Access Control Lists (ACLs), interface configuration issues, and connectivity failures between different LANs.

Lab Environment

Platform: Cisco Packet Tracer

Technologies:

- IPv4
- TCP/IP
- Static Routing
- Cisco IOS
- Access Control Lists (ACLs)
- VLANs
- ICMP
- Ethernet

Network Topology

The lab consisted of two LANs connected through Cisco routers.

Example networks:

- LAN 1: "192.168.1.0/24"
- LAN 2: "192.168.2.0/24"
- Router-to-router network: "10.0.0.0/30"

The objective was to maintain end-to-end communication between hosts located on different networks.

---

Troubleshooting Methodology

For each incident, I followed a structured troubleshooting process:

1. Verified host IP configuration.
2. Tested local connectivity.
3. Tested connectivity to the default gateway.
4. Used "tracert" or "ping" to isolate the failure.
5. Inspected router interface status.
6. Inspected routing tables.
7. Reviewed running configurations.
8. Identified the root cause.
9. Implemented the appropriate corrective action.
10. Verified end-to-end connectivity.

Commands used included:

ipconfig
ping
tracert

Cisco IOS commands included:

show ip interface brief
show ip route
show running-config
show interfaces status
show vlan brief

---

Incident 1 — Missing Static Route

Problem

A workstation could communicate with devices on its local network but could not reach a workstation located on the remote LAN.

Investigation

I first verified the workstation's IP configuration and confirmed that it could successfully reach its local default gateway.

I then tested connectivity between the networks and isolated the problem to the routers.

The routing table was inspected using:

show ip route

The remote network was not present.

Root Cause

Router0 did not contain a route to the remote "192.168.2.0/24" network.

Resolution

A static route was configured through the inter-router connection.

configure terminal
ip route 192.168.2.0 255.255.255.0 10.0.0.2
end

The routing table was checked again to confirm that the static route had been installed.

Verification

Connectivity was tested between the routers and then between the end devices.

The final end-to-end ping was successful with 0% packet loss.

---

Incident 2 — Incorrect Default Gateway

Problem

A workstation was unable to communicate with devices outside its local network.

Investigation

I checked the workstation configuration using:

ipconfig

The IP address and subnet mask were correct, but the default gateway was incorrect.

Root Cause

The workstation had been configured with an invalid default gateway.

Resolution

The correct default gateway was configured.

After correcting the host configuration, I also verified the router's routing table and ensured that a route to the remote LAN existed.

Verification

I tested:

- Workstation → Local gateway
- Router → Router
- Workstation → Remote workstation

End-to-end communication was restored.

---

Incident 3 — ACL Blocking ICMP Traffic

Problem

Hosts had valid IP configurations and the network interfaces were operational, but communication between the LANs was failing.

Investigation

Initial tests confirmed:

- Local connectivity was operational.
- Default gateways were reachable.
- Router interfaces were "up/up".
- Physical connectivity was functioning.

I inspected the router configuration using:

show running-config

An Access Control List was discovered that denied ICMP traffic between the networks.

Example:

deny icmp 192.168.1.0 0.0.0.255 host 192.168.2.10

Root Cause

An ACL applied to the router interface was blocking ICMP traffic between the two LANs.

Resolution

The incorrect ACL configuration and its interface association were removed.

Example troubleshooting commands included:

configure terminal
interface g0/0
no ip access-group 130 in
exit
no access-list 130

Verification

After removing the ACL restriction, connectivity was tested again.

The hosts successfully communicated across the network with 0% packet loss.

---

Incident 4 — Multiple ACL Configuration Issue

Problem

Communication between the "192.168.1.0/24" and "192.168.2.0/24" networks was unavailable despite correct IP addressing and operational router interfaces.

Investigation

Both routers were inspected.

The investigation revealed ACL configurations affecting traffic on both sides of the routed connection.

Root Cause

ACL rules applied to the routers were preventing legitimate traffic from passing between the LANs.

Resolution

The problematic ACL configurations were removed from the appropriate interfaces and the unnecessary ACL entries were deleted.

Verification

After correcting the ACL configuration:

- Router-to-router connectivity succeeded.
- Default gateways responded.
- End-to-end workstation communication succeeded.

---

Skills Demonstrated

This lab demonstrates practical experience with:

- Network troubleshooting
- IPv4 addressing and subnetting
- Static routing
- Default gateways
- Cisco IOS CLI
- Access Control Lists
- Routing tables
- ICMP
- VLAN configuration
- Network fault isolation
- Root cause analysis
- Technical incident documentation

Key Takeaway

The most important lesson from these scenarios was to troubleshoot systematically instead of immediately changing configurations.

By testing connectivity progressively from the endpoint toward the remote network, I was able to isolate failures and identify whether the problem existed at the host, gateway, routing, interface, or ACL level.