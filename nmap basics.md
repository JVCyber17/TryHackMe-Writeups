# Nmap Deep Dive & Command Cheatsheet

This document is my personal research and reference guide for Nmap (Network Mapper).

## What is Nmap?
Nmap is an open-source tool for network discovery and security auditing. It's used to discover hosts and services on a computer network by sending packets and analyzing the responses. It is the single most important tool for the *Reconnaissance* and *Enumeration* phases of a penetration test.

## My Nmap Workflow (The 5 Stages)

I am learning to structure my scans in a logical flow, moving from "loud and fast" to "slow and detailed."

---

### Stage 1: Host Discovery (Finding Live Computers)

**Goal:** To find out which IP addresses on the network are "alive" and responsive.

* **Ping Scan (No Port Scan)**
    * **Command:** `nmap -sn 192.168.1.0/24`
    * **Research:** The `-sn` flag tells Nmap *not* to do a port scan. It only performs host discovery. This is fast and efficient for just getting a list of live targets on a subnet (like `192.168.1.0/24`, which is all IPs from `.1` to `.254`).

* **ARP Scan**
    * **Command:** `nmap -PR -sn 192.168.1.0/24`
    * **Research:** This is my preferred method for local networks. It uses ARP requests, which is very reliable for finding hosts on the same LAN. It's much faster than a standard ping.

---

### Stage 2: Initial Port Scanning (Finding Open Doors)

**Goal:** To quickly find the most common open ports on a *single* live target.

* **Default TCP SYN Scan**
    * **Command:** `nmap 10.10.1.5`
    * **Research:** By default, Nmap runs a **TCP SYN Scan** (also called a "half-open" scan). This is the `-sS` flag. It sends a SYN packet (like knocking) and if it gets a SYN/ACK back (the door opening), it sends an RST (running away) instead of completing the connection. This is fast and stealthy. This command scans the top 1000 most common ports.

* **Fast Scan (Top 100 Ports)**
    * **Command:** `nmap -F 10.10.1.5`
    * **Research:** The `-F` flag selects the "Fast" scan mode, which only scans the top 100 most common ports instead of 1000. I use this when I am in a hurry and just want to see if a web server (80, 443) or SSH (22) is open.

* **Scan ALL 65,535 Ports**
    * **Command:** `nmap -p- 10.10.1.5`
    * **Research:** The `-p-` flag is a shortcut for `-p 1-65535`. This is the *most thorough* port scan, as it will check every single possible TCP port. This can take a very long time, so I run it in the background while I investigate other things.

* **Scan Specific Ports**
    * **Command:** `nmap -p 22,80,443,3389 10.10.1.5`
    * **Research:** This is extremely useful when I already know what I'm looking for. It's targeted and very fast.

---

### Stage 3: Service & Version Detection (Looking Inside the Doors)

**Goal:** To find out *what software and version* is running on the open ports. This is the most important step for finding vulnerabilities.

* **Service & Version Scan**
    * **Command:** `nmap -sV -p 80,443 10.10.1.5`
    * **Research:** The `-sV` flag is my most-used command. It probes the open ports to grab banners and responses. Instead of just "Port 80 is open," it will tell me, "Port 80 is open and running **Apache httpd 2.4.18**." This version number is what I use to search for public exploits.

---

### Stage 4: Script Scanning (Nmap Scripting Engine - NSE)

**Goal:** To automatically run scripts against the services I found to check for common misconfigurations or known vulnerabilities.

* **Default "Safe" Scripts**
    * **Command:** `nmap -sC 10.10.1.5`
    * **Research:** The `-sC` flag runs the default set of scripts. These are considered "safe" (won't crash the target) and are very useful. It can find things like anonymous FTP logins, web server titles, and more.

* **Vulnerability Scanning Scripts**
    * **Command:** `nmap --script vuln 10.10.1.5`
    * **Research:** This is a *very powerful* (and loud) command. It tells Nmap to run all scripts in its database that are tagged as "vuln." This will actively check for known vulnerabilities like Heartbleed, SMB guest access, etc. This is a mini-vulnerability scanner in itself.

---

### Stage 5: Putting It All Together (My Go-To Scans)

**Goal:** To combine these techniques into efficient, single commands.

* **My "Quick & Dirty" Scan:**
    * **Command:** `nmap -T4 -F 10.10.1.5`
    * **Purpose:** To quickly see if the top 100 ports are open. The `-T4` makes it run faster ("Aggressive" timing).

* **My "Full Enum" Scan:**
    * **Command:** `nmap -sC -sV -p- 10.10.1.5 -oN nmap-full.txt`
    * **Purpose:** This is my "go-to" scan when I'm serious.
        * `-sC`: Run default scripts.
        * `-sV`: Find service versions.
        * `-p-`: Scan ALL ports.
        * `-oN nmap-full.txt`: **Save the output** to a text file named `nmap-full.txt` so I don't lose my results.
