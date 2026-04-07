# Network Security Threats: A Comprehensive Research Report
 
**Task 4:** Research Report on Common Network Security Threats
**Date:** April 2026  
**Classification:** Educational / Research
## Executive Summary

Network security threats continue to evolve rapidly in 2025–2026, driven by increasing reliance on digital infrastructure, the proliferation of IoT devices, and sophisticated attack tools. This report provides a comprehensive analysis of three primary network security threats: **Denial-of-Service (DoS)** attacks, **Man-in-the-Middle (MITM)** attacks, and **Spoofing**. Each section details the underlying mechanisms, operational impacts, real-world incidents, and strategic mitigation techniques required to maintain a robust security posture in the current threat climate of 2026.

---
## Introduction
 
Modern organizations depend on network infrastructure for virtually every operational function. As network reliance grows, so does the attack surface available to adversaries. The threats examined in this report do not merely represent technical curiosities — they carry real financial, reputational, and safety consequences.
 
According to IBM's Cost of a Data Breach Report (2023), the average cost of a data breach reached $4.45 million USD, with network-layer attacks playing a significant contributing role. Understanding *how* these attacks function is the first step toward defending against them.
 
This report targets security professionals, network administrators, students, and decision-makers who need a clear, structured understanding of network attack categories and their countermeasures.
 
---

## 1. Denial-of-Service (DoS) and Distributed Denial-of-Service (DDoS)

### 1.1 Definition and Overview
 
A **Denial of Service (DoS)** attack is a malicious attempt to disrupt the normal functioning of a targeted server, service, or network by overwhelming it with a flood of illegitimate traffic or requests. 
Unlike data-theft attacks, DoS/DDoS attacks do not typically aim to breach confidentiality — their goal is **disruption of availability**, one of the three pillars of the CIA (Confidentiality, Integrity, Availability) triad.
When this attack is launched from multiple distributed sources simultaneously, it becomes a **Distributed Denial of Service (DDoS)** attack.
### 1.2 How DoS/DDoS Attacks Work
 
DoS and DDoS attacks are broadly categorized into three types:
 
#### 1.2.1 Volume-Based Attacks
These attacks flood the target's bandwidth with massive amounts of traffic, rendering the network unreachable.
 
- **UDP Flood:** Sends large numbers of UDP packets to random ports, causing the host to repeatedly check for and respond to each packet, exhausting resources.
- **ICMP Flood (Ping Flood):** Overwhelms the target with ICMP Echo Request packets faster than it can process them.
- **DNS Amplification:** The attacker sends DNS queries with a spoofed source IP (the victim's IP). The DNS server sends large responses to the victim, amplifying the traffic volume significantly.
 
#### 1.2.2 Protocol Attacks
These exploit weaknesses in network protocol stacks.
 
- **SYN Flood:** Exploits the TCP three-way handshake. The attacker sends many SYN packets but never completes the handshake (never sends ACK), filling the server's connection queue until it can accept no new legitimate connections.
- **Ping of Death:** Sends malformed or oversized ICMP packets to crash the target system.
- **Smurf Attack:** Broadcasts ICMP packets with the victim's spoofed source IP, causing all hosts on the network to reply to the victim.
 
#### 1.2.3 Application Layer Attacks (Layer 7)
These mimic legitimate user traffic and are harder to detect.
 
- **HTTP Flood:** Sends a flood of HTTP GET or POST requests to a web server, exhausting server resources.
- **Slowloris:** Opens many connections to the target server and keeps them open by sending partial HTTP headers, preventing the server from completing the request.

| Attack Type | Focus Area | Primary Mechanism |
| :--- | :--- | :--- |
| **Volumetric** | Bandwidth | Saturates the target's internet pipe with massive amounts of traffic (e.g., UDP/ICMP floods). |
| **Protocol** | Network Layer | Exploits weaknesses in network protocols (e.g., SYN floods, Ping of Death) to exhaust server resources. |
| **Application Layer** | Application Logic | Targets specific functions of a web application (e.g., HTTP GET/POST floods) to crash the server. |


#### 1.3 The Role of Botnets
Most large-scale DDoS attacks leverage **botnets** — networks of compromised devices (including IoT devices, home routers, and servers) controlled by an attacker (botmaster).
One of the most well-known examples is the **Mirai botnet (2016)**, which infected hundreds of thousands of poorly secured IoT devices. It was used to launch large-scale DDoS attacks that disrupted major online services.

Importantly, botnets are **not a standalone attack type**, but rather an **attack delivery mechanism**. They can be used to execute all categories of DDoS attacks:

- **Volumetric attacks** → by generating huge traffic volumes  
- **Protocol attacks** → by exploiting network weaknesses at scale  
- **Application-layer attacks** → by mimicking legitimate user requests  

```
Attack Flow:
[Attacker Command & Control Server]
        |
        v
[Botnet: Thousands of Compromised Devices]
        |
        v (Flood of traffic)
[Target Server / Network] --> Service Unavailable
```
### 1.4 Impact and Mitigation

| Impact Category | Description |
|----------------|-------------|
| Financial | Revenue loss from downtime; ransom demands; recovery costs |
| Reputational | Customer distrust; SLA violations |
| Operational | Staff diverted to incident response; disrupted workflows |
| National Security | Attacks on critical infrastructure (power grids, hospitals) |

The primary impact of DDoS attacks is **service unavailability**, which leads to direct financial loss, breach of Service Level Agreements (SLAs), and severe reputational damage.

A single hour of downtime can cost enterprises hundreds of thousands of dollars. For e-commerce platforms during peak periods (e.g., Black Friday), the losses can be catastrophic.

 Modern mitigation strategies involve a multi-layered approach:

*   **Traffic Scrubbing**: Utilizing cloud-based services (e.g., Cloudflare, Akamai) to filter malicious traffic before it reaches the origin server.
*   **Rate Limiting**: Implementing thresholds for the number of requests a server will accept from a single IP or user agent.
*   **Anycast Routing**: Distributing incoming traffic across a network of geographically dispersed servers to absorb the volume.
* **SYN cookies:** A technique that allows servers to avoid allocating resources until the TCP handshake is complete, defeating SYN flood attacks.
* **Firewall rule sets:** Block known malicious IPs, spoofed packets, and suspicious traffic patterns.
* **Web Application Firewalls (WAF):** Detect and block Layer 7 attack patterns.
* **CAPTCHA challenges:** Distinguish legitimate users from automated bots.
* **Content Delivery Networks (CDN):** Distribute load geographically.

### 1.5 Real-World Example: The Mirai Botnet (2016)

**1. GitHub DDoS Attack (2018)**  
GitHub suffered the largest DDoS attack recorded at the time — a 1.35 Tbps attack using a technique called Memcached amplification. Attackers exploited open Memcached servers to amplify traffic by a factor of 51,000x. GitHub's DDoS mitigation service absorbed the attack within 10 minutes.
 
**2. Dyn DNS Attack (2016)**  
The Mirai botnet launched a massive DDoS attack against Dyn, a major DNS provider. This took down major websites including Twitter, Reddit, Netflix, and Spotify for hours, demonstrating the cascading impact of targeting DNS infrastructure.

**3.Cloudflare Mitigation (February 2023 & 2025)**
 Cloudflare blocked a record 71 million requests-per-second attack and, in 2025, successfully defended against a 7.3 Tbps volumetric attack delivering 37.4 TB in 45 seconds.

**4.U.S. Airports Attack (October 2022)**
 Russian group KillNet used DDoS to crash websites of multiple major airports, disrupting flight information access (though operational systems remained intact).

---

## 2. Man-in-the-Middle (MITM) Attacks

### 2.1 Definition and Overview
A Man-in-the-Middle (MITM) attack occurs when a malicious actor secretly intercepts and potentially alters the communication between two parties who believe they are communicating directly.
The attacker positions themselves "in the middle" of the communication channel. This allows the attacker to capture sensitive information, such as login credentials or financial data, without either party's knowledge.

MITM attacks threaten all three pillars of the CIA triad:
- **Confidentiality:** The attacker reads private communications.
- **Integrity:** The attacker alters data in transit.
- **Availability:** The attacker can selectively block communications.

### 2.2 How MITM Attacks Work
MITM attacks typically proceed in two phases:
*  **interception:** The attacker gains access via unsecured Wi-Fi, DNS manipulation, ARP poisoning, or compromised routers.

*  **decryption/Manipulation:** The attacker eavesdrops on, alters, or injects data in real time while relaying messages to maintain the illusion of direct communication.

### 2.2.1 Interception Techniques

| Technique              | Description |
| :---                   | :--- |
| **ARP Spoofing**       | Associating the attacker's MAC address with a legitimate IP on a Local Area Network (LAN). |
| **DNS Spoofing**       | Poisoning a DNS resolver's cache to redirect users to a fraudulent website. |
| **HTTPS Stripping**    | Downgrading a secure HTTPS connection to an unencrypted HTTP connection to intercept data. |
| **Session Hijacking**  | Stealing session cookies to impersonate a user after they have successfully logged in. |
| **Rogue Wi-Fi AP**     | Creating a fake wireless network to intercept user traffic. |
| **BGP Hijacking**      | Manipulating internet routing to redirect traffic through malicious networks. |

* **ARP Spoofing (ARP Poisoning):**
```
Normal:
[Client] -----> [Router] -----> [Internet]
 
After ARP Poisoning:
[Client] --> [Attacker] --> [Router] --> [Internet]
             (intercepts/
              modifies traffic)
```
### 2.2.2 Decryption Techniques

Once traffic is intercepted, the attacker may employ:
| Technique                          | Description |
| :---                               | :--- |
| **SSL/TLS Interception (TLS Termination)** | The attacker uses a fraudulent certificate to terminate the TLS session, decrypt traffic, and re-encrypt it toward the legitimate server. |
| **BEAST Attack**                   | Exploits vulnerabilities in older TLS versions to decrypt parts of encrypted HTTPS traffic. |
| **CRIME Attack**                  | Targets TLS compression mechanisms to recover sensitive information from encrypted sessions. |
| **POODLE Attack**                 | Exploits weaknesses in SSL 3.0, allowing attackers to decrypt secure communications. |
| **HTTPS Certificate Forgery**     | The attacker presents a fake but CA-signed or trusted-looking certificate to trick the victim into accepting a malicious connection. |

### 2.2 Impact and Mitigation

| Impact Category | Description |
|----------------|-------------|
| Credential theft | Captured usernames, passwords, session tokens |
| Financial fraud | Interception of banking transactions |
| Data exfiltration | Theft of sensitive business communications |
| Session hijacking | Taking over authenticated sessions |
| Malware injection | Inserting malicious code into HTTP responses |

 Mitigation requires the enforcement of strong encryption and identity verification:

*   **End-to-End Encryption**: Mandatory use of TLS 1.3 for all web traffic and encrypted communication protocols (e.g., SSH, VPNs).
*   **HTTP Strict Transport Security (HSTS)**: Forcing browsers to only connect via secure HTTPS, preventing downgrade attacks.
*   **Multi-Factor Authentication (MFA)**: Ensuring that even if credentials are intercepted, the attacker cannot gain access without a secondary factor.
* **User Education:**
- Train users to verify the padlock icon and check for HTTPS before entering credentials.
- Warn against using public Wi-Fi for sensitive transactions without a VPN.
- Use a reputable **VPN** to encrypt traffic on untrusted networks.

### 2.3 Real-World Example: 
**1. Lenovo Superfish (2015)**  
Lenovo pre-installed adware called "Superfish" on consumer laptops. The software used a self-signed root CA certificate to perform MITM interception of all HTTPS traffic, injecting advertisements. This rendered every HTTPS connection on affected machines vulnerable to external MITM attacks.
 
**2. DigiNotar Certificate Authority Breach (2011)**  
the Dutch Certificate Authority (CA) **DigiNotar** was compromised by hackers who issued over 500 fraudulent SSL certificates. One of these certificates was used to conduct a massive MITM attack against Iranian citizens accessing Google services. This allowed the attackers to intercept private communications on a national scale, eventually leading to the total collapse and bankruptcy of DigiNotar after major browsers revoked trust in its certificates.
 
**3. NSA QUANTUM / FOXACID Programs (revealed 2013)**  
Edward Snowden's disclosures revealed that the NSA conducted large-scale MITM attacks against internet infrastructure, intercepting traffic and injecting malware into unencrypted HTTP sessions through a technique known as a "man-on-the-side" attack.
 
**4. Public Wi-Fi MITM Attacks**  
Numerous documented cases exist of attackers at airports, cafes, and hotels using tools like `ettercap` or `bettercap` to perform ARP poisoning and credential harvesting on public Wi-Fi networks.

---

## 3. Spoofing Attacks

### 3.1 Overview and Mechanisms
Spoofing is a broad category of attacks where a malicious actor disguises their identity to gain unauthorized access to a system, bypass security controls, or facilitate other attacks like DDoS and MITM. By forging headers or identity markers, the attacker tricks the network into trusting the malicious source.

| Spoofing Type        | Target Identity        | Operational Use |
| :---                 | :---                  | :--- |
| **IP Spoofing**      | Source IP Address     | Used to hide the attacker’s location, bypass IP-based access controls, or amplify DDoS attacks. |
| **MAC Spoofing**     | Hardware Address      | Used to impersonate a trusted device on a local network to bypass MAC filtering. |
| **Email Spoofing**   | "From" Address        | Used for phishing, spear phishing, and Business Email Compromise (BEC) attacks. |
| **DNS Spoofing**     | Domain Mapping        | Redirects legitimate traffic to malicious IP addresses for phishing or malware distribution. |
| **GPS Spoofing**     | Location Signals      | Sends false GPS signals to mislead navigation systems (aviation, maritime, military). |
| **Caller ID Spoofing** | Phone Number        | Used in vishing attacks by impersonating trusted institutions (banks, governments). |
| **Web Spoofing**     | Domain / Website      | Creates fake or look-alike websites (typosquatting) to steal credentials. |

### 3.2 Impact and Mitigation

| Impact Category | Description |
|----------------|-------------|
| Financial fraud | BEC attacks caused $2.7B in losses (FBI IC3, 2022) |
| Identity theft | Credential harvesting through phishing |
| Network infiltration | Using spoofed IPs to bypass perimeter defenses |
| Reputational damage | Attackers impersonating a company's domain |
| Physical safety | GPS spoofing affecting navigation systems |

Spoofing undermines the fundamental trust mechanisms of a network. Effective countermeasures focus on **validation** and **authentication**:

*   **Ingress/Egress Filtering**: Configuring routers to drop packets that have source IP addresses that do not belong to the network they originated from (e.g., BCP 38).
*   **Dynamic ARP Inspection (DAI)**: A security feature on network switches that validates ARP packets to prevent ARP spoofing.
*   **DNSSEC**: Implementing digital signatures on DNS records to ensure that the data received is authentic and has not been tampered with.
*   **Email Authentication**: Utilizing SPF, DKIM, and DMARC to verify the sender's identity and prevent email forgery.

### 3.3 Real-World Example: 
**1. Business Email Compromise — FACC AG (2016)**  
Austrian aerospace manufacturer FACC AG lost approximately €50 million after attackers spoofed the CEO's email address, instructing the finance department to transfer funds for a fictitious acquisition project ("fake president" fraud).
 
**2. Twitter Bitcoin Scam (2020)**  
Attackers gained access to Twitter's internal admin panel and used it to post from verified, high-profile accounts including Barack Obama, Elon Musk, and Apple. The posts appeared legitimate and directed followers to send Bitcoin to a wallet address, netting over $100,000 in minutes. While this involved account compromise rather than pure email spoofing, the deception mechanism was fundamentally a spoofing strategy.
 
**3. DNS Spoofing Attack on MyEtherWallet (2018)**  
Attackers compromised BGP routing to hijack DNS traffic for Amazon Route 53 and redirected MyEtherWallet users to a spoofed site, stealing approximately $150,000 in Ethereum from users who entered their private keys.
 
**4. GPS Spoofing in the Black Sea (2017)**  
Multiple ships in the Black Sea reported their GPS systems placing them at Gelendzhik Airport, far inland. Security researchers confirmed this was deliberate GPS spoofing, possibly state-sponsored, affecting over 20 vessels simultaneously.

---

## 4. Additional Common Network Threats
 
While DoS/DDoS, MITM, and spoofing are the focus of this report, a comprehensive security posture must also account for:
 
### 4.1 Packet Sniffing / Eavesdropping
Passive capture of network traffic using tools like Wireshark. Effective against unencrypted protocols (HTTP, FTP, Telnet). Mitigation: encrypt all traffic using TLS; use secure protocols (SSH instead of Telnet, SFTP instead of FTP).
 
### 4.2 Replay Attacks
Captured authentication tokens or session data are retransmitted to gain unauthorized access. Mitigation: use timestamps, nonces, and session tokens with short expiry.
 
### 4.3 Port Scanning and Reconnaissance
Attackers use tools like `nmap` to discover open ports and services, gathering intelligence before launching targeted attacks. Mitigation: firewall rules, intrusion detection systems, and minimizing exposed services.
 
### 4.4 SQL Injection (Network-Delivered)
Malicious SQL code is inserted via network-transmitted input fields, potentially exfiltrating or destroying databases. Mitigation: parameterized queries, input validation, WAFs.
 
---
 
## 7. Comparative Analysis
 
| Attribute | DoS/DDoS | MITM | Spoofing |
|-----------|----------|------|---------|
| **Primary Goal** | Disruption | Interception / Manipulation | Deception / Impersonation |
| **CIA Pillar Targeted** | Availability | Confidentiality, Integrity | All three |
| **Detection Difficulty** | Low–Medium | High | Medium–High |
| **Attack Origin** | Remote (often distributed) | Local network preferred | Local or remote |
| **Skill Level Required** | Low (DDoS-for-hire services exist) | Medium–High | Low–Medium |
| **Primary Victim** | Services, servers | Individual users | Organizations, individuals |
| **Common Tools** | LOIC, Mirai, hping3 | Ettercap, Bettercap, Wireshark | Sendmail, scapy, SET |
 
---
 

## 4. Conclusion and Future Outlook

The evolution of network security threats from 2024 to 2026 indicates a shift toward **AI-augmented attacks** and **automated exploitation**. As attackers leverage machine learning to bypass traditional detection, organizations must adopt a "Zero Trust" architecture, where no entity is trusted by default, regardless of its location relative to the network perimeter. Continuous monitoring, rapid patching, and comprehensive employee training remain the cornerstones of a resilient defense strategy against the ever-changing landscape of network threats.
