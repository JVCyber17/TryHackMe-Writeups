# Python Concurrency for High-Performance Network Scanning

This project documents the design and implementation of an advanced Python port scanner, demonstrating an understanding of **concurrency** through the `threading` library. A major limitation of my initial scanner was that it checked ports one-by-one (synchronously). This threaded version checks multiple ports *simultaneously*, significantly improving performance and demonstrating a deeper level of programming skill.

---

## I. Project Architecture: The Threaded Scanner

The Master's-level improvement here is the transition from a simple `for` loop to a **Thread Pool**. Instead of waiting for Port 1 to finish before checking Port 2, the script instantly creates small, lightweight "Worker Threads" that execute the check concurrently.

### A. Key Libraries and Concepts

| Concept | Python Library | Rationale |
| :--- | :--- | :--- |
| **Concurrency** | `threading` | Allows the script to run multiple network checks at the same time, drastically reducing scan time. This is how high-performance tools like Nmap operate. |
| **Locking** | `threading.Lock` | Essential for concurrent programs. Prevents "race conditions" where multiple threads try to write data (e.g., to the same output file or variable) simultaneously, causing data corruption. |
| **Error Handling** | `socket` & `sys` | Robust `try/except` blocks are critical when dealing with unstable network connections. |

### B. The Full Script (Threaded Port Scanner)

```python
#!/usr/bin/env python3

import socket
import threading
from queue import Queue
from datetime import datetime
import sys

# --- GLOBAL CONFIGURATION AND CONSTANTS ---
# Lock object to ensure clean output when multiple threads write to the console.
print_lock = threading.Lock() 

# The queue size limits the number of ports being actively checked at once.
# Limiting this prevents resource exhaustion.
THREAD_LIMIT = 50 

TARGET_IP = input("[?] Enter Target IP Address: ")
START_PORT = int(input("[?] Enter Starting Port: "))
END_PORT = int(input("[?] Enter Ending Port: "))

# --- CORE FUNCTIONALITY: THE SCANNER WORKER ---

def port_scanner(port):
    """
    Worker function executed by each thread to check a single port.
    """
    try:
        # 1. Create a socket object (AF_INET for IPv4, SOCK_STREAM for TCP)
        s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        s.settimeout(0.5)  # Fast timeout for rapid scanning

        # 2. Attempt connection: 0 on success, non-zero on failure
        result = s.connect_ex((TARGET_IP, port))
        
        # 3. Use the Lock to safely print the result
        if result == 0:
            with print_lock:
                # Use socket.getservbyport to identify the service name (e.g., 'http')
                service = socket.getservbyport(port, "tcp")
                print(f"[***] OPEN  -> Port {port:<5} | Service: {service}")
        
        s.close()
    
    # 4. Handle connection or network errors gracefully
    except socket.error as e:
        with print_lock:
            # We specifically catch and document network errors
            print(f"[-] ERROR -> Port {port} : {e}")
            
# --- CONCURRENCY MANAGER ---

def threader():
    """
    Retrieves ports from the queue and assigns them to an available worker thread.
    """
    while True:
        # Get the next port from the queue
        worker_port = q.get()
        # Run the port scanner function for that port
        port_scanner(worker_port)
        # Signal to the queue that the task is finished
        q.task_done()

# --- MAIN EXECUTION BLOCK ---

if __name__ == '__main__':
    
    # Start the timing
    start_time = datetime.now()
    print("-" * 60)
    print(f"[*] Starting Scan on Target: {TARGET_IP}")
    print(f"[*] Scanning Ports {START_PORT} to {END_PORT} with {THREAD_LIMIT} threads...")
    print("-" * 60)

    # Initialize the Queue object
    q = Queue()

    # Create the worker threads
    for _ in range(THREAD_LIMIT):
        # target=threader: Tells the thread to run the threader function
        # daemon=True: Allows the main program to exit even if threads are running
        t = threading.Thread(target=threader, daemon=True) 
        t.start()

    # Populate the queue with the ports to be scanned
    for port in range(START_PORT, END_PORT + 1):
        q.put(port)
        
    # Wait for the queue to be fully processed before calculating time
    q.join() 

    # --- Print Summary ---
    end_time = datetime.now()
    total_time = end_time - start_time
    print("-" * 60)
    print(f"[+] Scan Finished. Duration: {total_time}")
    print("-" * 60)
