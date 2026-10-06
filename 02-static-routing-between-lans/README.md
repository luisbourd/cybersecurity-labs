Static Routing Between Two LANs

Overview

This lab focused on configuring communication between two separate IPv4 LANs using Cisco routers and static routing.

The objective was to understand how routers learn about remote networks and how routing tables determine where packets should be forwarded.

Environment

Platform: Cisco Packet Tracer

Technologies:

- Cisco IOS
- IPv4
- Static Routing
- Ethernet
- ICMP
- TCP/IP

Network Design

The topology contained two separate LANs connected through two routers.

Example addressing:

LAN 1
192.168.1.0/24

       |
     Router0
       |
10.0.0.0/30
       |
     Router1
       |
LAN 2
192.168.2.0/24

The "/30" network was used for the point-to-point connection between the routers.

Initial Verification

Host configuration was verified using:

ipconfig

Local connectivity was tested using:

ping <default-gateway>

Router interfaces were inspected with:

show ip interface brief

This allowed me to confirm that the relevant interfaces were operational.

Routing Table Analysis

The router routing tables were examined using:

show ip route

I learned to distinguish between entries such as:

C    Connected
L    Local
S    Static

Connected networks appeared automatically, while remote networks required routing information.

Static Route Configuration

A route to a remote network was configured using the Cisco IOS "ip route" command.

Example:

ip route 192.168.2.0 255.255.255.0 10.0.0.2

The opposite router also required a return route so that traffic could successfully travel in both directions.

Verification

After configuring the routes, I checked the routing tables again:

show ip route

The static routes appeared with the "S" designation.

Connectivity was then tested progressively:

PC → Local Gateway
Router0 → Router1
PC → Remote PC

Successful end-to-end communication confirmed that routing was operating correctly.

Troubleshooting Exercises

Additional scenarios intentionally introduced configuration errors including:

- Missing static routes
- Incorrect default gateways
- Administratively disabled interfaces
- Incorrect ACL configurations

Instead of immediately changing configurations, each problem was isolated through connectivity testing and inspection of the routing tables and running configuration.

Skills Demonstrated

- Cisco IOS CLI
- IPv4 addressing
- Subnet masks
- "/30" point-to-point networks
- Static routing
- Routing tables
- Default gateways
- ICMP troubleshooting
- End-to-end connectivity testing
- Network fault isolation

Key Takeaway

Successful communication between two LANs requires more than correct IP addressing.

Routers must know how to reach remote networks, and return routes must also exist so response traffic can reach the originating network.