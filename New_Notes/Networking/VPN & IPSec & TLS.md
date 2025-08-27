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
## TLS Attack (Padding Oracle Attack: POODLE & Lucky13 Attacks)
Basics:
- DES enc/dec message size of 8 bytes![The DES encryption algorithm (in Technology > Encryption @ iusmentis.com)](https://www.iusmentis.com/technology/encryption/des/des.gif)
- AES enc/dec message size of 16 bytes ![[Pasted image 20250730212345.png]]
If the message is longer than that, we use Modes of Operations.
![[Pasted image 20250730213921.png]]


![[Pasted image 20250802152930.png]]

According to RFC 2246 & 4346 & 7366, AES-CBC is used along with MeE. In AES-CBC, we need to use 16 bytes for each block. So, last block will be padded with bytes as in [[#TLS Record Protocol]] when block ciphers are used. The issue was because of two things:
- SSL 3.0/TLS 1.0-1.2: 
	- **MACing -> Padding -> Encrypting data.**
	- **In reverse: Decrypt -> Unpadding -> UnMACing**
- Servers misconfigurations: 
	- Some servers were returning the issue **when it happen for faster response.**
	- Decrypt failed => return decryption error. Padding error => return padding error. MAC failed => MAC error.
	- Because of this flow, it **enabled attackers to do Side-Channel Attacks** (leak information from the server, not attacking encoding algorithms itself)

| **TLS Version** | **Vulnerability to Padding Oracle?** | **Why?**                                                                              |
| --------------- | ------------------------------------ | ------------------------------------------------------------------------------------- |
| **SSL 3.0**     | ✅ **Vulnerable**                     | Poor MAC-then-encrypt design and predictable padding errors.                          |
| **TLS 1.0**     | ✅ **Vulnerable**                     | Uses **CBC mode**; padding errors can be observed.                                    |
| **TLS 1.1**     | ✅ **Vulnerable**                     | Improved IV handling, but still uses CBC mode and does not fix padding oracle issues. |
| **TLS 1.2**     | ✅ **Vulnerable if CBC is used**      | CBC mode is optional — **GCM or ChaCha20-Poly1305** is safe.                          |
| **TLS 1.3**     | ❌ **Not Vulnerable**                 | **CBC mode removed entirely**; only AEAD ciphers allowed (GCM, CHaCha20).             |
What was important for the attacker to leak from the servers was **if the error is Padding error**. If so, they can intercept messages sent to servers => manipulate the ciphertext => send to server again => get padding error message => try again **until MAC error is received** (POODLE attack).  In (Lucky13 attack), they used the time difference in the server response to know if the error is MAC or Padding error.
When attackers know if the error is padding, it enabled them to exploit AES-CBC to brute force every byte of the ciphertext message.

*Note:*
- *SSL 3.0: Only MAC based on SHA1 & MD5 (no use of keys for message authentication as in HMAC)*
- *TLS 1.0 & 1.1: HMAC-SHA1*
- *TLS 1.2: HMAC-SHA256 & HMAC-SHA384*

*Note: maximum size for each data transmission [[OSI Model Layers#Data Link Layer]] is ~1500 bytes (for Ethernet MTU) and ~9000 for Jumbo frames, [[OSI Model Layers#Network Layer]] & [[OSI Model Layers#Transport Layer]] theoretically is 65,535 including headers. During [[TCP IP Model & Protocols & Ports#3-Way Handshake]], Max Segment Size (MSS) is set depending on the available resources and **the smallest will be chosen** to avoid network fragmentation which can lead to packet being dropped.* 

Assuming we use the following message encrypted with AES-CBC and MACed using HMAC-SHA1 (see above in notes why). Assuming we want to exploit the above TLS/SSL vulnerable versions:
- **Message** we send: `Hello, this is a 45-byte message for TLS test!` => 46 characters => 46 Bytes according to ASCII: `48 65 6C 6C 6F 2C 20 74 68 69 73 20 69 73 20 61 20 34 35 2D 62 79 74 65 20 6D 65 73 73 61 67 65 20 66 6F 72 20 54 4C 53 20 74 65 73 74 21`
- **HMAC** can take any key size. Assuming key is zeros in a size of 20 characters (20 bytes) as recommended by [RFC 2104](https://datatracker.ietf.org/doc/html/rfc2104) (since output is 20 bytes/chars): `E1 58 B2 B5 2D 39 29 98 8E 24 89 EB AA 03 61 FB 36 6F E2 EB`
- Since the vulnerable versions uses MAC then Encrypt (MtE), we will have to append the MAC with the message along with padding then encrypt them using AES-CBC:
	- **Block 1:** `48 65 6C 6C 6F 2C 20 74 68 69 73 20 69 73 20 61` => 16 bytes (message)
	- **Block 2:** `20 34 35 2D 62 79 74 65 20 6D 65 73 73 61 67 65` => 16 bytes (message)
	- **Block 3:** `20 66 6F 72 20 54 4C 53 20 74 65 73 74 21 E1 58` => 16 bytes ((14 bytes message + 2 bytes MAC))
	- **Block 4:** `B2 B5 2D 39 29 98 8E 24 89 EB AA 03 61 FB 36 6F` => 16 bytes (MAC)
	- **Block 5:** `E2 EB 0D 0D  0D 0D 0D 0D 0D 0D 0D 0D 0D 0D 0D 0D` => (2 bytes MAC + 14 padding (0D = 13, last byte tells padding length and not included in calculation for padding (meaning padding bytes are length -1). Note: this padding is specific for chain blocks, others use standardized padding like PKCS#7 where we pad without caring too much (not length -1, in our case it would be 0Es))
	- assume we use PKCS#7, **last block (5)** is: `E2 EB 0E 0E  0E 0E 0E 0E 0E 0E 0E 0E 0E 0E 0E 0E`
	- **AES Key** & **IV**: `AES-CBC key test`: `41 45 53 2d 43 42 43 20 6b 65 79 20 74 65 73 74`
- Encrypted message: [Encryption & HMAC Tool](https://gchq.github.io/CyberChef/#recipe=AES_Encrypt(%7B'option':'Hex','string':'4145532d434243206b65792074657374'%7D,%7B'option':'Hex','string':'4145532d434243206b65792074657374'%7D,'CBC','Hex','Hex',%7B'option':'Hex','string':''%7D)AES_Decrypt(%7B'option':'Hex','string':'4145532d434243206b65792074657374'%7D,%7B'option':'Hex','string':'4145532d434243206b65792074657374'%7D,'CBC','Hex','Hex',%7B'option':'Hex','string':''%7D,%7B'option':'Hex','string':''%7D/disabled)&input=NDg2NTZDNkM2RjJDMjA3NDY4Njk3MzIwNjk3MzIwNjEyMDM0MzUyRDYyNzk3NDY1MjA2RDY1NzM3MzYxNjc2NTIwNjY2RjcyMjA1NDRDNTMyMDc0NjU3Mzc0MjFFMTU4QjJCNTJEMzkyOTk4OEUyNDg5RUJBQTAzNjFGQjM2NkZFMkVC)
	- **Block 1:** `1A 01 2E 96 8B 0D D5 1B DB E9 DE EB 98 57 36 DD`
	- **Block 2:** `F9 63 A6 E9 AF 06 35 6F 86 7D 57 10 B4 3E F3 0F`
	- **Block 3:** `A6 2B 9B 29 9B 68 25 65 63 66 43 DC C3 DF D6 D7`
	- **Block 4:** `D4 7B A9 CD 10 02 F4 87 7E 9F F4 BC C5 50 9B 9C`
	- **Block 5:** `9F 66 23 E6 32 35 BC E0 0F B6 A8 EE 5D EC 6F 76`
- Decrypted blocks **before EXORing:**
	- **Block 1:** `92 03 F4 12 C6 E6 35 40 30 C0 A0 01 D1 65 31 5`
	- **Block 2:** `34 35 1B BB E9 74 A1 7E FB 84 BB 98 EB 36 51 B8`
	- **Block 3:** `D9 05 C9 9B 8F 52 79 3C A6 09 32 63 C0 1F 12 57`
	- **Block 4:** `14 9E B6 10 B2 F0 AB 41 EA 8D E9 DF A2 24 E0 B8`
	- **Block 5:** `36 90 A7 C3 1E 0C FA 89 70 91 FA B2 CB 5E 95 92`

Each row is 16 bytes that will be processed by AES-CBC.
### AES-CBC Encryption
Let's denote: 
- `Pi` as the i-th plaintext block.
- `Ci` as the i-th ciphertext block.
- `AES(K, Pi)` = AES **encryption** of `Pi` using key `K`.
- `IV` as the Initialization Vector.
The CBC encryption process can be represented as: 
1. **C0 = AES(K, IV)**
2. **C1 = AES(K, P1 ⊕ C0)**
3. **C2 = AES(K, P2 ⊕ C1)**
4. ...
5. **Ci = AES(K, Pi ⊕ Ci-1)**

![[Pasted image 20250730214056.png]]
### AES-CBC Decryption
Let's denote: 
- `Ci` as the i-th ciphertext block.
- `Di` as the i-th decrypted plaintext before XORing.
- `Pi` as the i-th plaintext block.
- `AES⁻¹(K, Ci)` = AES **decryption** of `Ci` using key `K`
- `IV` as the same Initialization Vector used in encryption.

The CBC decryption process can be represented as: 
1. **P0 = AES⁻¹(K, C0) = D0 ⊕ IV**
2. **P1 = AES⁻¹(K, C1) = D1 ⊕ C0**
3. **P2 = AES⁻¹(K, C2) = D2 ⊕ C1**
4. ...
5. **Pi = AES⁻¹(K, Ci) = Di ⊕ Ci-1**
![[Pasted image 20250803214150.png]]

In order to get the intermediate values before XORing, use [this tool](https://the-x.cn/en-us/cryptography/Aes.aspx) with ECB mode and make sure to XOR with [this tool](https://xor.pw/#) to display plaintext bytes for double checking.

Since: **Pi = Di ⊕ Ci-1**
It is true that: **Di = Pi ⊕ Ci-1**
Let: **P'i = Di ⊕ C'-1**, Assuming **C'i = case when we receive mac error** after trying 0-255. The resulted byte is **P'i = 0x01** (assuming it is the last byte in the block)
It is true that: **P'i = Pi ⊕ Ci-1 ⊕ C'-1**
And therefore: **Pi = P'i ⊕ Ci-1 ⊕ C'-1**
Since we already have P'i, Ci-1, C'-1, **DECRYPTION IS POSSIBLE!!**
![[Pasted image 20250806211126.png]]

*Note: [PadBuster](https://github.com/AonCyberLabs/PadBuster) is a tool to automate padding oracle attacks against encrypted data (e.g., CBC mode in AES).
*Example usage:*
*`perl padBuster.pl http://target.com/vuln.php data-to-decrypt 16`*

### Real example
Defining our target:
- We need to find `C'4[16]` that gives MAC error for the last byte of the last block (since it will be XORed wit `P5[16]`).
- Meaning when we XOR `C'4[16]` with`P5[16]`, it gives `P'5[16] = 0x01)` (our goal is not to find the plain text for now).
- ``Python code
```
target = 0x01
fixed_value = 0x92

for i in range(256):
    result = fixed_value ^ i
    if result == target:
        print(f"Found: 0x{fixed_value:02X} ^ 0x{i:02X} = 0x{result:02X}")
        break
```
- Output: `Found: 0x92 ^ 0x93 = 0x01`
- We already know `0x92` is the decrypted output of AES (in practical it will not be shown, server will return MAC error since it assumed 0x01 is the padding byte and remove the next 20 MAC bytes and hashed the plain text bytes and showed MAC error for unmatching the plaintext MAC and the provided MAC)
- So, so far we have
	- `C4[16] = 0x9C` (sent ciphertext)
	- `C'4[16] = 0x93` (output manipulated cipher text to get MAC error)
	- `P'5[16] = 0x01` (confirmed by receiving MAC error)
	- `Pi = P'i ⊕ Ci-1 ⊕ C'-1` => `P5[16] = 0x01 ⊕ 0x9C ⊕ 0x93` => `P5[16] = 0E` (which is the original padding byte of the plain text). **CRACKED!!**
- Not yet...
- In order to do the same for the rest of the bytes, assuming we will try to do the same with second last byte, we need to find 0x02 for the last two bytes. That's *256 * 256 = 65,536* possible values. And for the last byte there are *256^16  = 3.4 * 10^38* which is infeasible (check [[#Next Step]]).
#### Next Step
Define new value C''4[16] that force the server to put 0x02 in the last byte by sending it again with the resulted value in:
- **C''4[16]= C4[16] ⊕ P5[16] ⊕ 0x02**
- This is true because:
- **P''5[16] = D5[16] ⊕ C''4[16]** (exactly like the equation before)
	- **P''5[16] = P5[16] ⊕ C4[16] ⊕ C4[16] ⊕ P5[16] ⊕ 0x02** (just substituting from above equations)
	- **P''5[16] = 0x02** (equation above canceled rest of the elements)

![[Pasted image 20250806211252.png]]

To do it in practical, we **extend our target from 0x01 to 0x02**, then do the XORing:
1. C''4[16] = C4[16] ⊕ P5[16] ⊕ 0x02
2. C''4[16] = 0x9C ⊕ 0E ⊕ 0x02 = 0x90
3. P5[16] = C''4[16] ⊕ D5[16]
4. P5[16] = 0x90 ⊕ 0x92 = 0x02 **(unbelievable!)**

Server will assume two bytes of padding, and will check for the next one P5[15] which will give padding error and we will try 0-255 in C4[15] until we get MAC error:
1. ``Python code
```
target = 0x02
fixed_value = 0x95

for i in range(256):
    result = fixed_value ^ i
    if result == target:
        print(f"Found: 0x{fixed_value:02X} ^ 0x{i:02X} = 0x{result:02X}")
        break
```
Output: `Found: 0x95 ^ 0x97 = 0x02`
- Where `0x95` is the decrypted output (we receive MAC error that confirm the result is 0x02 instead of knowing the value 0x95)
- So, so far we have
	- `C4[15] = 0x9B` (sent ciphertext)
	- `C'4[15] = 0x97` (output manipulated cipher text to get MAC error)
	- `P'5[16] = 0x02` (confirmed by receiving MAC error)
2. `Pi = P'i ⊕ Ci-1 ⊕ C'-1` => `P5[15] = 0x02 ⊕ 0x9B ⊕ 0x97` => `P5[15] = 0E` (which is the original padding byte of the plain text). **CRACKED AGAIN!!**
The process will be repeated until all cipher text decrypted. Note that we cannot extend our target beyond `0x0F` (which is the case when we have 15 padding bytes and one byte of MAC). As a result, we can just exclude the last block in the next sending and assume we only have `number of blocks - 1` and repeat the process to decrypt the next block in the same exact way we did.
![[Pasted image 20250806210936.png]]
