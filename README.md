# Network Scanning and Host Enumeration with Nmap

## Project Objective

Nmap is a "Network Mapper" used to scan networks.

## Tools and Technologies Used


### Running a Basic Host Scan

I will be doing this on my local machine in Powershell.

> Powershell
> ```powershell
> ipconfig
> ```
_This command displays all current TCP/IP network configuration values._

Output:

> <img src="images/1ipconfig.png" alt="ipconfig" width="50%">

My current IP is 10.71.250.126. The subnet is 10.71.240.0/20.

### Scanning for Active Devices

I will be scanning for active devices on scanme.nmap.org instead of my current IP.

> Powershell
> ```powershell
> nmap -sn scanme.nmap.org
> ```

`sn` is a ping scan. The `n` stands for no port scan. This command tells Nmap to only perform host discovery. It sends a ping (ICMP) to the target to see if it’s "alive" but stops right there.

Output:

> <img src="images/2ping.png" alt="Ping Scan" width="70%">

### Port Scan and Service Detection

Now we will run a deeper scan to identify open ports and services.

> Powershell
> ```powershell
> nmap -sV scanme.nmap.org
> ```

`-sV` is service/version detection: it probes open ports to identify exactly which software is running and its specific version number.

Output:

> <img src="images/3version.png" alt="Version Detection" width="70%">

Open ports:

- 22/tcp
- 80/tcp
- 9929/tcp
- 31337/tcp

Nmap reports: "Not shown: 996 closed tcp ports (reset)." This indicates that only 4 ports are open out of the 1000 scanned.

**Services:**

- Port 22: Used for Secure Shell (SSH), the industry-standard protocol for secure remote access to a server.
- Port 80: The standard port for unencrypted web traffic. It is used by web browsers to request and load traditional webpages.
- Port 9929: This is a non-standard port often associated with Nmap’s benchmarking or testing services.
- Port 31337: Famously known in the security community as the "Elite" (31337 = ELEET) port. It was historically used by various hacking groups and trojans as a default backdoor port.

**Version Information:**

- 22/tcp: OpenSSH — Commonly used for secure remote command-line access; the specific version (e.g., OpenSSH 8.2) identifies the software build and its associated security patches.
- 80/tcp: Hypertext Transfer Protocol (HTTP) — Frequently identified as Apache httpd, nginx, or a router's web management interface.
- 9929/tcp: nping-echo — A service specifically associated with Nmap's Nping echo tool, used for network troubleshooting and connectivity testing.
- 31337/tcp: Elite — Historically used by the Back Orifice trojan or other backdoor tools; its presence is a high-security risk often labeled as "Elite" in port databases.

Saving Results:

> Powershell
> ```powershell
> nmap -sV -oN scan_results.txt scanme.nmap.org
> ```

`-oN` scan_results.txt (Nmap) → Outputs the scan results in normal text format to a file
named scan_results.txt for later review or reporting

### Document Findings

> <img src="images/4sheets.png" alt="Findings in Sheets" width="70%">

10.71.250.126: The host is active and exposing four specific ports. Port 22 is running OpenSSH for secure remote management, while Port 80 provides a web interface, likely for a router or administrative console. Additionally, Port 9929 is active for Nping network testing, and Port 31337 is open, which is historically associated with the "Elite" backdoor or custom hacking tools. This combination of standard management ports and a high-risk "Elite" port suggests a system being used for technical testing or one that requires immediate security investigation.

### Investigate and Interpret

The IP address 10.71.250.126 identifies an active host on the network with a mix of expected and highly unusual services. While Port 22 (SSH) and Port 80 (HTTP) are standard for remote management and web interfaces, the presence of Port 9929 (Nping) and Port 31337 (Elite) stands out. The "Elite" port is particularly noteworthy as it is a classic indicator of a backdoor or unauthorized tool, which is not typically expected in a standard configuration. Fortunately, no legacy protocols like FTP or Telnet were detected, which reduces the risk of clear-text credential theft. However, the discovery of the "Elite" port highlights the need for further investigation to ensure the system hasn't been compromised or isn't running unauthorized scripts.

## Deeper Dive

### Operating System Detection

> Powershell
> ```powershell
> nmap -O scanme.nmap.org -oN task1_os_detection.txt
> ```

- Here we use the `-O` option to identify the operating system in use
- The `-oN` option saves formatted output to a file

> task1_os_detection.txt
>
> <img src="images/5task1.png" alt="Output file" width="70%">

#### OS Fingerprint Section 

> Powershell
> ```powershell
> Device type: general purpose|router
> Running: Linux 5.X, MikroTik RouterOS 7.X
> OS CPE: cpe:/o:linux:linux_kernel:5 cpe:/o:mikrotik:routeros:7 cpe:/o:linux:linux_kernel:5.6.3
> OS details: Linux 5.0 - 5.14, MikroTik RouterOS 7.2 - 7.5 (Linux 5.6.3)
> Network Distance: 17 hops
> ```

Here Nmap offered OS guesses within specified ranges

#### Record Key Info

| Field | Value |
| :--- | :--- |
| **Target IP** | 45.33.32.156 (scanme.nmap.org) |
| **Detected OS** | Linux 5.X, MikroTik RouterOS 7.X |
| **OS Accuracy** | Not provided |
| **Device type** | general purpose\|router |
| **MAC Address** | Not provided (Target is on a remote network) |
| **Network Distance** | 17 hops |
| **Suspicious Mismatch?** | No. The detected Linux 5.X kernel matches the expected configuration for a public web and testing server like scanme.nmap.org. |

**Verification Analysis:**

A follow-up verification is not required as the initial operating system detection scan successfully mapped the open ports. The presence of standard Linux-centric services—specifically SSH (Port 22) and Nping-echo (Port 9929)—is entirely consistent with Nmap's OS fingerprint identifying the target as a general-purpose Linux 5.X environment. No structural discrepancies or unexpected mismatches were detected between the active services and the host operating system.

### TCP SYN Scan

This is to quickly find open ports without establishing a full network connection

> Powershell
> ```powershell
> nmap -sS scanme.nmap.org -oN task2_syn_scan.txt
> ```

- `-sS` is for the TCP scan. It is stealthy and fast.
- `oN` saves the output to a standard text file

Script output:

> <img src="images/6task2.png" alt="TCP Scan output" width="70%">

- Here we notice that the scan took 3.90 seconds, which is faster than the version scan.
- Also notice that the results of the SYN scan matches that of the version scan.

#### Key Findings

| Port | State | Service | Comments |
| :--- | :--- | :--- | :--- |
| 22 | open | ssh | Secure Shell protocol is open for remote administrative command-line access. |
| 25 | filtered | smtp | Simple Mail Transfer Protocol is filtered, indicating a firewall is blocking or protecting mail services. |
| 80 | open | http | Standard web service port is open, serving the target's public webpage. |
| 113 | filtered | ident | Identification protocol is filtered by a security device or firewall. |
| 139 | filtered | netbios-ssn | NetBIOS session service is filtered, preventing exposure of legacy network sharing protocols. |
| 445 | filtered | microsoft-ds | Microsoft Directory Services (SMB) is filtered, protecting against network-level file sharing exploits. |
| 9929 | open | nping-echo | Active port used explicitly for Nmap’s Nping network connectivity and echo benchmarking. |
| 31337 | open | Elite | Non-standard port historically tied to trojans or backdoors; active on this host for testing identification. |

### UDP Scan

User Datagram Protocol scan finds which services are running on a target machine.

> Powershell
> ```powershell
> nmap -sU scanme.nmap.org -oN task3_udp_scan.txt
> ```

- `-sU` performs a UDP scan.

Script Output:

> <img src="images/7udp.png" alt="UDP Scan output" width="70%">

- UDP are slower due to being a connectionless protocol, meaning devices often don't reply. This causes Nmap to retry multiple times
- Notice that the scan took 1022.01 seconds (around 17 minutes)

`task3_udp_scan.txt`

> <img src="images/8task3.png" alt="task3 file output" width="70%">

### Findings

A total of 995 closed UDP ports returned an "ICMP port unreachable" message, indicating they are clearly closed. There are 4 ports marked as open|filtered; because Nmap received no response, it cannot determine if they are actually open or being blocked by a firewall. Meanwhile, 1 port is confirmed completely open (123/udp), which is running NTP (Network Time Protocol) to handle system clock synchronization. Combined, this accounts for all 5 anomalous ports flagged during the 1,022-second network sweep.

Extracted UDP Ports:

| Port | State | Service | Notes / Comments |
| :--- | :--- | :--- | :--- |
| 123/udp | open | ntp | Network Time Protocol is active and explicitly confirmed open; standard service used for network clock synchronization. |
| 67/udp | open\|filtered | dhcps | DHCP server port; unresponsive to standard probes, indicating it is either active or protected by an upstream firewall. |
| 68/udp | open\|filtered | dhcpc | DHCP client port; returned no response, leaving its exact state open or filtered by security rules. |
| 517/udp | open\|filtered | talk | Legacy chat protocol port; marked as open or filtered due to packet silence from the host. |
| 518/udp | open\|filtered | ntalk | Extended legacy talk protocol port; state is ambiguous as no diagnostic packets were returned. |
| 995 ports | closed | — | Returned explicit ICMP port-unreachable messages, confirming these network avenues are securely shut down. |

#### Interpretation

**Why is UDP scanning slow?**

UDP doesn’t establish connections like TCP does. When a UDP packet is sent:

- If the port is closed, the host responds with an ICMP port-unreachable message, which is easy to detect.
- If the port is open, the host often gives no response, so Nmap must retry multiple times and wait out long timeouts.
- If the port is filtered, a firewall drops packets silently, which also results in no response.

This is why the scan took 1022.01 seconds (over 17 minutes) and shows:

- 4 open|filtered ports
- Only 1 confirmed open port

**Why is Port 123/UDP open?**

Port 123/udp is designated for NTP (Network Time Protocol). Public servers, infrastructure hosts, and active network nodes use this service to automatically synchronize their local system clocks with highly accurate atomic time sources across the internet.

**Why no DNS (53/udp) or standard router management ports?**

Unlike a typical home router that handles local web management and DNS caching for local devices, scanme.nmap.org is a public-facing server meant for network testing. Based on the scan, it appears to:

- Filter or close local network management protocols like DHCP and standard DNS.
- Keep time synchronization (NTP) accessible while leaving legacy communication systems like talk/ntalk (517/518) completely silent or filtered behind a security firewall.

# Lessons Learned

# Contact & Links

- LinkedIn: www.linkedin.com/in/zenithsaw

- GitHub: https://github.com/ZenithSaw12
