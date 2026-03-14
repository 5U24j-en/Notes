
Host must follow a set of of rules to send Data/Packets over a network.

OSI Model are the set of rules and instructions for devices to communicate over the Internet.

- OSI Model has 7 Layers
	- Physical
	- Data Link
	-  Network
	-  Transport
	-  Session
	- Presentation
	- Application

## Layer 1 - Physical Layer 

- Transporting Bits between hosts
- Example :
	- Cables
	- WiFi
	- Optical Fiber cable
	- Repeaters
	- Hub

## Layer 2 - Data Link

- Data Link -> Interacts with the wire
- Example -
	- Network Card
	- WiFi Card

- Layer 2 uses Addressing Scheme - MAC addresses
	-  48 bits, represented as 12 hex digits
	- Every Network Interface Card (NIC) has a unique MAC address
	![[Pasted image 20251225153455.png]]
- Switches also is a Layer 2 technology
- Routers are also a Layer 2 technology
- Hence even routers have MAC addresses
![[Pasted image 20251225153629.png]]
MAC address used to transport Data. Hop-to-Hop

## Layer 3 - Network

- Anything with an IP address are in Layer 3.
- IP Address = End to End Delivery
- MAC Address = Hop to Hop Delivery
![[Pasted image 20251225153901.png]]

- IP address is used to transport from One host to Another Host. First and Last destination
- MAC address is used to transport data through all the Layer 2 devices in between. Hop o Hop

![[Pasted image 20251225154119.png]]
![[Pasted image 20251225154209.png]]
Data has Layer 3 information and Layer 2 Information which changes at each Hop.


ARP ( Address Resolution Protocol ):
- it will Link L3 address to a L2 Address

## Layer 4 - Transport (service - service)

- If there are multiple applications sending multiple data.
![[Pasted image 20251225154902.png]]

- Distinguish data streams
- Layer 4 will take all the incoming data and provide the right data to the right application
- Uses **Addressing Scheme - Ports** to distinguish data.
- ![[Pasted image 20251225155204.png]]
- TCP and UDP are 2 different strategies to distinguish data streams
- TCP - favors reliability
- UDP - favors efficiency
![[Pasted image 20251225155357.png]]
Data packet structure

- For all connections there is a source port and destination port
- The destination PORT is governed by the application in use and the source port is randomly selected by the client
![[Pasted image 20251225155832.png]]
![[Pasted image 20251225155848.png]]

- When we open multiple tab of the same website, the client ports change
![[Pasted image 20251225160000.png]]

## Layer 5,6,7 - Session, Presentation, Application

- Every application can implement their configurations for Layer 5,6,7
- That's why some consider Layer 5,6,7 as a single Layer- Application Layer

- Example TCP/IP Model
- ![[Pasted image 20251225160338.png]]

Encapsulation
- Sending Data over network is Encapsulation
- ![[Pasted image 20251225160553.png]]
- Data send to Layer 4
- Layer 4 adds the **header** Port and this part of data is called Segment
- ![[Pasted image 20251225160630.png]]
- Now this segment is then sent to Layer 3 , Which adds the IP as header to the segment . 
- NOTE: Layer 3 only sees 1,0 in the segment. It cannot differentiate. Similarly other Layers follow this
- This Data is called a Packet
- ![[Pasted image 20251225160850.png]]
- Now the Packet is sent to Layer 2 , which add MAC address (SRC-DST)as the header.
- This Data is now called a Frame
- ![[Pasted image 20251225161024.png]]
- Finally this data is converted into 1 , 0 and transported over the wire/WiFi known as the Layer 1

De-Encapsulation 
- Receiving Data over the network Is Encapsulation
- Each Frame,Packet,Segment is verified and then sends the Data to the destination
![[Pasted image 20251225161412.png]]

