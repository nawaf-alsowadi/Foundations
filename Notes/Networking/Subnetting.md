While subnetting is **not required**, it is helpful for the network administrator to know which data is sent to which category of devices. It basically divide the network into smaller segments.
Achieved by splitting up the number of devices (100 => 50 + 50) that can fit in the network by representing it with a subnet mask.![[Pasted image 20250701005632.png]]
![[Pasted image 20250630223032.png]]
Each octet is 4 bytes (32 bits) ranging from 0 - 255 possibilities for each.
1 byte = 8 bits
=> 2^8 = 256
=> [0, 255]  = 256

## Classless Inter-Domain Routing (CIDR)
Represent IP addresses and their associated network masks, using the format `[IP address]/[prefix length]`:
**192.168.1.0/24**
## Subnet Mask & Prefix Length (/#)
![[Pasted image 20250701003645.png]]
Assuming as per our design the first two octets is reserved for the network (wont have hosts), and rest will be used for hosts, if we use /17, we will have only two subnets since we borrowed 1 bit: 2^1 = 2 subnets
Note: subnets can be subnetted further from larger to smaller ONLY: /24 => /26.
- `192.168.1.0/26` → Usable: `192.168.1.1` – `192.168.1.62`
- `192.168.1.64/26` → Usable: `192.168.1.65` – `192.168.1.126`
- `192.168.1.128/26` → Usable: `192.168.1.129` – `192.168.1.190`
- `192.168.1.192/26` → Usable: `192.168.1.193` – `192.168.1.254`

**255.255.255.0 (/24):** means first 24 bits (first 3 octets/bytes) of the total 32 bits (eventually the 4 octets/bytes) is reserved for the network and the subnet is allowed to use only the final octet for its hosts: 32-24 = 8 => 2^8 = 256-2=254.
**255.255.255.248 (/30):** means the first 30 bits are reserved and **only two devices** are allowed to have IP addresses: 32-30 = 2 bits => 2^2 = 4 - 2 (reserved IPs) = 2 IPs
**255.255.255.255 (/32):** means the first 32 bits are reserved and **only one device** is allowed in this subnet.
Can be used in scenario where we want the firewall to allow one specific IP to SSH:
```java
Allow SSH from 203.0.113.45/32
```
In the subnet, we have four types of IP addresses.

| Type              | Purpose                                                                                           | Explination                                                                                                | Example                           |
| ----------------- | ------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------- | --------------------------------- |
| Network Address   | Used to **identify network existance**                                                            | Device 192.168.1.1 will exist on network 192.168.1.0 which is **reserved** IP address                      | 192.168.1**.0**                   |
| Broadcast Address | Used to **send broadcast message** to the whole subnet devices                                    | Not assigned to any device, used in broadcasting only and it is **reserved** IP address                    | 192.168.1**.255**                 |
| Default Gateway   | Special address assigned to the device responsible for **sending information to another network** | Gateways (usually routers) has the IP address of the **first or last usable IP address but not necessary** | 192.168.1**.1** 192.168.1**.254** |
| Host address      | Used to identify the device                                                                       | Any device can have any usable IP address with one condition: **Not reserved IP**                          | 192.168.1 - 192.168.1.254         |
Subnetting provide efficiency, security (separate the cafe devices IPs from the public internet IPs provided by the cafe), and full control. 

#subnet #mask #IP