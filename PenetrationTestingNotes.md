# Penetration Testing Notes

# **Reconnaissance**

Reconnaissance (recon) can be defined as a preliminary survey to gather information about a target. It is the first step in [The Unified Kill Chain](https://www.unifiedkillchain.com/) to gain an initial foothold on a system. We divide reconnaissance into:

1. Passive Reconnaissance
2. Active Reconnaissance

## Passive recon

In passive reconnaissance, you rely on publicly available knowledge. It is the knowledge that you can access from publicly available resources without directly engaging with the target. Think of it like you are looking at target territory from afar without stepping foot on that territory.

### Whois

```bash
whois DOMAIN_NAME
```

### Nslookup

```bash
nslookup OPTIONS DOMAIN_NAME SERVER
```

some query type for nslookup:

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

### Dig

```bash
dig @SERVER DOMAIN_NAME TYPE
```

some query type for dig (note that are the same as nslookup):

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

source: [https://tryhackme.com/room/passiverecon](https://tryhackme.com/room/passiverecon)

---

## Active recon

Active reconnaissance requires you to make some kind of contact with your target. This contact can be a phone call or a visit to the target company under some pretence to gather more information, usually as part of social engineering. Alternatively, it can be a direct connection to the target system, whether visiting their website or checking if their firewall has an SSH port open. Think of it like you are closely inspecting windows and door locks.

### Ping

```bash
ping MACHINE_IP
```

### Traceroute

```bash
traceroute MACHINE_IP
```

### Telnet

```bash
telnet MACHINE_IP PORT
```

### Netcat

```bash
nc MACHINE_IP PORT
```

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

| Command | Example |
| --- | --- |
| ping | ping -c 10 MACHINE_IP on Linux or macOS |
| ping | ping -n 10 MACHINE_IP on MS Windows |
| traceroute | traceroute MACHINE_IP on Linux or macOS |
| tracert | tracert MACHINE_IP on MS Windows |
| telnet | telnet MACHINE_IP PORT_NUMBER |
| netcat as client | nc MACHINE_IP PORT_NUMBER |
| netcat as server | nc -lvnp PORT_NUMBER |

source: [https://tryhackme.com/room/activerecon](https://tryhackme.com/room/activerecon)

---

# **Scanning**

// TODO

# **Vulnerability Assessment**

// TODO

# Exploitation

// TODO

# **Reporting**

// TODO