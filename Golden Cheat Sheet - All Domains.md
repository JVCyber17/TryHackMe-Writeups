# Golden Cheat Sheet - All Domains

This highly detailed, task-focused guide serves as a quick operational reference for security professionals, covering the essential tools, methods, and strategic concepts across SOC Analysis, VAPT (Vulnerability Assessment and Penetration Testing), Incident Response, and Threat Intelligence.

## 1. Security Operations Center (SOC) & Defense Fundamentals

This section covers the core tasks related to daily monitoring, prevention, and security tool management.

| Daily Task / Operational Goal | Tool / Method / Concept | Detailed Application / Use Case |
|---|---|---|
| **Get a comprehensive, macro-level view of the entire network** | **SIEM** (Security Information and Event Management) | A system that aggregates, correlates, and analyzes logs from all network devices (firewalls, servers, routers) to provide a holistic view of security across the organization. |
| **Monitor and analyze activity on a specific user's laptop or server** | **EDR** (Endpoint Detection and Response) | A system focused on granular, real-time security on user devices (endpoints); essential for detecting and responding to file-less attacks and persistent threats directly. |
| **Decide which alerts to work on first** | **Alert Triage** (By Severity) | The critical process of prioritizing incoming alerts based on their severity (Critical, High, Medium, Low) to ensure the most damaging events are addressed immediately. |
| **Block a known malicious IP or domain** | **Firewall / IPS** (Intrusion Prevention System) | Preventative security devices set up to monitor network traffic and actively block recognized malicious activity, such as traffic coming from a known bad IP address. |
| **Develop a custom rule to detect a new attack pattern** | **IDS Signatures** (Basic) | Custom rules created for an Intrusion Detection System to identify and flag specific, known patterns of intrusion in network traffic or logs. |
| **Minimize the risk of system exploitation** | **Updating and Patching** | The essential maintenance task of ensuring all computers, servers, and network devices have the latest fixes to known software vulnerabilities (weaknesses) applied. |
| **Ensure all critical systems are known and tracked** | **Asset Management** | The foundational practice of identifying, documenting, and managing all valuable organizational hardware, software, and data that must be protected. |

## 2. Incident Response & Digital Forensics

This covers the actions taken when an alert is confirmed as a true security incident.

| Daily Task / Operational Goal | Tool / Method / Concept | Detailed Application / Use Case |
|---|---|---|
| **Stop the malicious activity from spreading immediately** | **Containment** (IR Phase) | The immediate first phase of the incident response lifecycle focused on isolating the affected system or segmenting the network to prevent the lateral spread of the threat. |
| **Find evidence of malware that never touched the hard drive** | **System Memory Analysis** | Taking a forensic image of the system’s volatile memory (RAM) to analyze malicious programs that run entirely in memory (file-less attacks). |
| **Look for installed backdoors, stolen credentials, or deleted files** | **File System Analysis** | Analyzing a digital forensic image (low-level copy) of a system's storage to uncover installed programs, created/deleted files, and other persistent artifacts. |
| **Ensure the malicious code is completely removed** | **Eradication** (IR Phase) | The stage focused on eliminating the threat after containment, ensuring all malicious files, backdoors, and persistence mechanisms are permanently removed. |
| **Return the system to a clean, working state** | **Recovery** (IR Phase) | The final stage of incident response where systems are fully restored to a secure, operational state and post-incident lessons are recorded. |

## 3. Threat Hunting, CTI & Malware Analysis

This section details how to enrich indicators of compromise (IOCs) and create custom detection logic.

| Daily Task / Operational Goal | Tool / Method / Concept | Detailed Application / Use Case |
|---|---|---|
| **Scan an unknown URL for malicious activity** | **UrlScan.io** | A free OSINT service used to automatically browse a submitted URL, recording network activity, contacted domains, technical metadata, and visual snapshots of the page. |
| **Check if a file hash is a known sample of malware** | **Abuse.ch (Malware Bazaar / Threat Fox)** | A centralized research project that hosts platforms for tracking and sharing known malware samples and indicators of compromise (IOCs) associated with botnets. |
| **Analyze metadata, attachments, and links from a phishing email** | **PhishTool** | A specialized tool designed for email analysis to retrieve and analyze all forensic data from phishing emails, aiding in rapid triage and reporting. |
| **Perform a global lookup on an IP address or file SHA256** | **Cisco Talos Intelligence** | An open-source intelligence platform providing searchable global threat data, vulnerability reports (CVEs), and reputation lookups for IP addresses and files. |
| **Create a custom signature for new malware detection** | **YARA Rules** (Pattern Matching) | A powerful signature-based language used to identify and classify malware based on unique internal strings, hex patterns, or binary structures across files or memory samples. |
| **Define the unique code markers in a YARA rule** | **YARA Strings Section** | The part of the rule that specifies the unique, identifiable patterns (text, hex, or regex) that must be found within the file for a match to occur. |
| **Define the logical constraints for a YARA rule** | **YARA Condition Section** | Defines the necessary criteria for the rule to trigger (e.g., must check for high **Entropy** suggesting encryption, or require specific strings to be present). |
| **Understand the company's long-term risk profile** | **Strategic Intelligence** | High-level reports aimed at leadership, focusing on broad industry trends and emerging threat actors that could impact business decisions. |
| **Get low-level IPs or hashes to block right now** | **Technical Intelligence** | Atomic, fleeting indicators of compromise (IOCs) such as malicious IP addresses, domains, and file hashes used for immediate operational blocking. |

## 4. Offensive Operations & Vulnerability Testing (VAPT)

This section covers the basic tasks and roles involved in the adversarial mindset and initial access.

| Daily Task / Operational Goal | Tool / Method / Concept | Detailed Application / Use Case |
|---|---|---|
| **Find hidden website pages during reconnaissance** | **Gobuster** | A brute-forcing tool used during initial reconnaissance to quickly find hidden paths, files, or sensitive administrative pages on a web server. |
| **Interact directly with a compromised system** | **Terminal / Command Line** | The primary non-graphical interface necessary to execute commands, scripts, and specialized offensive utilities on remote or local machines. |
| **Be hired to find exploitable weaknesses** | **Penetration Tester Role** | A professional role responsible for testing technology products and systems to find exploitable security vulnerabilities and report them to the client. |
| **Simulate a persistent, highly skilled attacker** | **Red Teamer Role** | Plays the role of a sophisticated adversary, attacking an organization over time to provide objective feedback on the organization's defensive capability. |

## 5. Strategic Frameworks (Modeling Attacks)

These models are used to map, classify, and communicate threats clearly across teams.

| Daily Task / Operational Goal | Framework / Concept | What It Is / When to Use It |
|---|---|---|
| **Map the 7 stages of a linear attack timeline** | **Cyber Kill Chain (CKC)** | A seven-stage model that describes the progression of an attack from initial *Reconnaissance* to final *Actions on Objectives*. |
| **Categorize the attacker's ultimate objective (The "Why")** | **MITRE ATT\&CK Tactic** | The high-level goal of the adversary, such as *Initial Access*, *Execution*, or *Exfiltration*. |
| **Define the specific action taken (The "How")** | **MITRE ATT\&CK Technique** | The precise technical method the adversary used to achieve a goal, such as using *PowerShell* or performing *OS Credential Dumping*. |
| **Identify who, what, how, and why the attack happened** | **Diamond Model** | A qualitative analysis framework used to define the four core elements of any intrusion: **Adversary**, **Infrastructure**, **Capability**, and **Victim**. |
| **Analyze post-breach activity (Lateral Movement, etc.)** | **UKC "Through" Phase** | The intermediate phase of the **Unified Kill Chain** that focuses specifically on internal activities like **Discovery**, **Privilege Escalation**, and **Lateral Movement** after initial access is gained. |
| **Force the attacker to change their entire methodology** | **Pyramid of Pain (TTPs - Apex)** | Focusing on detecting and blocking **Tactics, Techniques, and Procedures** (TTPs); this is the most costly change for the adversary and provides the highest defensive value. |
| **Block easy indicators that are quick to replace** | **Pyramid of Pain (Hashes/IPs - Base)** | Technical indicators like File **Hashes** or **IP Addresses** are the least effective things to block, as the adversary can easily change them by recompiling code or switching servers. |
