# Penetration Testing Notes

---

# Network Security

Networks are subject to attacks from malicious sources. Attacks can be from two categories: “Passive” when a network intruder intercepts data traveling through the network, and “Active” in which an intruder initiates commands to disrupt the network's normal operation or to conduct reconnaissance and lateral movements to find and gain access to assets available via the network.

## Reconnaissance

Reconnaissance (recon) can be defined as a preliminary survey to gather information about a target. It is the first step in [The Unified Kill Chain](https://www.unifiedkillchain.com/) to gain an initial foothold on a system. We divide reconnaissance into:

1. Passive Reconnaissance
2. Active Reconnaissance

**Passive Recon:**

In passive reconnaissance, you rely on publicly available knowledge. It is the knowledge that you can access from publicly available resources without directly engaging with the target. Think of it like you are looking at target territory from afar without stepping foot on that territory.

### Whois

```bash
whois DOMAIN_NAME
```

Search for various public information about specific domain

### Nslookup

```bash
nslookup OPTIONS DOMAIN_NAME SERVER
```

Name Server Look Up. Find an IP address for its domain.

Some query type for nslookup:

| Query Type | Result |
| --- | --- |
| A | IPv4 Addresses |
| AAAA | IPv6 Addresses |
| CNAME | Canonical Name |
| MX | Mail Servers |
| SOA | Start of Authority |
| TXT | TXT Records |

e.g.

```bash
nslookup -type=A tryhackme.com 1.1.1.1
```

Look up only for IPv4 address for [tryhackme.com](http://tryhackme.com) on 1.1.1.1 cloudflare dns server.

### Dig

```bash
dig @SERVER DOMAIN_NAME TYPE
```

Domain Information Grouper, For more advanced DNS queries.

Some query type for dig (note that are the same as nslookup):

| Query Type | Result |
| --- | --- |
| A | IPv4 Addresses |
| AAAA | IPv6 Addresses |
| CNAME | Canonical Name |
| MX | Mail Servers |
| SOA | Start of Authority |
| TXT | TXT Records |

### DNSDumpster

[https://dnsdumpster.com/](https://dnsdumpster.com/)

### Shodan.io

[https://www.shodan.io/](https://www.shodan.io/)

---

**Active Recon:**

Active reconnaissance requires you to make some kind of contact with your target. This contact can be a phone call or a visit to the target company under some pretence to gather more information, usually as part of social engineering. Alternatively, it can be a direct connection to the target system, whether visiting their website or checking if their firewall has an SSH port open. Think of it like you are closely inspecting windows and door locks.

### Ping

```bash
ping MACHINE_IP
```

ping is a command that sends an ICMP Echo packet to a remote system. If the remote system is online, and the ping packet was correctly routed and not blocked by any firewall, the remote system should send back an ICMP Echo Reply. Similarly, the ping reply should reach the first system if appropriately routed and not blocked by any firewall.

### Traceroute

```bash
traceroute MACHINE_IP
```

traceroute command traces the route taken by the packets from your system to another host. The purpose of a traceroute is to find the IP addresses of the routers or hops that a packet traverses as it goes from your system to a target host. This command also reveals the number of routers between the two systems. It is helpful as it indicates the number of hops (routers) between your system and the target host.

### Telnet

```bash
telnet MACHINE_IP PORT
```

The TELNET (Teletype Network) protocol was developed in 1969 to communicate with a remote system via a command-line interface (CLI). Hence, the command telnet uses the TELNET protocol for remote administration. The default port used by telnet is 23. From a security perspective, telnet sends all the data, including usernames and passwords, in cleartext.

### Netcat

```bash
nc MACHINE_IP PORT
```

Netcat or simply nc supports both TCP and UDP protocols. It can function as a client that connects to a listening port; alternatively, it can act as a server that listens on a port of your choice. Hence, it is a convenient tool that you can use as a simple client or server over TCP or UDP.

On the *server* system, where you want to open a port and listen on it, you can issue `nc -vnlp PORT`

| Option | Meaning |
| --- | --- |
| -l | Listen mode |
| -p | Specify the Port number |
| -n | Numeric only; no resolution of hostnames via DNS |
| -v | Verbose output (optional, yet useful to discover any bugs) |
| -vv | Very Verbose (optional) |
| -k | Keep listening after client disconnects |

### Recap

| Purpose | Command line example |
| --- | --- |
| Lookup WHOIS record | whois tryhackme.com |
| Lookup DNS A records | nslookup -type=A tryhackme.com |
| Lookup DNS MX records at DNS server | nslookup -type=MX tryhackme.com 1.1.1.1 |
| Lookup DNS TXT records | nslookup -type=TXT tryhackme.com |
| Lookup DNS A records | dig tryhackme.com A |
| Lookup DNS MX records at DNS server | dig @1.1.1.1 tryhackme.com MX |
| Lookup DNS TXT records | dig tryhackme.com TXT |

| Command | Example |
| --- | --- |
| ping | ping -c 10 MACHINE_IP on Linux or macOS |
| ping | ping -n 10 MACHINE_IP on MS Windows |
| traceroute | traceroute MACHINE_IP on Linux or macOS |
| tracert | tracert MACHINE_IP on MS Windows |
| telnet | telnet MACHINE_IP PORT_NUMBER |
| netcat as client | nc MACHINE_IP PORT_NUMBER |
| netcat as server | nc -lvnp PORT_NUMBER |

source: [https://tryhackme.com/room/activerecon](https://tryhackme.com/room/activerecon), [https://tryhackme.com/room/passiverecon](https://tryhackme.com/room/passiverecon)

---

## Nmap

When we want to target a network, we want to find an efficient tool to help us handle repetitive tasks and answer the following questions:

1. Which systems are up?
2. What services are running on these systems?

The tool that we will rely on is Nmap.

### Host discovery

```bash
nmap -sn TARGETS
```

Discover online hosts without port-scanning (-sn) the live systems.

**Commands example table:**

| Scan Type | Example Command |
| --- | --- |
| ARP Scan | sudo nmap -PR -sn MACHINE_IP/24 |
| ICMP Echo Scan | sudo nmap -PE -sn MACHINE_IP/24 |
| ICMP Timestamp Scan | sudo nmap -PP -sn MACHINE_IP/24 |
| ICMP Address Mask Scan | sudo nmap -PM -sn MACHINE_IP/24 |
| TCP SYN Ping Scan | sudo nmap -PS22,80,443 -sn MACHINE_IP/30 |
| TCP ACK Ping Scan | sudo nmap -PA22,80,443 -sn MACHINE_IP/30 |
| UDP Ping Scan | sudo nmap -PU53,161,162 -sn MACHINE_IP/30 |

source: [https://tryhackme.com/room/nmap01](https://tryhackme.com/room/nmap01)

### Basic Port Scans

Type of scan

| Port Scan Type | Example Command |
| --- | --- |
| TCP Connect Scan | nmap -sT MACHINE_IP |
| TCP SYN Scan | sudo nmap -sS MACHINE_IP |
| UDP Scan | sudo nmap -sU MACHINE_IP |

Scan options

| Option | Purpose |
| --- | --- |
| -p- | all ports |
| -p1-1023 | scan ports 1 to 1023 |
| -F | 100 most common ports |
| -r | scan ports in consecutive order |
| -T<0-5> | T0 being the slowest and T5 the fastest |
| --max-rate 50 | rate <= 50 packets/sec |
| --min-rate 15 | rate >= 15 packets/sec |
| --min-parallelism 100 | at least 100 probes in parallel |

source: [https://tryhackme.com/room/nmap02](https://tryhackme.com/room/nmap02)

### Advanced Port Scans

| Port Scan Type | Example Command |
| --- | --- |
| TCP Null Scan | sudo nmap -sN MACHINE_IP |
| TCP FIN Scan | sudo nmap -sF MACHINE_IP |
| TCP Xmas Scan | sudo nmap -sX MACHINE_IP |
| TCP Maimon Scan | sudo nmap -sM MACHINE_IP |
| TCP ACK Scan | sudo nmap -sA MACHINE_IP |
| TCP Window Scan | sudo nmap -sW MACHINE_IP |
| Custom TCP Scan | sudo nmap --scanflags URGACKPSHRSTSYNFIN MACHINE_IP |
| Spoofed Source IP | sudo nmap -S SPOOFED_IP MACHINE_IP |
| Spoofed MAC Address | --spoof-mac SPOOFED_MAC |
| Decoy Scan | nmap -D DECOY_IP,ME MACHINE_IP |
| Idle (Zombie) Scan | sudo nmap -sI ZOMBIE_IP MACHINE_IP |
| Fragment IP data into 8 bytes | -f |
| Fragment IP data into 16 bytes | -ff |

source: [https://tryhackme.com/room/nmap03](https://tryhackme.com/room/nmap03)

### Post Port Scans

| Option | Meaning |
| --- | --- |
| -sV | determine service/version info on open ports |
| -sV --version-light | try the most likely probes (2) |
| -sV --version-all | try all available probes (9) |
| -O | detect OS |
| --traceroute | run traceroute to target |
| --script=SCRIPTS | Nmap scripts to run |
| -sC or --script=default | run default scripts |
| -A | equivalent to -sV -O -sC --traceroute |
| -oN | save output in normal format |
| -oG | save output in grepable format |
| -oX | save output in XML format |
| -oA | save output in normal, XML and Grepable formats |

source: [https://tryhackme.com/room/nmap04](https://tryhackme.com/room/nmap04)

---

## Protocols and Servers

### Telnet

The Telnet protocol is an application layer protocol used to connect to a virtual terminal of another computer. Using Telnet, a user can log into another computer and access its terminal (console) to run programs, start batch processes, and perform system administration tasks remotely.

### HTTP

Hypertext Transfer Protocol (HTTP) is the protocol used to transfer web pages (not only web pages). Your web browser connects to the webserver and uses HTTP to request HTML pages and images among other files and submit forms and upload various files. Anytime you browse the World Wide Web (WWW), you are certainly using the HTTP protocol.

### FTP

File Transfer Protocol (FTP) was developed to make the transfer of files between different computers with different systems efficient.

### SMTP, POP3 and IMAP

Email delivery over the Internet requires the following components:

1. Mail Submission Agent (MSA)
2. Mail Transfer Agent (MTA)
3. Mail Delivery Agent (MDA)
4. Mail User Agent (MUA)

Simple Mail Transfer Protocol (SMTP) is used to communicate with an MTA server.

Post Office Protocol version 3 (POP3) is a protocol used to download the email messages from a Mail Delivery Agent (MDA) server.

Internet Message Access Protocol (IMAP) is more sophisticated than POP3. IMAP makes it possible to keep your email synchronized across multiple devices (and mail clients).

### SSH

Secure Shell (SSH) was created to provide a secure way for remote system administration. In other words, it lets you securely connect to another system over the network and execute commands on the remote system.

### Common ports

| Protocol/Secured Protocol | Default Port / with TLS | Application |
| --- | --- | --- |
| FTP / FTPS | 21 / 990 | File Transfer |
| HTTP / HTTPS | 80 / 443 | Worldwide Web |
| IMAP / IMAPS | 143 / 993 | Email (MDA) |
| POP3 / POP3S | 110 / 995 | Email (MDA) |
| SMTP / SMTPS | 25 / 465 | Email (MTA) |
| Telnet / N/A | 23 / N/A | Remote Access |
| SSH | 22 | Secure Remote Access |
| SFTP | 22 | Secure File Transfer |