Dynamic Host Configuration Protocol (DHCP) is a protocol used to assign IP address to the devices automatically (devices can be assigned IP addresses manually also).
Communication between the devices and the DHCP server as follows:
- **DHCP Discover Packet:** If the device is not assigned IP address manually, it sends this message to look for the DHCP server and to retrieve IP address.
- **DHCP Offer Packet:** The server respond with this message, offering IP address that can be assigned to the device.
- **DHCP Request Packet:** Device confirm it want the IP address.
- **DHCP Ack Packet:** Server reply acknowledging this has been completed, and the device can start using the IP Address.

![[Pasted image 20250707175250.png]]