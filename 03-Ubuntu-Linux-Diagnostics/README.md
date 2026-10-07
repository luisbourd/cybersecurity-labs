🐧 Lab 3 — Ubuntu Linux System & Network Diagnostics

📌 Overview

This lab focuses on practical Linux system administration and troubleshooting using an Ubuntu 24.04 virtual machine running in VirtualBox.

The objective was to develop a structured troubleshooting methodology by analyzing networking, services, processes, system resources, permissions, authentication, sessions, and system logs.

The lab concluded with an integrated troubleshooting scenario documented as INC-003.

---

🖥️ Lab Environment

- Operating System: Ubuntu 24.04 LTS
- Hypervisor: Oracle VirtualBox
- Network Mode: NAT
- IP Address: "10.0.2.15/24"
- Default Gateway: "10.0.2.2"
- Tools: Linux CLI, systemd, journalctl, jq, sysstat

---

🎯 Objectives

During this lab I practiced how to:

- Verify Linux network configuration
- Diagnose connectivity and DNS issues
- Identify listening ports and active connections
- Inspect and troubleshoot system services
- Analyze running processes
- Monitor CPU, memory, disk space, and I/O
- Manage Linux file permissions
- Review authentication and user sessions
- Analyze system logs using "journalctl"
- Filter structured logs using "jq"
- Investigate a simulated system performance incident
- Document technical findings in an incident ticket

---

1. Network Diagnostics

I started by establishing a baseline of the VM's network configuration.

Interface configuration

ip addr

Used to identify active interfaces and confirm the assigned IP address.

Observed VM address:

10.0.2.15/24

Routing table

ip route

Confirmed the default route through:

default via 10.0.2.2

Gateway connectivity

ping 10.0.2.2

This verified connectivity between the Ubuntu VM and the VirtualBox NAT gateway.

Internet connectivity

ping 8.8.8.8

A successful response confirmed external IP connectivity independently of DNS.

DNS resolution

nslookup google.com
ping google.com

This verified that DNS resolution and external connectivity were functioning correctly.

---

2. Ports and Network Connections

To inspect listening TCP and UDP ports:

sudo ss -tulpn

This allowed me to correlate open ports with the processes responsible for them.

One of the services identified was:

avahi-daemon

using UDP port:

5353

I also analyzed an established HTTPS connection associated with Firefox.

Example:

TCP → remote_host:443

This demonstrated how network connections can be mapped back to running processes.

---

3. Service Analysis with systemd

I inspected services using:

systemctl status avahi-daemon

I then correlated the service with its process:

ps -fp <PID>

and reviewed its logs:

journalctl -u avahi-daemon

The service was restarted using:

sudo systemctl restart avahi-daemon

After the restart, I confirmed that a new PID was assigned and reviewed the logs to verify that the service started normally.

This exercise demonstrated the relationship between:

Service
   ↓
systemd
   ↓
Process / PID
   ↓
Network Port
   ↓
Logs

---

4. Process Investigation

Processes were inspected using commands such as:

ps aux

and:

ps -fp <PID>

I analyzed CPU and memory utilization and traced process relationships.

Processes investigated included:

gnome-shell
systemd --user
gnome-session
gnome-keyring-daemon
avahi-daemon
Firefox

This helped distinguish normal user processes from system services.

---

5. CPU Monitoring

CPU usage was analyzed using real-time and historical information.

Examples included:

ps aux

and:

sar

This made it possible to compare current CPU utilization with historical activity instead of relying only on a single measurement.

During the final incident investigation, no persistent CPU saturation was detected.

---

6. Memory Analysis

Memory usage was inspected with:

free -h

This allowed me to analyze:

- Total RAM
- Used memory
- Available memory
- Cache
- Swap usage

The measurements taken during the final troubleshooting scenario did not indicate memory exhaustion.

---

7. Disk Space and I/O

Filesystem usage was checked using:

df -h

Disk performance and I/O activity were analyzed using:

iostat

This helped determine whether storage capacity or disk activity could explain reported performance problems.

No significant disk bottleneck was observed during the incident investigation.

---

8. Users and Linux Permissions

I practiced Linux ownership and permission concepts using:

whoami
id
touch
ls -l
chmod

Example permission progression:

-rw-------
-rw-r--r--
-rwxr--r--
-rw-r--r--

Commands practiced included:

chmod 600 file
chmod 644 file
chmod 744 file
chmod u-x file
chmod g+w file

This reinforced the relationship between:

Owner | Group | Others
  rwx |  rwx  |  rwx

and their numeric representation.

---

9. Securing Configuration Files

During the lab I also reviewed permissions on sensitive system configuration files.

A Netplan configuration file was secured using:

sudo chmod 600 <netplan-file>

Result:

rw-------

This ensures that only root can read or modify the configuration file.

---

10. Authentication and sudo/PAM

Authentication-related activity was investigated through system logs.

This included reviewing events associated with:

- "sudo"
- PAM authentication
- User sessions
- Privilege escalation

This demonstrated how Linux authentication events can be reconstructed through logs during a security investigation.

---

11. Session and Login Analysis

User activity was reviewed using commands such as:

who
last
uptime

These commands helped identify:

- Currently logged-in users
- Previous sessions
- Reboots
- System uptime

This is useful when establishing an incident timeline.

---

12. Log Analysis with journalctl

A major part of this lab focused on system log analysis.

Basic log inspection:

journalctl

Logs were filtered by time:

journalctl --since "..."

and by priority:

journalctl -p err

I also practiced filtering events using fields such as:

_COMM
_PID

This allowed me to isolate events generated by specific processes.

---

13. JSON Log Analysis with jq

Logs were exported/read in structured JSON format and analyzed using:

jq

This provided experience working with structured log data rather than only plain-text output.

Filters were used to isolate relevant fields and events based on:

- Process
- PID
- Time
- Priority
- Message content

This is particularly useful for security monitoring and SOC workflows where large quantities of logs need to be filtered quickly.

---

🔎 Final Troubleshooting Scenario — INC-003

Incident

A user reported intermittent system performance degradation.

The objective was to investigate the issue without assuming the cause beforehand.

---

Investigation

I followed a structured troubleshooting process covering:

CPU

Checked current and historical CPU utilization.

Result: No persistent CPU saturation detected.

Memory

Reviewed RAM and swap usage.

Result: No evidence of memory exhaustion.

Disk

Checked filesystem capacity and I/O performance.

Result: No significant storage or I/O bottleneck detected.

Network

Verified:

Interface configuration
Routing
Gateway connectivity
Internet connectivity
DNS resolution

Result: Network operation was normal during testing.

Services

Reviewed active services and their status.

Result: No critical service failure identified.

Logs

System logs were analyzed using:

journalctl
jq

Some graphical-related messages involving components such as:

vmwgfx
GNOME

were identified.

However, there was insufficient evidence to establish these messages as the root cause of the reported performance issue.

---

📝 Incident Conclusion

The reported intermittent performance degradation could not be reproduced during the investigation.

At the time of testing:

CPU      → Normal
Memory   → Normal
Disk     → Normal
Network  → Normal
Services → Normal

Graphical-related errors were observed in the system logs, but correlation with the reported performance problem could not be proven.

Therefore, the incident remained under investigation rather than assigning an unsupported root cause.

This was an important troubleshooting lesson:

«Finding an error in a log does not automatically mean that the error caused the reported incident.»

---

📋 Ticket INC-003

Issue: Intermittent Ubuntu VM performance degradation

Status: Investigation / Unable to reproduce

Findings:

- CPU utilization normal during testing
- Memory utilization normal
- No significant disk I/O bottleneck
- Network connectivity normal
- DNS resolution operational
- Critical services operational
- Graphical-related log errors observed
- No confirmed correlation between those errors and the reported degradation
- Root cause not established

Recommendation:

Continue monitoring the system and collect additional CPU, memory, disk, process, and log data if the performance issue occurs again.

---

🧠 Skills Demonstrated

This lab provided hands-on experience with:

Linux Administration
Linux Networking
TCP/IP Troubleshooting
DNS Troubleshooting
Process Analysis
Service Management
systemd
Linux Permissions
Authentication Logs
Resource Monitoring
Log Analysis
journalctl
jq
Incident Investigation
Technical Documentation

---

🛠️ Commands Practiced

ip addr
ip route
ping
nslookup
ss -tulpn

systemctl status
systemctl restart
journalctl

ps aux
ps -fp

free -h
df -h
iostat
sar

whoami
id
who
last
uptime

touch
ls -l
chmod

jq

---

📈 Lab Result

Status: ✅ Completed

Final Evaluation: 8.5 / 10

The lab strengthened my ability to troubleshoot Linux systems using a structured methodology rather than immediately assuming a root cause.

It also provided practical experience relevant to entry-level:

- SOC Analyst
- Cybersecurity Analyst
- IT Support
- Linux Support
- Junior System Administrator

roles.

---

➡️ Next Lab

Lab 4 — Nmap Network Scanning & Enumeration

The next lab will focus on identifying hosts, ports, services, and network exposure inside a controlled lab environment.