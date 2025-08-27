![[Pasted image 20250811170727.png]]

In a scenario where:
- **Application/Intent**: An application on Computer1 requests a TCP connection to Computer3 (e.g., HTTP GET).

- **(N/A) DNS (if hostname used)**: If a hostname is used it may show name resolution (DNS query → reply).

- **ARP (Layer 2 name-to-MAC)**: Computer1 checks its ARP table for Computer3’s MAC. If not present it broadcasts an ARP request; gateway, broadcast with ARP requests to other LANs to find MAC. Router replies with ARP reply containing MAC.
    
- **Frame encapsulation**: The IP packet is encapsulated into an Ethernet frame with source/dest MAC addresses.
    
- **Routing / gateway hops**: If Computer3 is on a different subnet, the packet goes to the local gateway (router).
    
- **IP layer**: IP packet is forwarded to the next hops until it reaches Computer3’s interface.
    
- **TCP handshake**:
    - Computer1 sends **SYN** packet to Computer3 (shows seq number).
        
    - Computer3 replies **SYN-ACK**.
        
    - Computer1 sends **ACK** — connection established.
- **Application data transfer**:
    
    - Computer1 sends **HTTP GET** (or other data) inside TCP.
        
    - Computer3 responds **HTTP 200** with the page payload.
- **Tear down**: FIN/ACK teardown sequence — not usually needed to reveal flag.


**HANDSHAKE:** Starting TCP/IP Handshake between computer1 and computer3

**HANDSHAKE:** Sending SYN Packet from computer1 to computer3

**ROUTING:** computer1 says computer3 is not on my local network sending to gateway: router

**ARP REQUEST:** Who has router tell computer1

**ARP RESPONSE:** Hey computer1, I am router

**ARP REQUEST:** Who has computer3 tell router

**ARP RESPONSE:** Hey router, I am computer3

**HANDSHAKE:** computer3 received SYN Packet from computer1 (router forwarding), sending SYN/ACK Packet to computer1

**HANDSHAKE:** computer1 received SYN/ACK Packet from computer3, sending ACK packet to computer3

**HANDSHAKE:** computer3 received ACK packet from computer1, Handshake Complete

**TCP:** Sending TCP packet from computer1 to computer3

**TCP:** computer3 received TCP Packet from computer1, sending ACK Packet to computer1
