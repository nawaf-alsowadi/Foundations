Open Systems Interconnection Model is a framework that dictate how devices over the network sends, receive, and interpret data no matter what the device is. 
OSI Model has the following seven layers. At each layer (except for layer 1 which transform data to bits), a new data is added to the data we wish to send by a process called *encapsulation*.   ![[6d17472b87f8792dadde3bb06aa1fdaa.svg]]![[Pasted image 20250707182243.jpg]]
# Physical Layer
It is the lowest layer in the model. Devices use hardware, hardware use electronic signals to transfer data between devices in binary numbering system (1's and 0's).
For example, ethernet cables/ WiFi signals connect devices to each other.![[Pasted image 20250707182015.png]]
0's and 1's can be transferred by using digital signals (NRZ, RZ, Manchester encoding). Typically, up (high voltage) means 1, and down (low voltage) means 0.

![[Pasted image 20250707183215.jpg]]

---

## 🔌 Signal Encoding Methods (with “Up” and “Down”)

In signal waves, "up" = high voltage and "down" = low voltage. These are used to represent binary values depending on the **encoding method**.

---

### 🔷 1. NRZ-L (Non-Return-to-Zero Level)

**L = Level**  
Voltage **level** directly represents the bit.
It can have:

| Bit | Signal Level |
| --- | ------------ |
| 1   | High (⬆️)    |
| 0   | Low (⬇️)     |
or it can have:

| Bit | Signal Level |
| --- | ------------ |
| 0   | High (⬆️)    |
| 1   | Low (⬇️)     |
What matters is the consistency!
🧠 _Level itself carries information._

---

### 🔷 2. NRZ-I (Non-Return-to-Zero Inverted)

**I = Inversion**  
Bits are represented by **transitions** or **no transitions**.

|Bit|Signal Behavior|
|---|---|
|1|Invert (flip signal)|
|0|No change|
Basically, when we face 1, change the wave, otherwise, keep the same wave.
🧠 _Transition = 1, No transition = 0_

---

### 🔷 3. Manchester Encoding

Each bit is divided into **two halves**. The **transition in the middle** determines the bit.

| Bit | First Half → Second Half       |
| --- | ------------------------------ |
| 1   | Low ⬇️ → High ⬆️:              |
| 0   | High ⬆️ → Low ⬇️               |
0:
   __
    | __

1:
    __
   __ |

🧠 _Transition direction in the middle tells the value._

---

### 🔷 4. Differential Manchester Encoding

- Always a **transition in the middle**
- Start of the bit determines the value:

|Bit|Start of Bit|Middle of Bit|
|---|---|---|
|1|No transition|Always transition|
|0|Has transition|Always transition|
The start of the wave determine the bit. If it start the transition immediately => 0, if it start transition in the middle => 1
🧠 _Clock sync is easy. Info is in the presence/absence of transition at the start._

---

### ⚖️ Comparison Table

|Encoding Method|`1`|`0`|Notes|
|---|---|---|---|
|**NRZ-L**|High (⬆️)|Low (⬇️)|Level-based|
|**NRZ-I**|Transition (Flip)|No transition|Change means 1|
|**Manchester**|Low ⬇️ → High ⬆️|High ⬆️ → Low ⬇️|Mid-bit transition always|
|**Diff Manchester**|No transition at start|Transition at start|Mid-bit transition always|

---

## Data Link Layer
This layer contain the physical addressing including src & dst MAC (Media Access Control) addresses.
MAC addresses comes with all network-enabled devices.
It is specifically inside the Network Interface Card (NIC,  piece of hardware that all networked devices come with) and it is unique and the created by the manufacturer which burned it there and it cannot be changed.
![[ethernet-frame-structure.webp]]
- **Preamble:** Ethernet uses digital encoding where data is transmitted as ***voltage changes (up/down)***. Receiver needs to ***synchronize its clock*** with these changes to interpret bits correctly.
- **Start Frame Delimiter (SFD):** Indicate the actual start of the frame.
- **Destination MAC Address:** Physical address of the receiver.
- **Source MAC Address:** Physical address of the sender.
- **Length/Type:** Either the length of the data field or the protocol type (e.g., IPv4, ARP).
- **Frame Check Sequence (FCS):** Used to detect errors in the packet. If error is happened, frame will be discarded/ a retransmission request is sent to the sender.

## Network Layer
In this layer, the routing of the data we want to send takes place. Routing protocols such as *Open Shortest Path First (OSPF)* and *Routing Information Protocol (RIP)* determine the best optimal path for the data to transmitted.

To decide the best route, it depends on multiple factors:
- Is it the shortest?
- Is it reliable (have packets been lost on that path before)?
- Is it the fastest physical connection? i.e. one path can use fiber (fast) and some others use copper (slow)

In this layer we only use IP addresses, devices such as routers that are capable of delivering the packets are called Layer 3 devices.
![[893620a3545775f0fbbd75ea4bc4a946.svg]]
### IP/Network Header
![[Pasted image 20250708181718.png]]

| Field                                     | Size     | Description                                                                                                                                                                                                                                          | Purpose                     |
| ----------------------------------------- | -------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------- |
| **Version**                               | 4 bits   | IP version: always `4` for IPv4                                                                                                                                                                                                                      | IPv4 vs IPv6                |
| **IHL (Internet Header Length)**          | 4 bits   | Length of the IP header in 32-bit (min = 5, max = 20 bytes)                                                                                                                                                                                          | Header length               |
| **Type of Service (ToS)** or **DSCP/ECN** | 8 bits   | Defines priority and handling (QoS). See [[#QoS]] for more info.                                                                                                                                                                                     | QoS prioritization          |
| **Total Length**                          | 16 bits  | Entire size of the IP packet (header + data), in bytes (max is 655,535)                                                                                                                                                                              | Full packet size            |
| **Identification**                        | 16 bits  | Used to identify fragments of the *same IP packet*. All fragments share same ID.                                                                                                                                                                     | Fragment tracking           |
| **Flags**                                 | 3 bits   | Controls fragmentation: First Bit: Reserved (unused), Second Bit: DF (Don’t Fragment, if needed and MTU cannot handle its size, packet will be dropped and ICMP error will be returned), Third Bit: MF (1: More Fragments follow, 0: last fragment). | Fragment control            |
| **Fragment Offset**                       | 13 bits  | Position of this fragment in the original data                                                                                                                                                                                                       | Fragment placement          |
| **Time To Live (TTL)**                    | 8 bits   | Max number of hops (routers); prevents infinite loops                                                                                                                                                                                                | Prevents infinite looping   |
| **Protocol**                              | 8 bits   | Indicates the transport layer protocol (e.g., TCP = 6, UDP = 17, ICMP = 1)                                                                                                                                                                           | Next layer (e.g., TCP/UDP)  |
| **Header Checksum**                       | 16 bits  | Error-checking for the IP header                                                                                                                                                                                                                     | Detects header errors       |
| **Source IP Address**                     | 32 bits  | Sender’s IP address                                                                                                                                                                                                                                  | Sender's IP address         |
| **Destination IP Address**                | 32 bits  | Receiver’s IP address                                                                                                                                                                                                                                | Receiver's IP address       |
| **Options (optional)**                    | variable | Extra features like security, timestamping (used rarely)                                                                                                                                                                                             | Optional features           |
| **Padding**                               | variable | Zeros added to make header length a multiple of 32 bits                                                                                                                                                                                              | Alignment (32-bit boundary) |
#### Quality Oof Service (QoS)
8 bits:
- First 6 for Differentiated Services Code Point (DSCP) for prioritizing the traffic. For example:

| DSCP Value | Binary | Meaning                   | Use Case                        |
| ---------- | ------ | ------------------------- | ------------------------------- |
| `000000`   | 0      | Best Effort               | Normal traffic                  |
| `001010`   | 10     | AF11 (Assured Forwarding) | Low-priority business data      |
| `101110`   | 46     | Expedited Forwarding (EF) | Voice over IP (VoIP)            |
| `111000`   | 56     | CS7 (Class Selector 7)    | Network control (high priority) |

- Last 2 for Explicit Congestion Notification (ECN) used along with TCP to avoid dropping packets because of network congestion.
	- **Routers** that support ECN can **mark packets** instead of dropping them when the network is congested.
	- The **receiver sees the ECN mark** and tells the **sender (via TCP acknowledgment)**.
	- The **sender slows down** the sending rate to relieve congestion.

| ECN Bits | Meaning                               |
| -------- | ------------------------------------- |
| `00`     | Router in the path is not ECN-Capable |
| `01`     | ECN Capable Transport (ECT(1))        |
| `10`     | ECN Capable Transport (ECT(0))        |
| `11`     | Congestion Encountered (CE)           |
*Note: 01 & 10 are the same.*

## Transport Layer
Responsible for end-to-end communication. It segment the data properly for packet transmission and ensure it is delivered accurately and in the right order. It has the following functionality:

| Function                        | Description                                                                                                                                                                                                                                                                               |
| ------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Segmentation & Reassembly**   | Breaks large messages into smaller segments and reassembles them at the destination.                                                                                                                                                                                                      |
| **End-to-End Connection**       | Manages connections between source and destination applications.                                                                                                                                                                                                                          |
| **Reliable Data Transfer**      | Ensures data is delivered without errors or duplication (in protocols like TCP).                                                                                                                                                                                                          |
| **Flow Control**                | Prevents overwhelming the receiver with too much data.                                                                                                                                                                                                                                    |
| **Error Control**               | Detects and retransmits lost or corrupted segments.                                                                                                                                                                                                                                       |
| **Multiplexing/Demultiplexing** | Multiplexing combines multiple data streams into a single signal, while demultiplexing separates them at the receiving end. This allows multiple users or applications to share the same network resources, like a single cable or wireless channel, without interfering with each other. |
Transport Layer uses different protocols such as:
- **Transmission Control Protocol (TCP):** Reliable, provide  sync, reserve the connection for the whole session until data sent and received from the session layer is completed. Incorporate error checking which guarantee reliability. Used in emails, file sharing, web apps which require accuracy. 
![[d47215ad75f503af0b06dacca9ebace6.svg]]

- **User Datagram Protocol (UDP):** No sync, just send even if packets are lost, but very fast, and even app developers can customize the speed of sending the packets. Useful when in protocols that send small pieces of data such as device discovery protocols (e.g. [[ARP]] & [[DHCP]]) or in transmitting large files such as video streaming where losing some pixels is not an issue! 
![[3259184a7fd3dafed265974c31fc8c46.svg]]
 
 *Note: Other protocols such as Stream Control Transmission Protocol (SCTP) is worth exploring.*
### TCP Header


![[Pasted image 20250708201238.jpg]]

| Field                     | Size     | Description                                                                                                            |
| ------------------------- | -------- | ---------------------------------------------------------------------------------------------------------------------- |
| **Source Port**           | 16 bits  | Identifies sending application or process                                                                              |
| **Destination Port**      | 16 bits  | Identifies receiving application or process                                                                            |
| **Sequence Number**       | 32 bits  | Represents the **first byte** of data in this segment which is important for Reordering packets                        |
| **Acknowledgment Number** | 32 bits  | The next byte expected from the other side. Example: If `Ack = 1001`, that means byte `1000` was received successfully |
| **Data Offset**           | 4 bits   | TCP header length                                                                                                      |
| **Reserved**              | 3 bits   | Reserved for future use, must be 0                                                                                     |
| **Flags (Control Bits)**  | 9 bits   | Control bits (SYN, ACK, FIN, etc.) explained in [[#Flags]]                                                             |
| **Window Size**           | 16 bits  | Amount of data (in bytes) the receiver is willing to accept                                                            |
| **Checksum**              | 16 bits  | **Preserves the integrity of the packet** => Used for error-checking the header + data                                 |
| **Urgent Pointer**        | 16 bits  | Points to the last byte of **urgent data** in the segment (used only when URG flag is set)                             |
| **Options**               | Variable | Extended features like window scaling, timestamps                                                                      |
| **Padding**               | Variable | Extra 0s to align header to 32-bit boundary                                                                            |
#### Flags
*Note: Typical [[TCP IP Model & Protocols & Ports#3-Way Handshake]] uses **SYN → SYN/ACK → ACK***

| Flag           | Name        | Meaning                                                             |
| -------------- | ----------- | ------------------------------------------------------------------- |
| URG            | Urgent      | Urgent pointer is valid                                             |
| ACK            | Acknowledge | Acknowledgment number is valid                                      |
| PSH            | Push        | Ask receiver to pass data to the app immediately                    |
| RST            | Reset       | Reset the connection in case some failures happen in the connection |
| SYN            | Synchronize | Initiate a connection (first step in handshake)                     |
| FIN            | Finish      | Close the connection                                                |
| (NS, CWR, ECE) | ECN-related | Used for Explicit Congestion Notification                           |
### UDP Header
| Field                | Size    | Description                                                                                 |
| -------------------- | ------- | ------------------------------------------------------------------------------------------- |
| **Source Port**      | 16 bits | Port number of the sender (optional)                                                        |
| **Destination Port** | 16 bits | Port number of the receiver                                                                 |
| **Length**           | 16 bits | Length of the UDP header + data (in bytes)                                                  |
| **Checksum**         | 16 bits | Error detection for UDP header + data (optional in IPv4 but usually used, required in IPv6) |
## Session Layer

Responsible for everything related to the session between the end devices including:

| Function                     | Description                                                                                            |
| ---------------------------- | ------------------------------------------------------------------------------------------------------ |
| **Session Establishment**    | Sets up and tears down connections between applications.                                               |
| **Dialog Management**        | Controls who can send data and when (e.g., full-duplex or token-based half-duplex).                    |
| **Synchronization**          | Uses checkpoints in long data streams to recover from failures.![[functions_of_session_layer2 1.webp]] |
| **Activity Management**      | Divides message streams into independent units (aka activities) for processing.                        |
| **Resynchronization**        | Restores communication to a known state after disruptions.                                             |
| **Data Transfer Management** | Oversees the exchange of data between systems.![[functions_of_session_layer5.webp]]                    |
How It Works:
- Negotiates session parameters (e.g., direction of communication, authentication).
- Inserts **checkpoints** to allow resuming data transfer after a failure.
- Uses **tokens** to manage who can transmit data.
- Gracefully closes sessions, ensuring no data is lost.

Session layer protocols examples:

| Protocol                                        | Purpose                                                                                                                                                        |
| ----------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **RTCP (Real-time Transport Control Protocol)** | Provides feedback on media stream quality in real-time sessions.                                                                                               |
| **PPTP (Point-to-Point Tunneling Protocol)**    | Establishes VPN sessions over IP networks.                                                                                                                     |
| **PAP (Password Authentication Protocol)**      | Authenticates users over PPP connections.                                                                                                                      |
| **RPCP (Remote Procedure Call Protocol)**       | Allows code on one machine to call functions on another.                                                                                                       |
| **SDP (Sockets Direct Protocol)**               | Enhances socket communication using Remote Direct Memory Access (RDMA, allow both ends to access memory of each other directly!) for high-speed data transfer. |
Devices That Operate at the Session Layer
- **Firewalls** – Filter and manage session states.
- **Proxy Servers** – Create/manage client-server sessions.
- **Session Border Controllers (SBCs)** – Manage VoIP sessions.
- **Application Servers** – Handle web or app-based session creation.

|Layer|Focus|
|---|---|
|**Session Layer**|Manages sessions, dialogues, checkpoints, and synchronization.|
|**Transport Layer (e.g., TCP)**|Provides reliable data delivery, sequencing, and error control.|
_Note_: The session layer's functions are typically **merged into** the **application layer** or partially handled by the **transport layer (TCP)**. Only if it is required by the applications, it is gonna use one of the session layer protocols which add the necessary data **inside the data payload** (tokens, markers, state info, ... etc.)

## Presentation Layer
AKA the translator or syntax layer, it is responsible to transform the data into readable and secure formats for both ends. Its main role is to format, encrypt/decrypt, encode/decode. compress/uncompress the data by agreeing on how the data will be represented on both ends.

| Protocol                                    | Purpose                                                          |
| ------------------------------------------- | ---------------------------------------------------------------- |
| **LPP (Lightweight Presentation Protocol)** | ISO presentation services over TCP/IP.                           |
| **NCP (NetWare Core Protocol)**             | File and print services in Novell NetWare.                       |
| **NDR (Network Data Representation)**       | Defines data types and their encoding.                           |
| **XDR (External Data Representation)**      | Standard data format for cross-platform data exchange.           |
| **SSL/TLS**                                 | Secure encryption of data for web and application communication. |
Some attacks in this layer:

| Attack Type                  | Description                                               |
| ---------------------------- | --------------------------------------------------------- |
| **Man-in-the-Middle (MITM)** | Intercepts and reads or alters transmitted data.          |
| **SSL/TLS Downgrade**        | Forces weaker encryption protocols for easier decryption. |
| **Certificate Spoofing**     | Fakes a certificate to impersonate a trusted source.      |
| **Code Injection**           | Exploits poor parsing or formatting of input data.        |

## Application Layer
It's not the user interface of the application itself, but rather **the layer that provides the functionalities and services** **that** user-facing **applications use to interact with the network.**

It utilizes various protocols to facilitate **different types of communication**, including HTTP, FTP, SMTP, DNS, and more.

One interesting app protocol is called **Network File System (NFS)** with port number 2049. It allow mounting files from remote hosts and interact with them as they are locally available by using RPC calls.
![[nfs.gif]]

## Packets vs Frames vs Segment
![[Pasted image 20250712142857.jpg]]
**Segment**: Data + TCP/UDP header
**Packets:** Segment + IP address, can have different structure based on the type of the packet
**Frames:** MAC address + Packet
