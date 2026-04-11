# Task 8 – Capture Network Traffic with Wireshark

## Objective
Capture and analyze live network traffic on a local network using Wireshark, with a focus on filtering and inspecting HTTP packets to understand how unencrypted data travels across a network.

---

## Tools & Environment
| Tool | Version / Details |
|------|------------------|
| OS | Kali Linux (on VMware) |
| Wireshark | 4.6.0 |
| Terminal | Bash |
| Interface | eth0 / ens33 (VMware NAT) |

---

## Setup & Installation

Wireshark comes pre-installed on Kali Linux. To verify:

```bash
wireshark --version
```

If not installed:

```bash
sudo apt update && sudo apt install wireshark -y
```

Allow non-root users to capture packets (select **Yes** when prompted), then add your user to the wireshark group:

```bash
sudo usermod -aG wireshark $USER
```

Log out and back in for the group change to take effect.

---

## Steps Performed

### 1. Launching Wireshark
Opened Wireshark from the terminal:
```bash
wireshark
```
Or via: **Applications → Sniffing & Spoofing → Wireshark**

### 2. Selecting the Network Interface
Identified the active interface:
```bash
ip a
```
Selected `eth0` (or `ens33`) in Wireshark — the interface showing an assigned IP address from VMware's NAT network.

### 3. Generating HTTP Traffic
With capture running, generated plain HTTP traffic using:
```bash
curl http://example.com
curl http://neverssl.com
curl http://httpforever.com
```
These sites use unencrypted HTTP, making the full request and response visible in Wireshark.

### 4. Stopping the Capture
Clicked the **red square** stop button after ~2 minutes of traffic.

### 5. Applying the HTTP Filter
In the Wireshark display filter bar, entered:
```
http
```
This isolated only HTTP request and response packets from the full capture.

### 6. Saving the Capture File
Saved the capture via **File → Save As** → `wireshark_capture.pcap`

---

## Packet Analysis

### HTTP GET Request
| Field | Value |
|-------|-------|
| Source IP | 10.0.2.15 (local VM) |
| Destination IP | 93.184.216.34 (example.com) |
| Protocol | HTTP |
| Method | GET |
| Request URI | / |
| Host | example.com |
| User-Agent | curl/7.x |

### HTTP 200 OK Response
| Field | Value |
|-------|-------|
| Status Code | 200 OK |
| Content-Type | text/html; charset=UTF-8 |
| Content-Length | 1256 bytes |
| Server | ECS |

### DNS Resolution (before TCP)
Before any HTTP traffic, Wireshark captured a DNS query:
- **Query:** `example.com → type A`
- **Answer:** `93.184.216.34`
- **Filter used:** `dns`

### TCP Three-Way Handshake
Observed before the HTTP exchange:
1. **SYN** — client initiates connection
2. **SYN-ACK** — server acknowledges
3. **ACK** — client confirms, connection established

### TCP Stream (Follow Stream)
Right-clicking a packet and selecting **Follow → TCP Stream** revealed the full conversation in plaintext — demonstrating that HTTP data (including headers and body) is fully readable without encryption.

---

## Display Filters Used

| Filter | Purpose |
|--------|---------|
| `http` | Show only HTTP packets |
| `dns` | Show DNS query/response packets |
| `tcp.port == 80` | Show traffic on port 80 only |
| `ip.addr == 93.184.216.34` | Filter by destination IP |
| `http.request.method == "GET"` | Show only HTTP GET requests |

---

## Security Observations

- **HTTP is unencrypted.** All headers, request URIs, cookies, and response bodies are visible in plaintext inside Wireshark.
- **HTTPS (TLS)** traffic on port 443 shows only encrypted payloads — content cannot be read, only metadata (IPs, port, packet size) is visible.
- This analysis demonstrates why sensitive operations (login, payments, personal data) must always use HTTPS.
- Even on a local network, an attacker with network access could capture and read all HTTP traffic using the same technique.



---

## How to Open and Reproduce

1. Install Wireshark on any OS from [wireshark.org](https://wireshark.org)
2. Open `wireshark_capture.pcap` via **File → Open**
3. Apply the `http` display filter in the filter bar
4. Click any GET packet and expand **Hypertext Transfer Protocol** in the middle pane
5. Right-click any packet → **Follow → TCP Stream** to view the full conversation

---

## Author
**Farah Hassen**
Security Analyst Intern
Task 8 — Network Traffic Capture with Wireshark
