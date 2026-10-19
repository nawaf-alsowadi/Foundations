# Security+ Assisted Lab: Finding Open Service Ports — Summary


## Exercise 1 — Border Firewall Scan (External/Internet Subnet)

**Setup:** Sign into KALI as `root` (password `Pa$$w0rd`), open elevated Terminal.

```
nmap 203.0.113.1 -F -sS -sV -O -Pn -oN border-scan.nmap
```

> [!important] Flag breakdown
> 
> - `-F` — Fast scan; limits to the top 100 most common ports instead of the full 1,000+ default set
> - `-sS` — SYN scan (the default/stealth scan type); simulates a TCP handshake without completing it, giving the most reliable read on open ports
> - `-sV` — Version detection; attempts to identify the specific service/version running on each open port
> - `-O` — OS detection; fingerprints the target's operating system
> - `-Pn` — Disables host discovery (skip ping); treats the target as up/active regardless of ping response — necessary here since the target is behind a firewall that may block ICMP
> - `-oN border-scan.nmap` — Saves normal-format output to the named file, in addition to displaying it on screen

**Filter open ports:**

```
grep open border-scan.nmap
```

**Result:** Open ports — 21, 22, 25, 53, 80, 443, 3389

**Filter OS results:**

```
grep OS border-scan.nmap
```

**Result:** OS detected — Windows

> [!warning] Analysis: Internet-exposed ports like 25 (SMTP) and 80 (HTTP) are attack targets if the underlying services carry vulnerabilities. Port 22 (SSH) being open externally raises the question of whether remote admin access from the internet is actually warranted — if not, it should be closed. OS fingerprinting also gives an adversary a head start on selecting OS-specific exploits; minimizing this exposure reduces risk.

---

## Exercise 2 — Guest Network Scan

**Setup — switch Kali's network location:**

1. Resources tab → Kali → eth0 dropdown → select `vGUEST`
2. Back in Terminal:

```
dhclient -r && dhclient
```

> [!note] Command explanation `dhclient -r` releases the current DHCP lease/IP; `dhclient` (chained with `&&`) then requests a new one — used here to move Kali onto the new network segment's IP range.

```
ip a s eth0
```

> [!note] Command explanation Displays IP configuration (address, subnet, state) for interface `eth0` only — used to confirm the new network assignment.

**Scan the guest gateway:**

```
nmap 192.168.16.254 -F -sS -sV -O -oN guest-scan.nmap
```

(Same flags as before — `-F`, `-sS`, `-sV`, `-O`, `-oN` — but **no `-Pn`** this time, since host discovery isn't disabled on this internal scan.)

**Filter open ports:**

```
grep open guest-scan.nmap
```

**Result:** Ports 80, 443, and 8000 open, all identified as the **firewall's management interface**.

**Filter OS:**

```
grep OS guest-scan.nmap
```

**Result:** OS detected — Linux

> [!warning] Analysis: Guest network users should never be able to reach the firewall's management interface — these ports (80/443/8000) should be closed on that segment. Some ports should still legitimately remain open for guest use (DNS 53, web 80/443, mail: SMTP 25/465/587, POP3 110/995, IMAP 143/993) — the concern here is specifically _management-plane_ access being reachable from an untrusted network.

---

## Exercise 3 — Internal Server Scan (from Client Network)

**Setup — switch Kali again:**

1. Resources tab → Kali → eth0 dropdown → select `vLAN_CLIENTS`
2. Same commands as before:

```
dhclient -r && dhclient
ip a s eth0
```

**Scan the legacy server (positioned in Client subnet, scanning a target in the Server subnet):**

```
nmap 10.1.16.2 -F -sS -sV -O -oN server-scan.nmap
```

**Filter open ports:**

```
grep open server-scan.nmap
```

**Result — services found:** FTP, Mountd (NFS), RDP, SMTP, HTTP, Microsoft-DS, IMAP, MySQL, MSRPC, NTP

**Filter OS:**

```
grep OS server-scan.nmap
```

**Result:** Windows Server 2016 (EOL Jan 11, 2022; EOSL Jan 12, 2027)

> [!warning] Analysis: A large number of exposed services on an internal host is significant — each needs justification, and any necessary service should use encrypted communication even internally. Critically, the fact that **all** of this is visible/enumerable from the Client network indicates **no firewall/segmentation** between Client and Server subnets — a real internal threat vector, since insiders can cause as much damage as external attackers. The server's OS is also past EOL, meaning it's unsupported (though still receiving security patches until EOSL in 2027) — a strong candidate for replacement.

---

## Comprehensive Review Q&A

|#|Question|Answer|
|---|---|---|
|1|What is a threat vector?|A pathway that could support an intrusion attempt|
|2|What is an attack surface?|The collection of exposed vulnerabilities|
|3|Which nmap flag performs service identification?|`-sV`|
|4|Where can threats originate?|All of the above (external, internal, third-party software)|
|5|Two primary responses to an insecure open port?|Configure service encryption; Close the exposed port|

---

## Key Comparison: Scans Across the Three Exercises

| Aspect           | Border (Internet)                               | Guest Network                               | Client → Server                                     |
| ---------------- | ----------------------------------------------- | ------------------------------------------- | --------------------------------------------------- |
| Target           | 203.0.113.1 (firewall)                          | 192.168.16.254 (gateway)                    | 10.1.16.2 (legacy server)                           |
| `-Pn` used?      | Yes (host discovery disabled)                   | No                                          | No                                                  |
| Open ports found | 21,22,25,53,80,443,3389                         | 80,443,8000 (mgmt interface)                | 10 services incl. FTP, RDP, MySQL, SMB              |
| OS detected      | Windows                                         | Linux                                       | Windows Server 2016 (EOL)                           |
| Core takeaway    | Internet-facing exposure = direct attack target | Guest segment shouldn't reach firewall mgmt | No Client↔Server segmentation = major internal risk |
