![[Pasted image 20250811173456.png]]

![[Pasted image 20250811175034.png]]
## Top-Level Domain (TLD):
The most righthand in the domain
	www . example **.com**

**TLD Types**
- Generic TLD (**gTLD**):
Meant to tell users what is the purpose of the domain.
	.com company
	.gov government
	.org organization
	.edu education
- Country Code TLD (**ccTLD**):
Meant to tell the geolocation of the domain.
	.qa Qatra
	.uk United Kingdom
	.usa united states of america
For full list of all TLDs: use [this](https://data.iana.org/TLD/tlds-alpha-by-domain.txt)

## Second Level Domain (SLD)
The one right before TLD. It is the actual domain name.
	www. **example** .com
It is limited by 63 characters (not counting TLD) and only take a-z, 0-9, and hyphens (-, but cannot start, end, or consecutive)

## Subdomain
The one right before SLD. It is a subdomain that is used for a specific purpose.
	www. **admin** .example.com
Same restrictions in [[#Second Level Domain (SLD)]] are applied here. We can use multiple subdomains to have longer names:
	www. **super-admin** .admin.example.com
But the **max total length of subdomains + SLD is 253**. But no limit in how many subdomains we can have (obviously 253 is the max)

## DNS Records

| Record Type                  | Purpose                                                                                                                                                                                                                                                                                                                                    | When You See/Use It                                                    | How to View It (Command & Sample Output)                                                                                                                                        | Example Record                                                                                                                                           |
| ---------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **A**                        | Maps a domain to an IPv4 address.                                                                                                                                                                                                                                                                                                          | When you open a website; almost every domain has one unless IPv6-only. | **Command:** `dig A example.com`**Output:**`<br>;; ANSWER SECTION:<br>example.com. 3600 IN A 93.184.216.34<br>`                                                                 | `example.com. IN A 93.184.216.34`                                                                                                                        |
| **AAAA**                     | Maps a domain to an IPv6 address.                                                                                                                                                                                                                                                                                                          | When the domain supports IPv6 connectivity.                            | **Command:** `dig AAAA example.com`**Output:**`<br>;; ANSWER SECTION:<br>example.com. 3600 IN AAAA 2606:2800:220:1:248:1893:25c8:1946<br>`                                      | `example.com. IN AAAA 2606:2800:220:1:248:1893:25c8:1946`                                                                                                |
| **CNAME**                    | Alias from one domain to another.                                                                                                                                                                                                                                                                                                          | When a subdomain should point to another hostname.                     | **Command:** `dig CNAME www.example.com`**Output:**`<br>;; ANSWER SECTION:<br>www.example.com. 3600 IN CNAME example.com.<br>`                                                  | `www.example.com. IN CNAME example.com.` example online shop has the subdomain name `store.example.com` which returns a CNAME record `shops.shopify.com` |
| **MX**                       | Resolve to the address of the servers that handle the emails.                                                                                                                                                                                                                                                                              | When sending/receiving email.                                          | **Command:** `dig MX example.com`**Output:**`<br>;; ANSWER SECTION:<br>example.com. 3600 IN MX 10 mail.example.com.<br>`                                                        | `example.com. IN MX 10 mail.example.com.`                                                                                                                |
| **NS**                       | Authoritative name servers.                                                                                                                                                                                                                                                                                                                | When delegating a domain to DNS hosting.                               | **Command:** `dig NS example.com`**Output:**`<br>;; ANSWER SECTION:<br>example.com. 172800 IN NS ns1.example.com.<br>example.com. 172800 IN NS ns2.example.com.<br>`            | `example.com. IN NS ns1.example.com.`                                                                                                                    |
| **PTR**                      | Reverse DNS: IP → hostname.                                                                                                                                                                                                                                                                                                                | For server identity verification.                                      | **Command:** `dig -x 93.184.216.34`**Output:**`<br>;; ANSWER SECTION:<br>34.216.184.93.in-addr.arpa. 86400 IN PTR example.com.<br>`                                             | `34.216.184.93.in-addr.arpa. IN PTR example.com.`                                                                                                        |
| **SOA**(more digging needed) | Start of Authority; zone details.                                                                                                                                                                                                                                                                                                          | When looking at zone admin settings.                                   | **Command:** `dig SOA example.com`**Output:**`<br>;; ANSWER SECTION:<br>example.com. 3600 IN SOA ns1.example.com. admin.example.com. ( 2025081101 7200 3600 1209600 3600 )<br>` | `example.com. IN SOA ns1.example.com. admin.example.com. (2025081101 7200 3600 1209600 3600)`                                                            |
| **SRV**                      | Service location record.                                                                                                                                                                                                                                                                                                                   | For SIP, XMPP, game servers, etc.                                      | **Command:** `dig SRV _sip._tcp.example.com`**Output:**`<br>;; ANSWER SECTION:<br>_sip._tcp.example.com. 3600 IN SRV 10 60 5060 sipserver.example.com.<br>`                     | `_sip._tcp.example.com. IN SRV 10 60 5060 sipserver.example.com.`                                                                                        |
| **TXT**                      | Stores free text fields info (SPF, DKIM, verification). Some common use cases can be to list servers that have the authority to send an email on behalf of the domain (this can help in the battle against spam and spoofed email). They can also be used to verify ownership of the domain name when signing up for third party services. | For domain verification or email security.                             | **Command:** `dig TXT example.com`**Output:**`<br>;; ANSWER SECTION:<br>example.com. 3600 IN TXT "v=spf1 include:_spf.example.com ~all"<br>`                                    | `example.com. IN TXT "v=spf1 include:_spf.example.com ~all"`                                                                                             |
| **CAA**                      | Restrict which CAs can issue certs.                                                                                                                                                                                                                                                                                                        | For SSL/TLS security.                                                  | **Command:** `dig CAA example.com`**Output:**`<br>;; ANSWER SECTION:<br>example.com. 3600 IN CAA 0 issue "letsencrypt.org"<br>`                                                 | `example.com. IN CAA 0 issue "letsencrypt.org"`                                                                                                          |
| **NAPTR**                    | Advanced name mapping for services.                                                                                                                                                                                                                                                                                                        | For VoIP, ENUM, etc.                                                   | **Command:** `dig NAPTR example.com`**Output:**`<br>;; ANSWER SECTION:<br>example.com. 3600 IN NAPTR 100 50 "U" "E2U+sip" "!^.*$!sip:info@example.com!" .<br>`                  | `example.com. IN NAPTR 100 50 "U" "E2U+sip" "!^.*$!sip:info@example.com!" .`                                                                             |
| **DS**                       | DNSSEC Delegation Signer.                                                                                                                                                                                                                                                                                                                  | When enabling DNSSEC.                                                  | **Command:** `dig DS example.com`**Output:**`<br>;; ANSWER SECTION:<br>example.com. 3600 IN DS 12345 8 2 49FD46E6C4B45C55D4AC...<br>`                                           | `example.com. IN DS 12345 8 2 49FD46E6C4B45C55D4AC...`                                                                                                   |
| **DNSKEY**                   | DNSSEC public key.                                                                                                                                                                                                                                                                                                                         | When validating DNSSEC.                                                | **Command:** `dig DNSKEY example.com`**Output:**`<br>;; ANSWER SECTION:<br>example.com. 3600 IN DNSKEY 256 3 8 AwEAAc3...<br>`                                                  | `example.com. IN DNSKEY 256 3 8 AwEAAc3...`                                                                                                              |
| **RRSIG**                    | DNSSEC signature.                                                                                                                                                                                                                                                                                                                          | Seen in DNSSEC-enabled lookups.                                        | **Command:** `dig RRSIG example.com`**Output:**`<br>;; ANSWER SECTION:<br>example.com. 3600 IN RRSIG A 8 3 3600 20250901120000 20250811120000 ...<br>`                          | `example.com. IN RRSIG A 8 3 3600 20250901120000 20250811120000 ...`                                                                                     |
| **NSEC**                     | Proves nonexistence of a record.                                                                                                                                                                                                                                                                                                           | Seen with DNSSEC when no record exists.                                | **Command:** `dig NSEC example.com`**Output:**`<br>;; ANSWER SECTION:<br>example.com. 3600 IN NSEC mail.example.com. A MX RRSIG NSEC<br>`                                       | `example.com. IN NSEC mail.example.com. A MX RRSIG NSEC`                                                                                                 |

## DNS Flow
![[DNS-SERVER.webp]]

Flow
1. Client: example.com
	1. Check browser cache in case visited before. If not found, continue.
2. -> **DNS Resolver** (AKA **Recursive DNS Server**, usually provided by ISP and we can set our own one), it will check its own cache. If not found, continue. 
3. -> **Root Server** the backbone of the internet. Managed by 13 organizations including NASA University of Maryland, ICANN (the overseer), ... etc. It checks the TLD, and forward the query to the server that are responsible for them. 
4. -> **TLD server** (AKA nameserver) it has records to where to find the authoritative servers of the [[#Second Level Domain (SLD)]]. For example, the name server for [tryhackme.com](http://tryhackme.com/) is [kip.ns.cloudflare.com](http://kip.ns.cloudflare.com/) and [uma.ns.cloudflare.com](http://uma.ns.cloudflare.com/)
5. **Authoritative** **server** is responsible for storing the actual IP of the domain and where the changes of the domain name will actually happen. Therefore, it holds all the records for a domain.
Depending on the record type, the DNS record is then sent back to the Recursive DNS Server, where a local copy will be cached for future requests and then relayed back to the original client that made the request.
DNS records all come with a TTL (Time To Live) value. This value is a number represented in seconds that the response should be saved for locally until you have to look it up again. Caching saves on having to make a DNS request every time you communicate with a server.

## Example:
- A record
```powershell
C:\Users\Asus>nslookup -type=A google.com
Server:  UnKnown
Address:  fe80::980d:afff:fe35:7064

Non-authoritative answer:
Name:    google.com
Address:  142.250.202.46
```
- AAAA record
```powershell
C:\Users\Asus>nslookup -type=AAAA google.com
Server:  UnKnown
Address:  fe80::980d:afff:fe35:7064

Non-authoritative answer:
Name:    google.com
Address:  2a00:1450:4018:80b::200e
```
- CNAME Record 
```powershell
C:\Users\Asus>nslookup -type=CNAME google.com
Server:  UnKnown
Address:  fe80::980d:afff:fe35:7064

google.com
        primary name server = ns1.google.com
        responsible mail addr = dns-admin.google.com
        serial  = 792087638
        refresh = 900 (15 mins)
        retry   = 900 (15 mins)
        expire  = 1800 (30 mins)
        default TTL = 60 (1 min)
```
- TXT Record
```powershell
C:\Users\Asus>nslookup -type=TXT google.com
Server:  UnKnown
Address:  fe80::980d:afff:fe35:7064

Non-authoritative answer:
google.com      text =

        "docusign=05958488-4752-4ef2-95eb-aa7ba8a3bd0e"
google.com      text =

        "google-site-verification=wD8N7i1JTNTkezJ49swvWW48f8_9xveREV4oB-0Hf5o"
google.com      text =

        "facebook-domain-verification=22rm551cu4k0ab0bxsw536tlds4h95"
google.com      text =

        "globalsign-smime-dv=CDYX+XFHUw2wml6/Gb8+59BsH31KzUr6c1l2BPvqKX8="
google.com      text =

        "MS=E4A68B9AB2BB9670BCE15412F62916164C0B20BB"
google.com      text =

        "onetrust-domain-verification=de01ed21f2fa4d8781cbc3ffb89cf4ef"
google.com      text =

        "apple-domain-verification=30afIBcvSuDV2PLX"
google.com      text =

        "google-site-verification=TV9-DBe4R80X4v0M4U_bd_J9cpOJM0nikft0jAgjmsQ"
google.com      text =

        "docusign=1b0a6754-49b1-4db5-8540-d2c12664b289"
google.com      text =

        "google-site-verification=4ibFUgB-wXLQ_S7vsXVomSTVamuOXBiVAzpR5IZ87D0"
google.com      text =

        "v=spf1 include:_spf.google.com ~all"
google.com      text =

        "cisco-ci-domain-verification=47c38bc8c4b74b7233e9053220c1bbe76bcc1cd33c7acf7acd36cd6a5332004b"
```
- MX Record (mail priority is 10)
```powershell
C:\Users\Asus>nslookup -type=MX google.com
Server:  UnKnown
Address:  fe80::980d:afff:fe35:7064

Non-authoritative answer:
google.com      MX preference = 10, mail exchanger = smtp.google.com

C:\Users\Asus>
```