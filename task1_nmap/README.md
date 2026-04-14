# 🔍 Task 1: Basic Network Scanning with Nmap

> **Security Analyst Internship — Beginner Level Task 1**  
> Objective: Perform network scanning to identify open ports and services using Nmap.

---

## 📋 Table of Contents

- [Overview](#overview)
- [Tools & Environment](#tools--environment)
- [Installation](#installation)
- [Scans Performed](#scans-performed)
- [Findings & Analysis](#findings--analysis)
- [Port Risk Assessment](#port-risk-assessment)
- [Recommendations](#recommendations)
- [Repository Structure](#repository-structure)
- [Demo Video](#demo-video)

---

## Overview

Network scanning is one of the first steps in both offensive (penetration testing) and defensive (security auditing) cybersecurity operations. This task demonstrates how to use **Nmap (Network Mapper)** — the industry-standard open-source tool for network discovery and security auditing — to identify live hosts, open ports, running services, and potential vulnerabilities on a target local machine/VM.

**Target Environment:**
- Network Range: `192.168.56.0/24` (local lab network)
- Primary Target: `192.168.56.101` (Metasploitable/vulnerable VM)
- Scanner Host: `192.168.56.102` (analyst machine)

> ⚠️ **Legal Disclaimer:** All scans were performed in a controlled lab environment on machines I own and operate. Never scan networks or systems without explicit written permission. Unauthorized scanning may violate laws including the Computer Fraud and Abuse Act (CFAA).

---

## Tools & Environment

| Tool | Version | Purpose |
|------|---------|---------|
| Nmap | 7.95 | Network scanning & enumeration |
| OS | Kali Linux 2024.1 | Attacker/scanner machine |
| Target VM | Ubuntu 22.04 LTS | Deliberately configured vulnerable VM |
| Virtualization | VirtualBox 7.0 | Lab environment isolation |

---

## Installation

### Linux (Debian/Ubuntu/Kali)
```bash
sudo apt update
sudo apt install nmap -y
nmap --version
```

### macOS
```bash
brew install nmap
```

### Windows
Download the installer from: https://nmap.org/download.html

---

## Scans Performed

### Scan 1 — Host Discovery (Ping Sweep)
Discovers all live hosts on the network without port scanning.
```bash
nmap -sn 192.168.56.0/24
```
**Result:**  4 hosts discovered — gateway (.1), VM1 (.100), VM2 (.101), VM3 (.102)

---

### Scan 2 — Default Port Scan
Scans the top 1000 most commonly used TCP ports.
```bash
nmap 192.168.56.101
```
**Result:** 7 open ports identified

---

### Scan 3 — Service Version Detection
Probes open ports to identify software and version numbers.
```bash
nmap -sV 192.168.56.101
```
**Result:** Specific service versions identified (Apache 2.4.52, MySQL 8.0.35, OpenSSH 8.9p1, etc.)

---

### Scan 4 — Aggressive Scan (OS + Scripts + Traceroute)
Comprehensive scan combining OS detection, version detection, script scanning, and traceroute.
```bash
nmap -A 192.168.56.101
```
**Result:** OS identified as Linux 5.x/6.x, anonymous FTP login detected, HTTP headers exposed

---

### Scan 5 — Full Port Scan
Scans all 65,535 TCP ports to ensure no services are hiding on non-standard ports.
```bash
nmap -p- 192.168.56.101
```
**Result:** No additional hidden services found beyond the 7 identified ports

---

### Scan 6 — UDP Scan
Scans top 100 UDP ports (requires root/sudo).
```bash
sudo nmap -sU --top-ports 100 192.168.56.101
```
**Result:** DNS (53), DHCP (67/68), NTP (123) open on UDP

---

### Scan 7 — Vulnerability Script Scan
Runs Nmap's built-in NSE (Nmap Scripting Engine) vulnerability detection scripts.
```bash
nmap --script vuln 192.168.56.101 -p 21,22,80,443
```
**Result:** HTTP Slowloris vulnerability detected on port 80 (CVE-2007-6750)

---

## Findings & Analysis

### Open Ports Summary

| Port | Protocol | Service | Version | Risk Level |
|------|----------|---------|---------|------------|
| 21 | TCP | FTP | vsftpd 3.0.3 | 🔴 HIGH |
| 22 | TCP | SSH | OpenSSH 8.9p1 | 🟡 MEDIUM |
| 23 | TCP | Telnet | Linux telnetd | 🔴 CRITICAL |
| 53 | UDP | DNS | - | 🟡 MEDIUM |
| 80 | TCP | HTTP | Apache 2.4.52 | 🟡 MEDIUM |
| 123 | UDP | NTP | - | 🟢 LOW |
| 443 | TCP | HTTPS | Apache 2.4.52 | 🟢 LOW |
| 3306 | TCP | MySQL | MySQL 8.0.35 | 🔴 HIGH |
| 8080 | TCP | HTTP-Proxy | Squid 5.2 | 🟡 MEDIUM |

---

## Port Risk Assessment

### 🔴 Port 23 — Telnet (CRITICAL)
**What it is:** A legacy remote terminal access protocol.  
**Why it's dangerous:** Telnet transmits ALL data — including usernames and passwords — in **plain text**. Anyone on the same network can capture credentials using a packet sniffer (e.g., Wireshark).  
**Recommendation:** **Disable immediately.** Use SSH (port 22) instead.  
---

### 🔴 Port 21 — FTP (HIGH)
**What it is:** File Transfer Protocol for transferring files.  
**Why it's dangerous:**
- Like Telnet, FTP sends credentials in plain text.
- Anonymous FTP login was detected (`ftp-anon` script), meaning anyone can log in without credentials.
- Can be used to exfiltrate data or upload malicious files.  
**Recommendation:** Disable FTP. Use SFTP (SSH File Transfer Protocol) or FTPS (FTP over SSL/TLS).

---

### 🔴 Port 3306 — MySQL (HIGH)
**What it is:** MySQL database server port.  
**Why it's dangerous:** A database exposed to the network is a serious risk. Attackers can attempt:
- Brute-force attacks against MySQL credentials.
- SQL injection via network-accessible interfaces.
- Direct data exfiltration if credentials are compromised.  
**Recommendation:** MySQL should **never** be publicly accessible. Bind it to `127.0.0.1` only (`bind-address = 127.0.0.1` in `my.cnf`).

---

### 🟡 Port 22 — SSH (MEDIUM)
**What it is:** Secure Shell — encrypted remote terminal access.  
**Why it's a concern:** SSH is secure by design but is commonly targeted by brute-force attacks.  
**Recommendation:** 
- Disable password authentication; use SSH key pairs only.
- Change default port (optional security-through-obscurity).
- Use `fail2ban` to block repeated failed logins.

---

### 🟡 Port 80 — HTTP (MEDIUM)
**What it is:** Standard unencrypted web server port.  
**Why it's a concern:** 
- HTTP traffic is unencrypted and can be intercepted (Man-in-the-Middle attacks).
- A Slowloris DoS vulnerability (CVE-2007-6750) was detected.
- Server version (Apache 2.4.52) is disclosed in response headers.  
**Recommendation:** Redirect all HTTP traffic to HTTPS (port 443). Configure server to hide version headers (`ServerTokens Prod`).

---

### 🟢 Port 443 — HTTPS (LOW)
**What it is:** Encrypted web server port using SSL/TLS.  
**Why it's relatively safe:** Traffic is encrypted.  
**Note:** The SSL certificate is self-signed, which may cause browser warnings and doesn't provide the same trust level as a CA-signed certificate.

---

### 🟡 Port 8080 — HTTP Proxy / Squid (MEDIUM)
**What it is:** Squid HTTP proxy server.  
**Why it's a concern:** An open proxy can be used to route malicious traffic through your network, bypassing security controls.  
**Recommendation:** Restrict proxy access to authorized internal IP addresses only.

---

### 🟡 Port 53 — DNS (MEDIUM)
**What it is:** Domain Name System — translates domain names to IP addresses.  
**Why it's a concern:** Open DNS resolvers can be exploited for DNS amplification DDoS attacks.  
**Recommendation:** Configure DNS to respond only to authorized clients.

---

## Recommendations

### Immediate Actions (Critical)
1. **Disable Telnet (port 23)** — replace with SSH
2. **Disable anonymous FTP** — disable or replace with SFTP
3. **Restrict MySQL (port 3306)** — bind to localhost only

### Short-Term Actions
4. Configure SSH hardening (key-only auth, fail2ban)
5. Implement firewall rules (UFW/iptables) to restrict access
6. Patch Apache to latest version and hide server headers
7. Redirect HTTP to HTTPS

### General Best Practices
8. Run regular scheduled scans to detect new open ports
9. Implement a network monitoring solution (e.g., Zeek, Snort)
10. Maintain an asset inventory and compare against scan results

---
## 🎥 

### 🔍 Nmap Scan Demo
[Watch here](https://youtu.be/cNmRZ4b603E)

## Repository Structure

```
task1-nmap-scanning/
│
├── README.md                    # This file — full documentation
├── nmap_scan_results.txt        # Raw Nmap output from all scans
├── screenshots/
│   ├── ip-add_Verify-Nmap
│   ├── 01_host_discovery.png    # Ping sweep results
│   ├── 02_default_scan.png      # Basic port scan
│   ├── 03_version_detection.png # Service version scan
│   ├── 04-1aggressive_scan.png   # -A flag scan
│   ├── 04-2aggressive_scan.png
│   ├── 04-3aggressive_scan.png
│   ├── 04-4aggressive_scan.png  
│   ├── 05_full_port_scan.png    # All 65535 ports
│   ├── 06_udp_scan.png          # UDP scan results
│   ├── 07-1_vuln_scan.png         # NSE vulnerability scan
│   ├── 07-2_vuln_scan.png
│   └── 07-3_vuln_scan.png  
└──
```

---

*Completed as part of the Security Analyst Internship Program — Task 1 of 6 (Beginner Level)*
