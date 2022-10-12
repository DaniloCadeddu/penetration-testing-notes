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

When we want to target a network, we want to find an efficient tool to help us handle repetitive tasks and answer the
    following questions:

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

**Commands example tables:**

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