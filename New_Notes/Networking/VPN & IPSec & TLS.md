## VPN
Used to create virtual network between two devices/networks that is secure (uses encryption, even ISP cannot see traffic) and private (anonymus) an untracebale (if vpn server doesnot use logging, if so it is like we are not using vpn).
VPN uses different tecknologies to for security and privacy:

| Technology                               | Description                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| ---------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Point-to-Point (PPP)                     | Layer 2 protocol used to establish a direct connection between two network nodes. In VPNs like PPTP, PPP **handles authentication** (e.g., via MS-CHAPv2) and can encapsulate network layer protocols for tunneling. It does not use certificates or public/private keys. PPP is non-routable and does not inherently support encryption; encryption is added by the tunneling protocol (e.g., MPPE in PPTP).                                   |
| Point-to-Point Tunneling Protocol (PPTP) | VPN protocol that **encapsulates PPP frames using tunnels** them over IP networks, **making PPP-based traffic routable across public networks** like the internet. While PPTP enables remote access, it is considered insecure due to weak encryption and broken authentication (MS-CHAPv2). It uses PPP for authentication and to support multiple network layer protocols, not as a remedy for its insecurity.![[What-is-PPTP-Nedir-EN.webp]] |
| Internet Protocol Security (IPSec)       | [[#IPSec]]![[Pasted image 20250721170841.png]]                                                                                                                                                                                                                                                                                                                                                                                                  |
|                                          |                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
Types of VPN:

| Topology Type  | Description                        | Best For                          |
| -------------- | ---------------------------------- | --------------------------------- |
| Point-to-Point | One-to-one connection              | Single user to network            |
| Site-to-Site   | Connects networks!                 | Office-to-office                  |
| Hub-and-Spoke  | Central hub with remote sites      | Centralized enterprise            |
| Full Mesh      | Every site connects to every other | High availability, real-time apps |
| Partial Mesh   | Some direct, some via hub          | Balanced cost/performance         |
| Client-to-Site | Remote users to company network    | Telecommuting, mobile users       |
## IPSec
Set of protocols that secures IP traffic by providing encryption, authentication, and integrity *at the network layer*. It operates within the IP protocol using components like [[#Authentication Header (AH)]] and [[#Encapsulating Security Payload (ESP)]] , and can be used in both [[#Transport Mode]] and [[#Tunnel Mode]] to protect data in transit across IP networks.

### Authentication Header (AH)
Extension header to provide authentication, integrity, and anti-replay (not widely used since [[#Encapsulating Security Payload (ESP)]] provides the same + encryption). **Used in scenarios where encryption is not needed / [[#Encapsulating Security Payload (ESP)]] is not supported.**
### Encapsulating Security Payload (ESP)
Encapsulate the IP packet with header and trailer for encryption or both encryption and authentication. 
### Transport Mode
Provides protection for [[OSI Model Layers#Transport Layer]] protocols.
In this mode, [[#Encapsulating Security Payload (ESP)]] will encrypt and optionally authenticate **IP payload** not the IP header.
![[Pasted image 20250722221237.png]]
![[Pasted image 20250722221049.png]]
### Tunnel Mode
Provides protection to the **entire IP packet**.  
Only used when one or both security association  are security gateway => hosts doesn't need to implement IPSec.
In this mode, [[#Encapsulating Security Payload (ESP)]] encrypts and optionally authenticate the entire IP packet.
*Note*: *AH header authenticate inner IP packet and selected portion of outer IP header.*
![[Pasted image 20250722221321.png]]
![[Pasted image 20250722221109.png]]

Encryption Then Authentication (EtA) to cover more fields for authentication:
- Use ESP to protect data then use ESP authentication option or AH **on the cipher text**.
- Use two bundled SAs: Inner ESP -> Outer AH. IP packet goes to transport mode. (ESP-SA)AH-SA

Authentication Then Encryption (AtE) to prevent manipulating the original data without detection: Inner AH -> Outer ESP. IP packet goes to tunnel mode. (ESP-AH)ESP-SA.
![[Pasted image 20250730204025.png]]
### [[#Transport Mode]] VS. [[#Tunnel Mode]]

![[Pasted image 20250722220840.png]]

### Security Association (SA) & Security Association Database (SAD) & Security Policy Database (SPD)
![[Pasted image 20250730200615.png]]

![[Pasted image 20250730201639.png]]

- SA:
	Logical unidirectional connection used to set the security parameters (crypto algorithms, keys, ... etc.) between the both ends to establish the secure connection in IPSec. Each SA connection identified by SPI (determine if association is AH or ESP), and dest IP. 
- SAD:
	The database of the security parameters:
	- Security parameter index (SPI)
	- Sequence number counter
	- Sequence counter overflow
	- Anti-replay window
	- AH information
	- ESP information
	- Lifetime of this security association
	- IPsec protocol mode (i.e. Tunnel, transport, or wildcard).
	- Path MTU
- SPD:
	Policies to determine whether the IP packet should be protected by IPSec or how it should be processed for security.![[Pasted image 20250722201513.png]]

Flow chart for **outbound connection using IPSec:** 
![[Pasted image 20250722201626.png]]

Flow chart for **inbound connection using IPSec:** 
![[Pasted image 20250722201748.png]]

## Transport Layer Security (TLS)
Responsible for securing [[#Transport Mode]] by adding additional layer on top of it called [[#TLS Record Protocol]] to provide confidentiality and message integrity.  It uses two concepts:

| Feature                     | **TLS Session**                                                                                                                                                                                                                                                                                           | **TLS Connection**                                                                                                                                                                                                                                                                                         |
| --------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Purpose**                 | Stores negotiated [[#TLS Security Parameters]]                                                                                                                                                                                                                                                            | Transports actual encrypted **application data**                                                                                                                                                                                                                                                           |
| **Established by**          | [[#TLS Handshake]]                                                                                                                                                                                                                                                                                        | TLS Record Protocol using session parameters                                                                                                                                                                                                                                                               |
| **Lifetime**                | Can be **long-lived** (persisted across multiple connections)                                                                                                                                                                                                                                             | **Short-lived** (ends when communication finishes)                                                                                                                                                                                                                                                         |
| **Reusability**             | Yes — can be reused in **session resumption**                                                                                                                                                                                                                                                             | No — **each connection is unique and temporary**                                                                                                                                                                                                                                                           |
| **Connection Relationship** | One session can support **multiple connections**                                                                                                                                                                                                                                                          | One connection is tied to **exactly one session**                                                                                                                                                                                                                                                          |
| **Peer-to-peer?**           | Not directly — it’s a security context                                                                                                                                                                                                                                                                    | Yes — connections are **peer-to-peer** (both sides encrypt/decrypt)                                                                                                                                                                                                                                        |
| **Data Transmission**       | Does **not** directly transmit data                                                                                                                                                                                                                                                                       | **Does** transmit encrypted data                                                                                                                                                                                                                                                                           |
| **Examples**                | You log into your **bank’s website** (e.g., `bank.com`) and perform a TLS handshake. The server and browser agree on encryption settings and generate a session. Later, if you open a **new tab** and go to `bank.com` again, your browser can **reuse the session** to skip another expensive handshake. | When you first visit `bank.com`, a **connection is established** using the session’s encryption keys to **send your login request**. Each tab or action (like checking transactions) could create a new connection, **but under the same session**. Once the data is exchanged, the connection **closes**. |
#### Other examples:
| Scenario                          | TLS Session                                                         | TLS Connection                                                             |
| --------------------------------- | ------------------------------------------------------------------- | -------------------------------------------------------------------------- |
| **HTTPS** (e.g., Gmail, Amazon)   | A session is created when you first connect to the site             | Each browser tab or resource (images, JS, etc.) uses separate connections  |
| **Email (SMTP with STARTTLS)**    | Your email client establishes a session with the SMTP server        | Each email sent might use a new connection under the same session          |
| **WebSocket over TLS (wss://)**   | The initial session secures the handshake                           | The WebSocket TLS connection stays open while messages are exchanged       |
| **VPN (e.g., TLS-based OpenVPN)** | The session defines encryption keys for the secure VPN tunnel       | The actual VPN data stream uses one or more connections inside the session |
| **Load-balanced HTTPS**           | Sessions can be resumed using session tickets shared across servers | Each load-balanced request forms a short-lived connection                  |
### TLS Security Parameters 

Parameters for **Session:**
![[Pasted image 20250729180253.png]]

Parameters for **connection**:
![[Pasted image 20250729180428.png]]

### TLS Record Protocol
![[Pasted image 20250729180732.png]]
![[Pasted image 20250729180837.png]]

![[4603275cd98c93aeb8c46b1b1afa0ba6.svg]]

Under this protocol, we send **4** type of **independent messages**:
![[Pasted image 20250729181359.png]]
Where:
- Cipher Spec: inform other end to start encryption based on security params we agreed on.
- Alert Protocol:
	- Byte 1: tell other end about fatal error (terminate connection, other connection within same session may continue) or value of the severity of the warning.
	- Byte 2: The specific alert.
- Handshake protocol: Allows the server and client to authenticate each other and to negotiate [[#TLS Security Parameters]].

### TLS Handshake
![[Pasted image 20250729182554.png]]
![[b83b75dbbf5b7e4be31c8000f91fc1a8.svg]]

![[Pasted image 20250729182745.png]]

*Note: In phase 3, the **client** generates the **pre-master secret**, Encrypted with the **server's public key** (from the certificate), Sent in `client_key_exchange`. Now both share the same pre-master secret which they derive from it a master key (pre-master secret + client & server random numbers) to generate [[#TLS Security Parameters]].*

#### HTTPS
It is just the normal HTTP but over SSL/TLS. When used, we start first with [[#TLS Handshake]] which must include certs exchange then we start sending first HTTP message. 
*Note: when closing HTTPS session => closing TLS => closing TCP connection, we must exchange closure alerts, not just send then close without waiting the peer to send the closure alert in the standard way of closing TCP connection as explained in [[TCP IP Model & Protocols & Ports#3-Way Handshake]]! because it can cause "incomplete close" which could be a sign of DoS attack*.

#### SSH
When comparing SSH and [[#HTTPS]], securing the communication is not similar. **SSH does not use TLS/SSL** to secure the communication. **It uses its own protocol!** due to the fact that the authentication can be based on username/passwords or public and private keys (and of course because SSH (1995) was invented before TLS (1999)).
![[Pasted image 20250730194239.png]]


| Feature              | **SSH**                                      | **TLS/SSL**                                 |
| -------------------- | -------------------------------------------- | ------------------------------------------- |
| **Protocol Purpose** | Secure remote shell access & file transfer   | Secure web, email, VPN, etc. communications |
| **Used by**          | `ssh`, `scp`, `sftp`                         | `https`, `ftps`, `imaps`, `vpn`, etc.       |
| **Port**             | Default: **22**                              | Default (HTTPS): **443**                    |
| **Key Exchange**     | Own methods: Diffie-Hellman, ECDH, etc.      | TLS key exchange (RSA, DHE, ECDHE, etc.)    |
| **Authentication**   | Username/password or **public/private keys** | Usually certificate-based (X.509)           |
| **Encryption**       | Uses its own cipher negotiation              | Uses cipher suites defined in TLS spec      |
| **Handshake**        | SSH has its **own handshake protocol**       | TLS has its **standard handshake**          |
![[Pasted image 20250729192146.png]]
The key exchange method allows to establish a shared secret K and the hash value h which are used to derived the SSH session keys:
- The hash h of the initial key exchange is also taken as the session_id
- IVClient2Server = Hash(K, h, “A”, session_id) // initialization vector
- IVServer2Client = Hash(K, h, “B”, session_id) // initialization vector
- EKClient2Server = Hash(K, h, “C”, session_id) // encryption key
- EKServer2Client = Hash(K, h, “D”, session_id) // encryption key
- IKClient2Server = Hash(K, h, “E”, session_id) // integrity key
- IKServer2Client = Hash(K, h, “F”, session_id) // integrity key


##### Channels in SSH
Instead of opening a separate SSH connection to the same device for each operation, we open a channel (or multiple channels within same connection. e.g, open multiple terminals) with the same SSH connection. Life of channels:
- opening a channel
- data transfer
- closing a channel
Types of channels:

| Channel Type                             | Description                                                                                                                                                                                                                                          |
| ---------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Session Channel**                      | For interactive shell, running remote commands, or executing scripts                                                                                                                                                                                 |
| **X11 Channel**                          | For forwarding GUI (X11) applications over SSH                                                                                                                                                                                                       |
| **Port Forwarding** (SSH Tunneling)      | For tunneling TCP connections (used in local/remote/SSH forwarding) to provide the ability to **convert any insecure TCP connection into a secure SSH connection**. ![[Pasted image 20250730194845.png]]                                             |
| **SFTP Channel**                         | For secure file transfers (uses its own subsystem within SSH)                                                                                                                                                                                        |
| **Direct TCP/IP** (Local forwarding)     | For proxying TCP connections through SSH. **Client -> Server -> Remote**. Used when I want to access a remote device that is accessible **only** from specific server. Redirect traffic from an **unsecured TCP connection to a secure SSH tunnel.** |
| **Forwarded TCP/IP** (Remote forwarding) | For receiving forwarded TCP connections (from remote to local, etc.). Server -> Client -> Remote. When I want remote machine to access a service on my local machine.                                                                                |
## TLS Attack (Padding Oracle Attack)
Basics:
- DES enc/dec message size of 8 bytes![The DES encryption algorithm (in Technology > Encryption @ iusmentis.com)](https://www.iusmentis.com/technology/encryption/des/des.gif)
- AES enc/dec message size of 16 bytes ![[Pasted image 20250730212345.png]]
If the message is longer than that, we use Modes of Operations.
![[Pasted image 20250730213921.png]]
The **vulnerability arises because of the use of CBC mode** in the following SSL/TLS vulnerable versions:

| **TLS Version** | **Vulnerability to Padding Oracle?** | **Why?**                                                                              |
| --------------- | ------------------------------------ | ------------------------------------------------------------------------------------- |
| **SSL 3.0**     | ✅ **Vulnerable**                     | Poor MAC-then-encrypt design and predictable padding errors.                          |
| **TLS 1.0**     | ✅ **Vulnerable**                     | Uses **CBC mode**; padding errors can be observed.                                    |
| **TLS 1.1**     | ✅ **Vulnerable**                     | Improved IV handling, but still uses CBC mode and does not fix padding oracle issues. |
| **TLS 1.2**     | ✅ **Vulnerable if CBC is used**      | CBC mode is optional — **GCM or ChaCha20-Poly1305** is safe.                          |
| **TLS 1.3**     | ❌ **Not Vulnerable**                 | **CBC mode removed entirely**; only AEAD ciphers allowed (GCM, CHaCha20).             |
Assuming we use the following message with AES-CBC in the above TLS/SSL vulnerable versions
`Hello, this is a 45-byte message for TLS test!`
This is 46 characters => 46 Bytes according to ASCII:
Block 1: `48 65 6C 6C 6F 2C 20 74 68 69 73 20 69 73 20 61` => 16 bytes
Block 2: `20 34 35 2D 62 79 74 65 20 6D 65 73 73 61 67 65` => 16 bytes
Block 3: `20 66 6F 72 20 54 4C 53 20 74 65 73 74 21` => 14 bytes

*Note: maximum size for each data transmission [[OSI Model Layers#Data Link Layer]] is ~1500 bytes (for Ethernet MTU) and ~9000 for Jumbo frames, [[OSI Model Layers#Network Layer]] & [[OSI Model Layers#Transport Layer]] theoretically is 65,535 including headers. During [[TCP IP Model & Protocols & Ports#3-Way Handshake]], Max Segment Size (MSS) is set depending on the available resources and **the smallest will be chosen** to avoid network fragmentation which can lead to packet being dropped.* 

According to AES-CBC, we need to use 16 bytes for each block. So, last block will be padded with two bytes as in [[#TLS Record Protocol]] when block ciphers are used. 
![[Pasted image 20250730214056.png]]

