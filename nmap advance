# Nmap: Advanced Research and Network Enumeration Methodology

This document outlines a rigorous, multi-stage methodology for network reconnaissance and service enumeration using Nmap. This approach moves beyond basic command execution to focus on **protocol analysis, defensive evasion, and targeted vulnerability identification.**

---

## I. Phase 1: Host and Network Discovery (The Low-Noise Approach)

The objective is to gather a comprehensive list of active targets while minimizing detection by firewalls or Intrusion Detection Systems (IDS).

### A. Non-Standard Pings (Protocol-Level Evasion)

| Technique | Command | Underlying Principle & Evasion |
| :--- | :--- | :--- |
| **TCP SYN Ping** | `nmap -PS [port] 10.10.1.0/24` | Sends a TCP SYN packet to a non-standard port (e.g., `-PS21,80,443`). Many modern firewalls only monitor ICMP (standard ping), making this far more reliable for active hosts. |
| **TCP ACK Ping** | `nmap -PA [port] 10.10.1.0/24` | Sends a TCP ACK packet. This can bypass stateless firewalls that only block SYN packets. An ACK to a non-existent connection typically elicits an RST response if the host is alive. |
| **UDP Ping** | `nmap -PU [port] 10.10.1.0/24` | Sends an empty UDP packet. Excellent for discovering targets that heavily filter all TCP traffic but rely on UDP services (like DNS or SNMP). |

### B. Stealth Host-Sweep Techniques

* **ARP Discovery (`-PR`):** `nmap -sn -PR 192.168.1.0/24`
    * **Context:** Used *only* when the attacker is on the same local network (LAN) as the target. It relies on Address Resolution Protocol (ARP) requests, which are completely ignored by network layers 3 and 4, making it undetectable by most host-based firewalls.

---

## II. Phase 2: Port Scanning and Firewall Evasion

This phase involves determining the state of all 65,535 TCP and UDP ports using techniques designed to defeat common network security controls.

### A. Advanced Scan Types (Protocol Deep Dive)

| Technique | Flag | Network State Determination |
| :--- | :--- | :--- |
| **TCP NULL Scan** | `-sN` | Sends a packet with *no* flags set. If the port is open, a RFC-compliant machine will send *no* response. If it's closed, it sends an RST packet. **Mitigation Bypass:** Many stateful firewalls are programmed to only inspect standard SYN/FIN packets. |
| **TCP FIN Scan** | `-sF` | Sends only the FIN flag. Logic is identical to the NULL scan. Often used when the NULL scan is detected, offering an alternative path to evasion. |
| **TCP Xmas Scan** | `-sX` | Sends FIN, PSH, and URG flags. The packet lights up "like a Christmas tree." Follows the same detection logic as NULL/FIN scans. |
| **Window Scan** | `-sW` | Exploits a subtle difference in TCP Window size reporting. Can determine open/closed states even when firewall rules make it look "filtered." |

### B. Fragmentation and Timing (Detection Evasion)

* **IP Fragmentation:** `nmap -f 10.10.1.5`
    * **Context:** Splits the Nmap probe into tiny fragments, forcing the firewall's network stack to reassemble them. Older IDS/IPS devices often fail to correctly reassemble the packets, allowing the malicious payload to slip through uninspected.
* **Decoy Scan:** `nmap -D RND:5,ME 10.10.1.5`
    * **Context:** Sends scan packets seemingly from 5 random, decoy IP addresses in addition to the attacker's (`ME`). This floods the target's logs with false sources, making it difficult for a security analyst to pinpoint the true attacker.
* **Timing Optimization:** `nmap -T1 10.10.1.5`
    * **Context:** Setting the timing template to **Paranoid (`-T1`)** makes the scan extremely slow (sending one probe every few minutes). This prevents pattern matching by many IDS/IPS systems but is extremely time-consuming.

---

## III. Phase 3: Post-Scan Enumeration and Vulnerability Assessment

The final phase uses the Nmap Scripting Engine (NSE) for targeted intelligence gathering based on the services discovered.

### A. Advanced NSE Script Categorization

| Category | Flag / Command | Purpose & Context |
| :--- | :--- | :--- |
| **Auth** | `--script auth` | Attempts to bypass authentication or run brute-force checks against services like FTP, SSH, and database logins. |
| **Vuln** | `--script vuln` | Aggressively checks for *known* vulnerabilities by sending exploit-like payloads. *Example:* Heartbleed, Shellshock, MS08-067 (SMB). |
| **Discovery** | `--script smb-enum*` | Used for deep domain enumeration. Discovers shares, users, security policies, and group memberships in Windows networks via the SMB protocol. |
| **Malware** | `--script malware` | Scans for backdoors or signs of infected systems (e.g., checking for connection patterns to known Command & Control servers). |

### B. Targeting Specific Services (Deep-Dive Examples)

* **Web Server Enumeration:** `nmap -p80,443 --script http-enum,http-headers,http-waf-detect 10.10.1.5`
    * **Rationale:** Checks for hidden files, common application paths, server headers (to fingerprint the platform), and attempts to detect the presence of a Web Application Firewall (WAF).
* **Database Service Scanning (MySQL/MSSQL):** `nmap -p3306 --script mysql-info,mysql-brute 10.10.1.5`
    * **Rationale:** Gathers version and configuration details, and immediately attempts to brute-force weak credentials, moving directly to exploitation.

---

## IV. Conclusion and Documentation

My preferred comprehensive scan to run in the background is:

```bash
# Full-scope, versioned, script-assisted scan, saving output to all formats.
nmap -T4 -sC -sV -p- 10.10.1.5 -oA full-scan-report-$(date +%Y%m%d).txt
