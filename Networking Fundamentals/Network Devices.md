
## Host

Any computational device which sends or receive traffic

![[Pasted image 20251224203905.png]]

- Client -- Server
- Client -> Initiate Request
- Server -> Responds to the request
- The above are terms and anything can be a client or a server

## IP address

Identity of each host.

Every host needs a IP address to communicate over the internet.

SRC -> Source IP address
DST -> Destination IP address

![[Pasted image 20251225144744.png]]

IP address are 32 bits.

![[Pasted image 20251225144851.png]]

- Hierarchically Assigned

![[Pasted image 20251225145017.png]]

The process of breaking down IP address to their hierarchy is called Subnetting.

## Network

- Connection between n hosts.
- A network is what transports traffic between Hosts.
- Local Grouping of hosts require similar connectivity.
- Network can contain other networks
- These other networks are called Sub-nets
![[Pasted image 20251225145514.png]]



## Repeater

- Repeaters Regenerate signals
-  Like a Signal extender

## Hub

- Multi port repeater.


![[Pasted image 20251225145804.png]]

## Bridges

- Bridges sit between Hub - connected hots
- Bridges only have 2 ports
-  Bridges can learn which hosts are on each sides.

![[Pasted image 20251225145904.png]]

## Switches

- Switches are a combination of Hubs and Bridges 
	- Multiple Ports
	- Learns which hosts are on each port
- Switches facilitate communication within a network
![[Pasted image 20251225150308.png]]
- All the devices above belong to the same network

## Router 

- A device which facilitate communication BETWEEN networks
![[Pasted image 20251225150459.png]]

- Middle blue one is a router
-  Knowledge of each of the networks is called a route
- Routing Table is the table that stores all the routes.

A Router has different IP for each network
![[Pasted image 20251225150830.png]]
IP for network 1 = 172.16.20.1
IP doe network 2 = 172.16.30.254

 - Gateway:
	 - A gateway is the hosts way out of the Network
![[Pasted image 20251225151124.png]]

For IP Address: 172.16.30.44 -> Default Gateway: 172.16.30.254
![[Pasted image 20251225151301.png]]

![[Pasted image 20251225151409.png]]



