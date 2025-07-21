It control what traffic is allowed from outside to inside and vice versa based on the configuration of the admin. Firewall (FW) check from where, and where the traffic going to and what ports and protocols it uses. It find the answers by using **Packet Inspection**. 
(2-5 categories of FWs exits) Example of two of the types of firewalls:
- **Stateful:** Depends on the **behavior** of the connection. Consume resources. decision is dynamic (e.g., allow only part of [[TCP IP Model & Protocols & Ports#3-Way Handshake]]). If connection is bad, it **block the entire host**. 
- **Stateless:** It depends on static **set of rules/polices** to **block/allow each packet**.
![[Pasted image 20250720223812.png]]
![[stateless-vs-stateful-firewall-table.webp]]