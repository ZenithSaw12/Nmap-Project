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

`sV` is service/version detection: it probes open ports to identify exactly which software is running and its specific version number.

Output:

> <img src="images/3version.png" alt="Version Detection" width="70%">

Open ports:

- 22/tcp
- 80/tcp
- 9929/tcp
- 31337/tcp

Nmap reports: "Not shown: 996 closed tcp ports (reset)." This indicates that only 4 ports are open out of the 1000 scanned.

Services:

- Port 22: Used for Secure Shell (SSH), the industry-standard protocol for secure remote access to a server.
- Port 80: The standard port for unencrypted web traffic. It is used by web browsers to request and load traditional webpages.
- Port 9929: This is a non-standard port often associated with Nmap’s benchmarking or testing services.
- Port 31337: Famously known in the security community as the "Elite" (31337 = ELEET) port. It was historically used by various hacking groups and trojans as a default backdoor port.

Version Information:

- 22/tcp: OpenSSH — Commonly used for secure remote command-line access; the specific version (e.g., OpenSSH 8.2) identifies the software build and its associated security patches.
- 80/tcp: Hypertext Transfer Protocol (HTTP) — Frequently identified as Apache httpd, nginx, or a router's web management interface.
- 9929/tcp: nping-echo — A service specifically associated with Nmap's Nping echo tool, used for network troubleshooting and connectivity testing.
- 31337/tcp: Elite — Historically used by the Back Orifice trojan or other backdoor tools; its presence is a high-security risk often labeled as "Elite" in port databases.

Saving Results:

> Powershell
> ```powershell
> nmap -sV -oN scan_results.txt scanme.nmap.org
> ```

oN scan_results.txt (Nmap) → Outputs the scan results in normal text format to a file
named scan_results.txt for later review or reporting
