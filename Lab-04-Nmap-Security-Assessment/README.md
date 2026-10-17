Lab 4 — Nmap: Network Scanning and Service Exposure Assessment
A hands-on Linux/Ubuntu networking and security assessment performed in a VirtualBox lab environment. This project documents authorized localhost scanning, service/process investigation, configuration review, a controlled service-stop test, and restoration.
Objectives
- Identify TCP and UDP services using Nmap.
- Correlate scan results with listening sockets, systemd units, processes, versions, and logs.
- Distinguish service exposure from confirmed vulnerabilities.
- Validate temporary hardening with before/after evidence and rollback.
- Produce a SOC-style assessment ticket.
Environment and tools
Ubuntu VM on VirtualBox; Nmap, ss, systemctl, journalctl, dpkg, apt, tcpdump, ufw, nft, iptables, and ip6tables.
Key observations
Service	Local scan (before)	Listening interfaces (before)	Local scan (after temporary stop)
CUPS / IPP (TCP 631)	open, CUPS 2.4	IPv4 and IPv6 loopback	closed
Avahi / mDNS (UDP 5353)	`open	filtered`	IPv4 and IPv6 wildcard


cups-browsed was also active. UFW was inactive, the displayed nftables ruleset had no rules, and inspected IPv4/IPv6 iptables chains had ACCEPT policies without listed rules. These results do not establish access from a separate host; the VM's network mode can affect reachability.
Validation workflow
1. Gather baseline with sudo ss -tulpen | grep -E ':631|:5353' and targeted Nmap scans of 127.0.0.1.
2. Review service state and dependencies using systemctl.
3. Check local firewall configuration for both IP families.
4. Temporarily stop CUPS, Avahi, and cups-browsed (including their activation units) in the lab.
5. Repeat Nmap and ss: both scanned ports appeared closed; no matching sockets were displayed.
6. Restore services and verify that they are active and their sockets listen again.
Security interpretation
An exposed listening socket is not the same as a confirmed exploitable vulnerability. CUPS listened on loopback, while Avahi bound to wildcard addresses. Operational need, firewall policy, other-host reachability, and relevant advisories should be considered before assigning severity or changing a production system.
SOC ticket
See [SEC-004 — Network Service Exposure Assessment](SEC-004.md) for evidence, provisional risk level, mitigations, and follow-up checks.
Skills demonstrated
Nmap scanning · TCP/UDP interpretation · Linux networking · Systemd troubleshooting · Service exposure assessment · Change validation · SOC documentation
