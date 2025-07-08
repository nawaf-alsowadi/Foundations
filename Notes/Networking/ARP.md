Address Resolution Protocol (ARP) used to find the MAC address (Physical Identifier of devices) of another device and associate it with the IP address by sending a broadcast message to find the device we wish to communicate with.
Each device store the mapping of IP -> MAC addresses of other devices by using its cache. ARP uses two types of messages:
- **ARP Packet Request:** Broadcast message with specific IP we wish to communicate with.
- **ARP Packet Respond:** Reply from the device who own the IP with its MAC address.
*Note:* IP address is considered as logical address since it can change whereas MAC address is the physical address and it is never changed.
![[Pasted image 20250706182312.png]]
