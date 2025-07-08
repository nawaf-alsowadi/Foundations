# Network Topology
In short, it is the design of the network.
# Star Topology
Most commonly used today because of its reliability and scalability.
In this topology (design), multiple devices are connected to one CENTRAL switch/hub.
![[Pasted image 20250630182826.png]]

| Advantages                                                                              | Disadvantages                                                                                                  |
| --------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------- |
| **Scalability:** very easy to add more devices as the demand for the network increases. | **Expensive:** more cabling and the purchase of dedicated networking equipment                                 |
|                                                                                         | **Prone to Failure:** due to the centralization nature                                                         |
|                                                                                         | The more the network scales, the more dependance on maintenance will be which result in harder troubleshooting |

# Bus Topology
Relies on a single connection (backbone cable) to deliver. Packets are sent to the left and right until it reaches its destination. It cannot handle large amount of data simultaneously. 
![[Pasted image 20250630183734.png]]

| Advantages                                                                                                                            | Disadvantages                                                                                                  |
| ------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------- |
| **Easier and more cost-efficient topologies to set up:** because of their expenses, such as cabling or dedicated networking equipment | **Prone to becoming slow and bottlenecked:** if devices within the topology are simultaneously requesting data |
|                                                                                                                                       | **Difficult to identify** which device is experiencing issues with data all travelling along the same route.   |
|                                                                                                                                       | **Single point of failure** along the backbone cable                                                           |
# Ring (aka Token) Topology
Devices are connected directly to each other to form a loop => less cabling and no need for central device.
It works by sending the data in the loop until it reach its destination. The device will send its own data before forwarding other data received from neighbor device.

![[Pasted image 20250630215627.png]]

| Advantages                                                                  | Disadvantages                                                              |
| --------------------------------------------------------------------------- | -------------------------------------------------------------------------- |
| **Cheaper:** due to less cabling and no centralizing requirments            |                                                                            |
| **Easy to Troubleshoot:** because only on direction of the transmitted data | If many devices exists, we may have to visit many nodes to reach the issue |
| **Less prone to bottleneck:** Traffic does not travel simultaneously        | If one device is broken or one cable is cut => whole network will break    |
# Switch
Dedicated devices that aggregate multiple other devices in the network using ethernet cable. Can connect devices by having ports of: 4, 8, 16, 24, 32, and 64 to be plugged into (since multiplication of 4 is easier to manage in the network).

More efficient than counterparts (hubs/repeaters) since they keep the track of which device connected to which port instead of sending the packets to every port to find the destination like the hubs would do.
![[Pasted image 20250630221043.png]]

# Router
Connect networks and pass the data between them by using routing. Routing will create & determine the path of the data to be delivered such as the following.
![[Pasted image 20250630221832.png]]

#LAN #router #switch