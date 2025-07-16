![[Pasted image 20250712191810.jpg]]
TCP/IP (aka DoD or Department of Defense) model has four layers compared to [[OSI Model]]. It is **more practical, real-world, and used everywhere since it is considered as the basis of the internet** model whereas [[OSI Model]] is more theoretic model used for teaching and designs. It is also a family of protocols in each layer including:

|Layer|Protocol Examples|
|---|---|
|Application|HTTP, FTP, DNS, SMTP, SSH|
|Transport|TCP, UDP|
|Internet|IP, ICMP, ARP, IGMP|
|Network Access|Ethernet, Wi-Fi, PPP, Frame Relay|

## 3-Way Handshake
Recalling [[OSI Model#Transport Layer]] & [[OSI Model#TCP Header]], TCP must establish a connection before the sending of the data begin. In order to establish the connection, a series of packets must be sent in the following order.
![[67dc0504ffa42cac0579cfeb64227ccb.svg]]
![[6d8fe891-e992-42b7-a735-6ee2aeb7902b_2107x1318.webp]]
More information about the packets in [[OSI Model#Flags]].
*Note: Server do not ack every packet received, it instead use cumulative acks and delayed acks to optimize performance. So, it sends one ack for multiple received packets (usually 2 segments of data after slicing if needed, default behavior and can be customized) / after (usually) around ~200 ms if it is one segment, to let more data to arrive first and reduce overhead.*

Both parties must agree on the **same** **random Initial Sequence Number (ISN)** then increment it by 1 after each message. For example:
- Client (SYN): my ISN is 1000.
- Server (SYN/ACK): I ack ur sequence number is 1000 | my ISN is 5000 (it will send 1001 as ack).
- Client (ACK): I ack ur ISN is 5000 (client send 5001 as ack).
- Client (data): here is my first data with 1000 + 1 = 1001 sequence number. 
After finishing sending the data we close the connection in the following steps.
![[d29463eda80fa9e4cbe78b16aa5d9f87.svg]]

## UDP/IP Protocol
UDP [[OSI Model#Transport Layer]] & [[OSI Model#UDP Header]] is a stateless (doesn't require the connection to be established) protocol. Just throw the packets and there is not gurantee it will be received!

![[53d459ccda57e5fdea0dafe7e64ffe7c.svg]]

## Ports
Available ports are ranged between **0-65535**, where:
- **0-1023 are reserved** and known as common/well known ports
- **Registered ports 1024 - 49151**
- **Dynamic/Private ports 49152-65535** used by clients (not protocols/services) 

Most used ports:
[IANA port registry](https://www.iana.org/assignments/service-names-port-numbers/service-names-port-numbers.xhtml) for official mappings
*Note: apps doesn't necessarily follow the same port numbers, we can customize the port number like 8080 for http instead of 80 for http. However, apps will assume the standard that define the protocol is being followed and it must be specified in the url to be accessed like `mongodb://localhost:27018/mydb` and `http://site.com:8080`*

| **Port**  | **Protocol** | **Service / Application**                     |
| --------- | ------------ | --------------------------------------------- |
| 20        | TCP          | FTP (Data Transfer)                           |
| 21        | TCP          | FTP (Control Command)                         |
| 22        | TCP          | SSH (Secure Shell), SCP, SFTP                 |
| 23        | TCP          | Telnet                                        |
| 25        | TCP          | SMTP (Email Sending)                          |
| 53        | TCP/UDP      | DNS (Domain Name System)                      |
| 67        | UDP          | DHCP (Server)                                 |
| 68        | UDP          | DHCP (Client)                                 |
| 69        | UDP          | TFTP (Trivial File Transfer Protocol)         |
| 80        | TCP          | HTTP (Web Traffic)                            |
| 110       | TCP          | POP3 (Email Receiving)                        |
| 123       | UDP          | NTP (Network Time Protocol)                   |
| 137       | UDP          | NetBIOS Name Service                          |
| 138       | UDP          | NetBIOS Datagram Service                      |
| 139       | TCP          | NetBIOS Session Service                       |
| 143       | TCP          | IMAP (Email)                                  |
| 161       | UDP          | SNMP (Monitoring)                             |
| 162       | UDP          | SNMP Trap                                     |
| 179       | TCP          | BGP (Border Gateway Protocol)                 |
| 194       | TCP          | IRC (Internet Relay Chat)                     |
| 389       | TCP/UDP      | LDAP (Lightweight Directory Access Protocol)  |
| 443       | TCP          | HTTPS (Secure Web Traffic)                    |
| 445       | TCP          | SMB (Windows File Sharing)                    |
| 465       | TCP          | SMTP over SSL                                 |
| 514       | UDP          | Syslog (Logging)                              |
| 515       | TCP          | LPD (Line Printer Daemon)                     |
| 520       | UDP          | RIP (Routing Information Protocol)            |
| 546       | UDP          | DHCPv6 Client                                 |
| 547       | UDP          | DHCPv6 Server                                 |
| 587       | TCP          | SMTP with Authentication                      |
| 631       | TCP/UDP      | IPP (Internet Printing Protocol)              |
| 636       | TCP          | LDAP over SSL                                 |
| 993       | TCP          | IMAPS (IMAP Secure)                           |
| 995       | TCP          | POP3S (POP3 Secure)                           |
| 1080      | TCP          | SOCKS Proxy                                   |
| 1194      | UDP          | OpenVPN                                       |
| 1234      | UDP          | VLC Streaming / Custom apps                   |
| 1433      | TCP          | Microsoft SQL Server                          |
| 1434      | UDP          | Microsoft SQL Server Browser                  |
| 1521      | TCP          | Oracle Database                               |
| 1701      | UDP          | L2TP VPN                                      |
| 1723      | TCP          | PPTP VPN                                      |
| 1812      | UDP          | RADIUS Authentication                         |
| 1813      | UDP          | RADIUS Accounting                             |
| 1883      | TCP          | MQTT (IoT Messaging Protocol)                 |
| 2049      | TCP/UDP      | NFS (Network File System)                     |
| 2082      | TCP          | cPanel                                        |
| 2083      | TCP          | cPanel (SSL)                                  |
| 2222      | TCP          | DirectAdmin Panel                             |
| 2375      | TCP          | Docker Remote API (insecure)                  |
| 2376      | TCP          | Docker Remote API (secure)                    |
| 2483      | TCP          | Oracle DB (unsecure)                          |
| 2484      | TCP          | Oracle DB (secure)                            |
| 3306      | TCP          | MySQL Database                                |
| 3389      | TCP/UDP      | RDP (Remote Desktop Protocol)                 |
| 3690      | TCP          | Subversion (SVN)                              |
| 4000      | TCP/UDP      | Games / Custom Servers (e.g., Diablo, Unreal) |
| 4444      | TCP          | Metasploit, malware RATs                      |
| 4567      | TCP          | RADVD (Router Advertisement) / Custom apps    |
| 5000      | TCP          | UPnP / Flask Dev Server / Docker Registry     |
| 5001      | TCP          | iPerf                                         |
| 5060      | UDP          | SIP (VoIP signaling)                          |
| 5061      | TCP/UDP      | SIP over TLS                                  |
| 5432      | TCP          | PostgreSQL                                    |
| 5500      | TCP          | VNC (Alt Port)                                |
| 5631      | TCP          | PCAnywhere Data                               |
| 5632      | UDP          | PCAnywhere Control                            |
| 5900      | TCP          | VNC (Virtual Network Computing)               |
| 6000–6063 | TCP          | X11 (Linux GUI forwarding)                    |
| 6379      | TCP          | Redis                                         |
| 6443      | TCP          | Kubernetes API Server                         |
| 6667      | TCP          | IRC (Alternative ports)                       |
| 7000      | TCP          | File Servers / Cassandra DB / Custom          |
| 7070      | TCP          | RealAudio / Streaming                         |
| 8000      | TCP          | Web servers (alternative port), Shoutcast     |
| 8080      | TCP          | HTTP (alternative / proxy / dev servers)      |
| 8443      | TCP          | HTTPS (alternative / Tomcat / Spring Boot)    |
| 8888      | TCP          | Web dev, Jupyter Notebook                     |
| 9000      | TCP          | PHP-FPM, SonarQube, FastCGI                   |
| 9200      | TCP          | Elasticsearch HTTP API                        |
| 9300      | TCP          | Elasticsearch Transport                       |
| 9999      | TCP          | Debugging, Custom Services                    |
| 10000     | TCP          | Webmin                                        |
| 11211     | TCP/UDP      | Memcached                                     |
| 27017     | TCP          | MongoDB                                       |
| 50000     | TCP/UDP      | SAP / DB2                                     |
| 50070     | TCP          | Hadoop Web UI                                 |
