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
