# My Journey: Learning Python for Security Automation

This document tracks my progress in learning Python. My goal is to build my own security tools from scratch to understand *how they work* at a deep level. This is my first "real" project: building a port scanner that mimics Nmap.

---

## Project: Python Multi-Port Scanner

This script is designed to ask the user for a target IP and then scan the 100 most common ports, reporting which ones are open.

### The Full Script

```python
#!/usr/bin/env python3

import socket
import sys
from datetime import datetime

# --- Configuration: Top 100 Ports ---
# I researched the most common ports to build this list.
COMMON_PORTS = [
    21, 22, 23, 25, 53, 67, 68, 69, 80, 110, 111, 113, 119, 123, 135, 137, 
    138, 139, 143, 161, 162, 179, 194, 389, 443, 445, 465, 514, 515, 548, 
    587, 636, 873, 990, 993, 995, 1080, 1194, 1352, 1433, 1434, 1521, 1701, 
    1723, 1755, 1812, 1813, 2049, 2082, 2083, 2086, 2087, 2095, 2096, 2181, 
    2222, 2483, 2484, 3074, 3306, 3389, 3690, 4333, 4664, 5060, 5061, 5432, 
    5555, 5800, 5900, 5985, 5986, 6000, 6001, 6379, 6667, 7071, 8000, 8008, 
    8080, 8081, 8443, 8888, 9090, 9100, 9200, 9300, 10000, 11211, 27017, 
    27018, 30000, 32768, 33333, 49152
]

# --- Main Program Logic ---

# Clear the screen for a clean look
print("\n" * 2) 
print("=" * 60)
print("           My First Python Port Scanner Tool")
print("=" * 60)

# 1. Get the target IP from the user
target_ip = input("Enter the IP address you want to scan: ")

# 2. Get the start time for the scan
start_time = datetime.now()

print(f"\n[+] Scanning target: {target_ip}")
print(f"[+] Scanning started at: {start_time.strftime('%Y-%m-%d %H:%M:%S')}")
print("Please wait, this may take a moment...")
print("-" * 60)

# --- This is the core logic ---
open_ports = []

try:
    # 3. This is the main loop. It will check every port in our list.
    for port in COMMON_PORTS:
        # 4. Create a new "socket" object for each connection attempt
        # AF_INET = IPv4 | SOCK_STREAM = TCP
        s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        
        # 5. Set a short timeout. If it can't connect in 0.5s, move on.
        s.settimeout(0.5)
        
        # 6. Try to connect. connect_ex() returns 0 if the port is OPEN.
        result = s.connect_ex((target_ip, port))
        
        if result == 0:
            # 7. If open, print it and add it to our list
            print(f"[***] Port {port} is OPEN")
            open_ports.append(port)
        
        # 8. Close the socket to clean up
        s.close()

except KeyboardInterrupt:
    print("\n[!] Scan cancelled by user.")
    sys.exit()
except socket.gaierror:
    print("\n[!] Error: Hostname could not be resolved.")
    sys.exit()
except socket.error:
    print("\n[!] Error: Could not connect to the server.")
    sys.exit()

# --- Scan Summary ---
print("-" * 60)
end_time = datetime.now()
total_time = end_time - start_time

print(f"[+] Scan Finished at: {end_time.strftime('%Y-%m-%d %H:%M:%S')}")
print(f"[+] Total scan duration: {total_time}")

if open_ports:
    print(f"\nSummary: Found {len(open_ports)} open port(s):")
    print(f"-> {open_ports}")
else:
    print(f"\nSummary: No open ports found in the top 100.")

print("=" * 60)
