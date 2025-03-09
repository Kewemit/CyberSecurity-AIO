# Terms
| **Characteristic**         | **TCP**                                                                    | **UDP**                                                                 |
| -------------------------- | -------------------------------------------------------------------------- | ----------------------------------------------------------------------- |
| `Transmission`             | Connection-oriented                                                        | Connectionless. Fire and forget.                                        |
| `Connection Establishment` | TCP uses a three-way handshake to ensure that a connection is established. | UDP does not ensure the destination is listening.                       |
| `Data Delivery`            | Stream-based conversations                                                 | packet by packet, the source does not care if the destination is active |
| `Receipt of data`          | Sequence and Acknowledgement numbers are utilized to account for data.     | UDP does not care.                                                      |
| `Speed`                    | TCP has more overhead and is slower because of its built-in functions.     | UDP is fast but unreliable.                                             |

* ``PDU`` = **Protocol Data Units**
	* data packet made up of control information and data encapsulated from each layer of the **OSI** model.
	* Here is a breakdown:![[Pasted image 20250228092040.png]]
* ``OSI`` = **Open Systems Interconnect**
	* A representation of how communication is handled between networked computers.![[Pasted image 20250228085059.png]]
* ``TCP-IP`` = **Transmission Control Protocol - Internet Protocol** 
	* A representation of how communication is handled between networked computers.![[tcp-ip-model-layers-and-their-functions.png]]
* ``NTA`` = **Network Traffic Analysis**
	* A method of monitoring network availability and activity to identify anomalies, including security and operational issues.
* ``TCP`` = **Transmission Control Protocol**
	* Connection-based, transfers data **slower** than ``UDP`` but is **more reliable**.
* ``UDP`` = **User Datagram Protocol**
	* Connectionless, transfers data **faster** than ``TCP`` but is **less reliable**.
* ``IDS`` = Intrusion Detection System
	* Monitors network activities, flagging irregularities for review. Doesn't affect data flow.
* ``IPS`` = Intrusion Prevention System
	* Prevents identified threats (typically by dropping packets or blocking IP).
* `Packet`
	* A small segment of a larger message. Data sent over the networks are divided into packets. Packets are recombined by the receiving device.
* ``Encapsulation``
	* Adds information to a ``packet`` as it travels to its destination. 
* ``Decapsulation`` 
	* Reverses the ``encapsulation`` process by removing the info, so a destination device can read the original data.
* ``BPF`` = **Berkeley Packet Filter**
	* Technology that enables a raw interface to read and write from the **Data-Link** layer. Syntax is shared by many tools.
* ``NTAP`` = **Network Tap**
	* Devices capable of taking copies of network traffic and sending them to another place for analysis. 
	* These can be in-line or out-of-band. 
	* They can actively capture and analyze the traffic directly or passively by putting the original packet back on the wire as if nothing had changed.
	* ![[Pasted image 20250228075444.png]]
* ``MAC`` = **Media Access Control**
	* Network protocol that identifies devices in networks
* ``MAC Address`` = **Media Access Control Address**
	* Unique address given to each network connected hardware. Works on the Data-Link layer.
* `Broadcast Domain` 
	* When a computer network is separated logically then it is a ``Broadcast Domain``
	* Devices in the same broadcast domain can communicate **directly** using ``Layer 2`` addressing.
	* A **router** is required only when communicating **outside** the broadcast domain (``Layer 3`` communication).
	* **MAC addresses** are used for communication within the same broadcast domain.
	* A **broadcast domain** is a logical division of a network where all devices can reach each other using Layer 2 broadcasts.
	- Typically, all devices connected to the same switch or VLAN belong to the same broadcast domain.
- ``LAN`` = **Local Area Network**
	- A collection of devices connected together in one physical location (like an office or home).
* `VLAN` = **Virtual Local Area Network**
	* A logical ``overlay network`` that groups together a **subset of devices** that share a physical ``LAN``, **isolating the traffic** for each group
	* Here, a set of multiple computers form a ``broadcast domain`` using a virtual connection as they are physically separated
	* ![[VLAN_Concept.svg.png]]
* ``Overlay Network``
	* 
* ``IP`` = Internet Protocol
	* 
* `IP Address` = Internet Protocol Address
	* IP Addressing is the logical means of assigning addresses to devices on a network.
	* Each device connected to the internet requires a unique IP Address.
	* An IP Address has two parts.
		* 1. One part identifies the host, such as a computer.
		* 2. The other part identifies the network it belongs to.
		* TCP/IP uses a subnet mask to seperate them.
* ``DNS`` = Domain Name System
	* The "phone book of the internet".
	* Translates domain names that we easily remember.
		* Like ``google.com`` into an IP address like ``105.249.102.100``. 
	* Allows computers, servers, and other networked devices, each with their unique IP addresses, to talk to each other. 
	* Gets users to the website they’re looking for.
* `IPv4`
	* Made up of 32-bit four octet numbers represented in decimal format. 
		* For example: `192.168.86.243`. 
	* Format known as dotted quad which is x.x.x.x, where:
		* each x/octet can be a value between ``0``-``255``.
	* Used in these layers:
		* **Layer 3** (`Network`) of the OSI model, or 
		* **Layer 2** (`internet`) of the TCP-IP model. 
	* IPv4 routes most of today's internet traffic.
		* Makes it possible for us to connect our devices to the web
* `IPv6`
	* 
* `VLSM` = Variable-length subnet masks
	* 
* `CIDR` = Classless Inter-Domain Routing
	* 
* `TLS` = Transport Layer Security
	* 
* `SSL` = Secure Sockets Layer
	* 
* a
# What is it?
## Network Traffic Analysis
**Network Traffic Analysis (NTA)**; the act of examining network traffic to characterize common ports and protocols utilized, establish a baseline for our environment, monitor and respond to threats.

This process helps security specialists determine anomalies, including security threats in the network, early and effectively pinpoint threats. 

Everyday use cases include:
* `Collecting` traffic to analyze upcoming threats.
* `Setting` a baseline for network communications.
* `Identifying` and ``analyzing`` traffic from 
	* non-standard ports, 
	* suspicious hosts, 
	* and issues such as 
		* **HTTP errors, 
		* problems with TCP, 
		* or misconfigurations.
* `Detecting` malware on the wire, such as 
	* ransomware, 
	* exploits, 
	* non-standard interactions.

For example, if we detect many `SYN` packets on ports that we rarely utilize in our network, we can conclude that it's someone trying to determine what ports are open on our hosts. Actions like this are typical markers of a **port scan**. 
## Required Skills and Knowledge
We don't have to know everything by heart, but we should know what to look for when certain aspects of the content seem unfamiliar. 
### TCP/IP Stack & OSI Model
This understanding will ensure we know how networking traffic and the host applications interact.
### Basic Network Concepts
Knowing what types of traffic we will see at each level includes an understanding of the individual layers that make up the TCP/IP and OSI model and the concepts of switching and routing. 

If we tap a network on a backbone link, we will see much more traffic than usual, and it will be vastly different from what we find compared to tapping an office switch.
### Common Ports and Protocols
Identifying common ports and protocols quickly and having a functional understanding of how they communicate will ensure we can identify potentially malicious or malformed network traffic.
### Concepts of IP Packets and the Sublayers
Foundational knowledge of how TCP and UDP communicate will, at a minimum, ensure we understand what we see or are searching for.
* ``TCP`` is stream-oriented and allows us to follow a conversation between hosts easily. 
* ``UDP`` is quick but not concerned with completeness (doesn't care if some packets are dropped), so it would be harder to recreate something from this packet type.
### Protocol Transport Encapsulation
Each layer will encapsulate the previous. Being able to read or dissect when this encapsulation changes will help us move through data quicker. It is easy to see hints based on encapsulation headers.
## Environment and Equipment
The list below contains many different tools and equipment types that can be utilized to perform network traffic analysis.

Each will provide a different way to capture or dissect the traffic. Some offer ways to copy and capture, while others read and ingest. 

These notes will explore just a few of these ([Wireshark](https://www.wireshark.org/) and [tcpdump](https://www.tcpdump.org/)). 
### Common Traffic Analysis Tools
| Tool                  | Description                                                                                                                                                                                                                                                                                                                                                                   |
| --------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| tcpdump               | A CLI utility that, with the aid of LibPcap, captures and interprets network traffic from a network interface or capture file.                                                                                                                                                                                                                                                |
| Tshark                | A network packet analyzer much like TCPDump. It will capture packets from a live network or read and decode from a file. It is the command-line variant of Wireshark.                                                                                                                                                                                                         |
| Wireshark             | Wireshark is a graphical network traffic analyzer. It captures and decodes frames off the wire and allows for an in-depth look into the environment. It can run many different dissectors against the traffic to characterize the protocols and applications and provide insight into what is happening.                                                                      |
| NGrep                 | NGrep is a pattern-matching tool built to serve a similar function as grep for Linux distributions. The big difference is that it works with network traffic packets. NGrep understands how to read live traffic or traffic from a PCAP file and utilize regex expressions and BPF syntax. This tool shines best when used to debug traffic from protocols like HTTP and FTP. |
| tcpick                | tcpick is a command-line packet sniffer that specializes in tracking and reassembling TCP streams. The functionality to read a stream and reassemble it back to a file with tcpick is excellent.                                                                                                                                                                              |
| Network Taps          | Taps (Gigamon, Niagra-taps) are devices capable of taking copies of network traffic and sending them to another place for analysis. These can be in-line or out of band. They can actively capture and analyze the traffic directly or passively by putting the original packet back on the wire as if nothing had changed.                                                   |
| Networking Span Ports | Span Ports are a way to copy frames from layer two or three networking devices during egress or ingress processing and send them to a collection point. Often a port is mirrored to send those copies to a log server.                                                                                                                                                        |
| Elastic Stack         | The Elastic Stack is a culmination of tools that can take data from many sources, ingest the data, and visualize it, to enable searching and analysis of it.                                                                                                                                                                                                                  |
| SIEMS                 | SIEMS (such as Splunk) are a central point in which data is analyzed and visualized. Alerting, forensic analysis, and day-to-day checks against the traffic are all use cases for a SIEM.                                                                                                                                                                                     |
## BPF Syntax
Many of the tools above have their own syntax to utilize, but one that is shared among them is [Berkeley Packet Filter (BPF)](https://en.wikipedia.org/wiki/Berkeley_Packet_Filter)syntax. 
* BPF is a technology that enables a raw interface to read and write from the Data-Link layer. 
* We care for BPF because of the filtering and decoding abilities it provides us. 
* For more information on BPF syntax, check out this [reference](https://www.ibm.com/docs/en/qsip/7.4?topic=queries-berkeley-packet-filters).

## Performing Network Traffic Analysis
* Can be as simple as watching live traffic roll by in our console 
* **Or** 
* Can be as complex as capturing data with a tap, sending it back to a SIEM for ingestion, and analyzing the pcap data for signatures and alerts related to common tactics and techniques.

At a minimum, to listen passively, we need to be connected to the network segment we wish to listen on. This is especially true in a **switched environment** where **VLANS** and **switch ports** will not forward traffic outside their broadcast domain.

If we wish to capture traffic from a specific VLAN, our capture device should be connected to that same network. 

Devices like network taps, switch or router configurations like span ports, and port mirroring can allow us to get a copy of all traffic traversing a specific link, regardless of what network segment or destination it belongs to.
![[Pasted image 20250228075444.png]]
#### NTA Workflow
* NTA can be a very dynamic process and is not a direct loop. 
* It is greatly influenced by what we are looking for (network errors vs. malicious actions) and where we have visibility into our network. 

Performing traffic analysis can distill down to a few basic tenants.
![[Pasted image 20250228075644.png]]
#### 1. Ingest Traffic
Once we have decided on our placement: 
* Begin capturing traffic. 
* Utilize capture filters if we know what we're looking for.
#### 2. Reduce Noise by Filtering
Capturing traffic of a link, especially one in a production environment, can be extremely noisy. So once we complete the initial capture, 
* an attempt to filter out unnecessary traffic from our view can make analysis easier. (i.e. Broadcast and Multicast traffic)
#### 3. Analyze and Explore
Now is the time to start carving out data pertinent to the issue we are chasing down. Look at 
* specific hosts, 
* protocols, 
* even things as specific as flags set in the TCP header. 

The following questions will help us:
1. Is the traffic encrypted or plain text? Should it be?
2. Can we see users attempting to access resources to which they should not have access?
3. Are different hosts talking to each other that typically don't?
#### 4. Detect and Alert
1.  Are we seeing any errors? Is a device not responding that should be?
2.  Use our analysis to decide if what we see is harmless or potentially malicious.
3.  Other tools like **IDS** and **IPS** can come in handy at this point. They can run heuristics and signatures against the traffic to determine if anything within is potentially malicious.
#### 5. Fix and Monitor
Fix and monitor is not a part of the loop but should be included in any workflow we perform. 
* If we make a change or fix an issue, we should continue to monitor the source for a time to determine if the issue has been resolved.
# Networking Primer - Layers 1-4
This section is a quick refresher on networking and how some standard protocols we can see while performing traffic captures work. 
* These concepts are at the core of capturing and dissecting traffic. 
* Without a fundamental understanding of typical network flow and what ports and protocols are used, we cannot accurately analyze any traffic we capture. 

If this is the first time you encounter some of these terms or concepts, we suggest completing the [Introduction to Networking](https://academy.hackthebox.com/course/preview/introduction-to-networking) Module from HackTheBox.

## OSI / TCP-IP Models
### Networking Models
![[Pasted image 20250228081907.png]]
The image above gives a great view of the **Open Systems Interconnect** (`OSI`) model and the **Transmission Control Protocol - Internet Protocol** (`TCP-IP`) model side by side. 

The models are a representation of how communication is handled between networked computers. Let's take a second to compare the two.
#### Model Traits Comparison.

| Trait           | OSI                            | TCP-IP                                  |
| --------------- | ------------------------------ | --------------------------------------- |
| ``Layers``      | Seven                          | Four                                    |
| ``Flexibility`` | Strict                         | Loose                                   |
| ``Dependency``  | Protocol independent & generic | Based on common communication protocols |

When examining these two models, we can notice that the OSI model is segmented more than the TCP-IP model. This is because it is broken down into small functional chunks. 
* ``Layers 1.-4.`` of the OSI model are **focused on controlling the transportation of data between hosts**. 
	* This control includes everything from the physical medium used for transmission to the protocol utilized to manage the conversation or lack thereof when transporting data. 
* ``Layers 5.-7.`` handle the interpretation, management, and presentation of the encapsulated data presented to the end-user. 
![[OSI-7-layers.jpg.webp]]
>[!Important]
>* Think of the **OSI** model as the **theory behind how everything works**,
>* Whereas the **TCP-IP** model is **more closely aligned with the actual functionality of networking.** 

The TCP-IP model is a bit more blended, and the rules are flexible. 
* The TCP-**IP** model comprises four layers where 
	* ``Layers 5.-7.`` of the OSI model align with ``layer 4.`` of the TCP-IP model,
	* ``Layer 3.`` deals with transportation, 
	* ``Layer 2.`` is the internet layer which aligns with the network layer (``3.``) in OSI,
	* ``Layer 1.`` is the link-layer which covers layers ``1.-2.`` of the OSI model.
![[tcp-ip-model-layers-and-their-functions.png]]
![[c173bb_0b8d26799e6f4bdfbb4ad0aeb5160908~mv2.avif]]

Later on we will examine many different **Protocol Data Units** (`PDU`), so a functional understanding of how it appears in theory and on the wire is required. 

>[!Important]
>* A **PDU** is a **data packet made up of control information and data encapsulated from each layer of the OSI model**. 
### PDU Example
The breakout below will show how the layers in the two models match up to a **PDU**:
![image](https://academy.hackthebox.com/storage/modules/81/net_models_pdu2.png)

When inspecting a **PDU**, we need to keep the idea of encapsulation in mind. 
As our data moves down the protocol stack, each layer will wrap the previous layers' data in a new bubble we call **encapsulation**. 

This bubble adds the necessary information of that layer into the header of the **PDU**. This information can vary by level, but it includes:
* **what is held by the previous layer**, 
* **operational flags**, 
* any **options required to negotiate communications**, 
* the **source** and **destination IP addresses**, 
* **ports**, 
* **transport**, 
* **application layer protocols**.
#### PDU Packet Breakdown
![[Pasted image 20250228092027.png]]

The image above shows us the makeup of a **PDU** side by side with a packet breakout from Wireshark's ``Packet Details`` pane. 

>[!Important]
When we see the breakout in Wireshark, it is in **reverse order**. Wireshark shows us the **PDU** in reverse because it is in the order that it was ``unencapsulated``.
## Addressing Mechanisms
Now that we have gone over the basic concepts driving networking behavior let us take some time to discuss the addressing mechanisms that enable the delivery of our packets to the correct hosts. 

We will begin with **Media Access Control** (**MAC**) addresses first.
#### MAC-Addressing
Each **logical** or **physical interface** attached to a host has a **Media Access Control** (`MAC`) **address**. 

This address is a 48-bit `six octet` address represented in **hexadecimal** format. If we look at the image below, we can see an example of one by the `red` arrow:
![image](https://academy.hackthebox.com/storage/modules/81/Addressing.png)

**MAC-addressing** is utilized in ``Layer 2`` (``data-link`` or ``link-layer`` depending on the model) communications between hosts. This works through **host-to-host communication** within a broadcast domain. 
* If ``layer 2`` traffic needs to cross a ``layer 3`` interface, that **PDU** is sent to the ``layer 3`` egress/exit interface, and it´s routed to the correct network. 
* At ``layer 2``, this looks as though the **PDU** is addressed to the router interface, and the router will take the ``layer 3`` address into account when determining where to send it next. 
* Once it makes a choice, it strips the ``encapsulation`` at ``layer 2`` and replaces it with new information that indicates the next physical address in the route.
## IP Addressing
The Internet Protocol (`IP`) was developed to deliver data from one host to another across network boundaries. 
* IP is responsible for 
	* Routing packets, 
	* The encapsulation of data, 
	* Fragmentation and reassembly of datagrams when they reach the destination host. 
* By nature, IP is a connectionless protocol that provides no assurances that data will reach its intended recipient. 
	* For the reliability and validation of data delivery, IP relies on upper-layer protocols such as **TCP**. 
* Currently, there exist two main versions of IP:
	* ``IPv4``, which is the current dominant standard, 
	* ``IPv6``, which is intended to be the successor of IPv4.
#### IPv4
The most common addressing mechanism is the **Internet Protocol address version 4 (`IPv4`)**. 

IPv4 addressing is the core method of routing packets across networks to hosts located outside our immediate vicinity. 

The image below shows us an example of an **IPv4** address by the `green` arrow:
![[Addressing.png]]

* An IPv4 address is made up of a 32-bit `four octet` number represented in decimal format. 
	* In our example, we can see the address `192.168.86.243`. 
* Each **octet** of an IP address can be represented by a number ranging from `0` to `255`. 
* When examining a **PDU**, we will find IP addresses in:
	* **Layer 3** (`Network`) of the OSI model 
	* **Layer 2** (`internet`) of the TCP-IP model. 
* You need to understand what these addresses are, what they do for us, and at which layer they are used.
#### IPv6
After a little over a decade of utilizing IPv4, it was determined that we had quickly exhausted the pool of usable IP addresses. 

To help solve this issue, two things were done: 
* 1. Implementing variable-length subnet masks (`VLSM`) and Classless Inter-Domain Routing (`CIDR`). 
	* This allowed us to redefine the usable IP addresses in the **v4** format changing how addresses were assigned to users. 
* 2. The creation and continued development of `IPv6` as a successor to IPv4.

* IPv6 provides us a much larger address space that can be utilized for any networked purpose. 
* IPv6 is a 128-bit address `16 octets` represented in Hexadecimal format. 

We can see an example of a shortened IPv6 address in the image below by the blue arrow:
![image](https://academy.hackthebox.com/storage/modules/81/Addressing.png)

Along with a much larger address space, IPv6 provides: 
* Better support for Multicasting (sending traffic from one to many),
* Global addressing per device, 
* Security within the protocol in the form of IPSec Simplified, 
* Packet headers allow for easier processing and move from connection to connection without being re-assigned an address.
#### IPv6 Addressing Types
IPv6 uses four main types of addresses within its schema:

| **Type**    | **Description**                                                                |
| ----------- | ------------------------------------------------------------------------------ |
| `Unicast`   | Addresses for a single interface.                                              |
| `Anycast`   | Addresses for multiple interfaces, where only one of them receives the packet. |
| `Multicast` | Addresses for multiple interfaces, where all of them receive the same packet.  |
| `Broadcast` | Does not exist and is realized with multicast addresses.                       |

When thinking about each address type, it is helpful to remember that: 
* ``Unicast`` traffic is host to host, 
* ``Multicast`` is one to many, 
* ``Anycast`` is one to many in a group where only one will answer the packet. (think load balancing).

Even with its current state providing many advantages over IPv4, the adoption of IPv6 has been slow to catch on:
![[ipv6-adoption.png]]

At the time of writing, according to statistics published by Google, the adoption rate is only around 40 percent globally.
## TCP / UDP, Transport Mechanisms
The Transport Layer (**Layer 4 for OSI** || **Layer 3 for TCP/IP**) has several mechanisms to help ensure the seamless delivery of data from source to destination. 

**Think about the Transport layer as a control hub:**
1. Application data from the higher layers have to traverse down the stack to the Transport layer. 
2. The Transport Layer directs how the traffic will be encapsulated and thrown to the lower layer protocols (``IP`` and ``MAC``). 
3. Once the data reaches its intended recipient, the Transport layer -- working with the Network / Internet layer protocols -- is responsible for reassembling the encapsulated data back in the correct order. 
4. The two mechanisms used to accomplish this task are the **Transmission Control** (`TCP`) and the **User Datagram Protocol** (`UDP`).
#### TCP vs. UDP
Let us take a second to examine these two protocols side by side:

| **Characteristic**         | **TCP**                                                                    | **UDP**                                                                 |
| -------------------------- | -------------------------------------------------------------------------- | ----------------------------------------------------------------------- |
| `Transmission`             | Connection-oriented                                                        | Connectionless. Fire and forget.                                        |
| `Connection Establishment` | TCP uses a three-way handshake to ensure that a connection is established. | UDP does not ensure the destination is listening.                       |
| `Data Delivery`            | Stream-based conversations                                                 | packet by packet, the source does not care if the destination is active |
| `Receipt of data`          | Sequence and Acknowledgement numbers are utilized to account for data.     | UDP does not care.                                                      |
| `Speed`                    | TCP has more overhead and is slower because of its built-in functions.     | UDP is fast but unreliable.                                             |

By looking at the table above, we can see that TCP and UDP provide two very different data transmission methods. 
* ``TCP`` is considered a more reliable protocol since it allows for error checking and data acknowledgment as a normal function. 
* ``UDP`` is a quick, fire, and forget protocol best utilized when we care about speed over quality and validation.

To put this into perspective: 
* TCP is utilized when moving data that requires completeness over speed. 
	* For example, when we use Secure Shell (`SSH`) to connect from one host to another, a connection is opened that stays active while you issue commands and perform actions. 
	* This is a function of TCP, ensuring our conversation with the distant host is not interrupted. 
	* If it does get interrupted for some reason, TCP will not reassemble a partial fragment of a packet and send it to the application. 
		* **We can avoid errors this way**. 
	* What would happen if we issued a command like `sudo passwd user` to change the user's password on a remote host, and during the change, part of the message drops. 
* If this were over UDP:
	* We would have no way of knowing what happened to the rest of that message and potentially mess up the user's password or worse. 

TCP helps prevent this by acknowledging each packet received to ensure the destination host has acquired each packet before assembling the command and sending it to the application for action.

--- 

On the other hand, when we require quick responses or utilize applications that require speed over completeness, UDP is our answer. Take streaming a video, for example:
* The user will not notice a pixel or two dropped from a streaming video. 
* We care more about watching the video without it constantly stopping to buffer the next piece. 

Another example of this would be DNS:
* When a host requests a record entry for ``inlanefreight.com``, the host is looking for a quick response to continue the process it was performing. 
* The worst thing that happens if a DNS request is dropped is that it is reissued. No harm, no foul. 
	* The user will not receive corrupted data because of this drop.

UDP traffic appears like regular traffic; it is a single packet, with no response or acknowledgment that it was sent or received, so there is not much to show here. 

However, we can take a look at TCP and how it establishes connections.
## TCP Three-way Handshake
One of the ways TCP ensures the delivery of data from server to client is the utilization of sessions. 
* These sessions are established through what is called a **three-way handshake**.
	* To make this happen, TCP utilizes an option in the TCP header called **flags**. 

We will not deep dive into TCP flags now; know that the common flags we will see in a three-way handshake are: 
* **Synchronization** (`SYN`), and 
* **Acknowledgment** (`ACK`). 

When a host requests to have a conversation with a server over TCP;
1. The `client` sends a packet with the ``SYN`` flag set to on along with other negotiable options in the TCP header.
    1. This is a **synchronization** packet. It will only be set in the first packet from host and server and enables establishing a session by allowing both ends to agree on a sequence number to start communicating with.
    2. This is crucial for the tracking of packets. Along with the sequence number sync, many other options are negotiated in this phase to include **window size**, **maximum segment size**, and **selective acknowledgments**.
2. The `server` will respond with a TCP packet that includes a ``SYN`` flag set for the sequence number negotiation and an ``ACK`` flag set to **acknowledge** the previous ``SYN`` packet sent by the host.
    1. The server will also include any changes to the TCP options it requires set in the **options fields** of the TCP header.
3. The `client` will respond with a TCP packet with an ``ACK`` flag set agreeing to the negotiation.
    1. This packet is the end of the **three-way handshake** and established the connection between client and server.

Let us take a quick look at this in action to be familiar with it when it appears in our packet output later on.
#### TCP Session Establishion
![[three-way-handshake.png]]

When examining this output, we can see the **start of our handshake**:

**On line 1:** 
* Looking at the information highlighted in the `red box`, we can see 
	* Our initial ``SYN`` flag is set. 
* If we look at the port numbers underlined in `green`, we can see two numbers, 
	* `57678`, and 
	* `80`. 
* The first number is the **random high port number** in use by the client, and the second is the well-known port for HTTP used by the server to listen for incoming **web request connections**. 

**In line 2:** 
* We can see the server's response to the client with an `SYN / ACK` packet sent to the same ports. 

**On line 3:** 
* We can see the client **acknowledge** (``ACK``) the server's **synchronization** (`SYN`) packet to establish the connection.

**Packet 4**:
* We can see, that the HTTP request was sent, and a session is established to stream the data for the image requested. 

We can see as the stream continues that TCP sends acknowledgments for each chunk of data sent. This is an example of typical TCP communication.

We have seen how a session is established with TCP; now, let us examine how a session is concluded.
#### TCP Session Conclusion
![[session-teardown.png]]

In the image above, a set of packets similar to our **three-way handshake** visible at the end of the output. This is how TCP gracefully shuts connections. 

* Another flag we will see with TCP is the `FIN` flag. 
	* It is used for signaling that the data transfer is finished and the sender is requesting termination of the connection. 
1. The client acknowledges the receipt of the data and then sends a `FIN` and `ACK` to begin session termination. 
2. The server responds with an acknowledgment (``ACK``) of the ``FIN`` and sends back its own ``FIN``. 
3. Finally, the client acknowledges (`ACK`) the session is complete and closes the connection. 
4. Before session termination, we should see a packet pattern of:
	1. `FIN, ACK`,
	2. `FIN, ACK`,
	3. `ACK`

If we look at the image above detailing a session, we will see that this is the case. An output similar to this is considered an adequately terminated connection.
# Networking Primer - Layers 5-7
We have seen how lower-level networking functions, now let us look at some of the upper layer protocols that handle our applications. 

It takes many different applications and services to maintain a network connection and ensure that data can be transferred between hosts. This section will outline just a vital few.
## HTTP
**Hypertext Transfer Protocol** (`HTTP`) is a stateless Application Layer protocol that has been in use since 1990. 
* HTTP enables the transfer of data in clear text between a client and server over TCP. 
* The client would send an HTTP request to the server, asking for a resource. 
	* A session is established, and the server responds with the requested media (HTML, images, hyperlinks, video). 
* HTTP utilizes ports ``80`` or ``8000`` over TCP during normal operations. 
	* In exceptional circumstances, it can be modified to use alternate ports, or even at times, UDP.
#### HTTP Methods
* To perform operations such as: 
	* fetching webpages, 
	* requesting items for download, or 
	* posting your most recent tweet 
* All of these require the use of specific methods. 
	* These methods define the actions taken when requesting a URI. 

Methods:

| **Method** | **Description**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `HEAD`     | `required` is a safe method that requests a response from the server similar to a `GET` request except that the message body is not included. It is a great way to acquire more information about the server and its operational status.                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| `GET`      | `required` GET is the most common method used. It requests information and content from the server. For example, `GET http://10.1.1.1/Webserver/index.html` requests the ``index.html`` page from the server based on our supplied URI.                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| `POST`     | `optional` POST is a way to submit information to a server based on the fields in the request. For example, submitting a message to a Facebook post or website forum is a POST action. The action taken can vary based on the server.                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| `PUT`      | `optional` PUT will take the data appended to the message and place it under the requested URL. If an item does not exist there already, it will create one with the supplied data. If an object already exists, the new PUT will be considered the most up-to-date, and the object will be modified to match. The easiest way to visualize the differences between PUT and POST is to think of it like this; PUT will create or update an object at the URI supplied, while POST will create child entities at the provided URI. The action taken can be compared with the difference between creating a new file vs. writing comments about that file on the same page. |
| `DELETE`   | `optional` DELETE does as the name implies. It will remove the object at the given URI.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| `TRACE`    | `optional` Allows for remote server diagnosis. The remote server will echo the same request that was sent in its response if the TRACE method is enabled.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| `OPTIONS`  | `optional` The OPTIONS method can gather information on the supported HTTP methods the server recognizes. This way, we can determine the requirements for interacting with a specific resource or server without actually requesting data or objects from it.                                                                                                                                                                                                                                                                                                                                                                                                             |
| `CONNECT`  | `optional` CONNECT is reserved for use with Proxies or other security devices like firewalls. Connect allows for tunneling over HTTP. (`SSL tunnels`)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |

Notice that we have `required` or `optional` listed beside each method. As a requirement by the standard, ``GET`` and ``HEAD`` must always work and exist with standard HTTP implementations. This is true only for them. 

The methods ``TRACE``, ``OPTIONS``, `DELETE`, `PUT` and `POST` are optional functionalities one can allow. 
* An example of this is a **read-only webpage** like a blog post. 
	* The client PC can **request** a resource from the page but **not** modify, add, or delete the resource or resources.
## HTTPS
**HTTP Secure** (`HTTPS`) is a modification of the HTTP protocol designed to utilize Transport Layer Security (`TLS`) or Secure Sockets Layer (`SSL`) with older applications for data security. 

* **TLS** is utilized as an encryption mechanism to secure the communications between a client and a server. 
	* **TLS** can wrap regular HTTP traffic within **TLS**, which means that we can encrypt our entire conversation, not just the data sent or requested.
	* Before the **TLS** mechanism was in place, we were vulnerable to **Man-in-the-middle** attacks and other types of reconnaissance or hijacking,
		* Meaning anyone in the same **LAN** as the client or server could view the web traffic if they were listening on the wire. 
	* We can now have security implemented in the browser enabling everyone to encrypt their web habits, search requests, sessions or data transfers, bank transactions, and much more.

Even though it is **HTTP** at its base, **HTTPS** utilizes ports ``443`` and ``8443`` instead of the standard port ``80``. 
* This is a simple way for the client to signal the server that it wishes to establish a secure connection. 

Let's look at an output of HTTPS traffic and discern how a `TLS handshake` functions for a minute.
#### TLS Handshake Via HTTPS
![[https.png]]
In the first few packets, we can see that the client establishes a session to the server using port ``443`` **boxed in blue**. 
* This signals the server that it wishes to use **HTTPS** as the application communication protocol.

Once a session is initiated via ``TCP``, a **TLS ClientHello** is sent next to begin the TLS handshake. 
* During the handshake, several parameters are agreed upon, including:
	* session identifier, 
	* peer x509 certificate, 
	* compression algorithm to be used, 
	* the cipher spec encryption algorithm, 
	* if the session is resumable, and 
	* a 48-byte master secret shared between the client and server to validate the session.

Once the session is established, all data and methods will be sent through the **TLS** connection and appear as ``TLS Application Data`` **as seen in the red box**. 

**TLS** is still using ``TCP`` as its transport protocol, so we will still see acknowledgment (`ACK`) packets from the stream coming over port ``443``.

To summarize the handshake:
1. Client and server exchange **hello** messages to agree on connection parameters.
2. Client and server exchange necessary **cryptographic parameters** to establish a pre-master secret.
3. Client and server will exchange **x.509 certificates** and **cryptographic information** allowing for authentication within the session.
4. Generate a master secret from the pre-master secret and exchanged random values.
5. Client and server issue negotiated security parameters to the record layer portion of the TLS protocol.
6. Client and server verify that their peer has calculated the same security parameters and that the handshake occurred without tampering by an attacker.

Encryption in itself is a complex and lengthy topic. This section is a simple summary of how HTTP and TLS provide security within the HTTPS application protocol. 
![[tls-ssl-handshake.png]]
## FTP
**File Transfer Protocol** (`FTP`) is an **Application Layer** protocol that enables quick data transfer between computing devices. 
* **FTP** can be utilized from:
	* The command-line, 
	* Web browser, or 
	* A graphical **FTP** client such as **FileZilla**. 
* **FTP** itself is established as an insecure protocol.
	* Most users have moved to utilize tools such as **SFTP** to transfer files through secure channels. 
* As a note moving into the future, most modern web browsers have phased out support for **FTP** as of 2020.

When we think about communication between hosts, we typically think about a client and server talking over a single socket. Through this socket, both the client and server send commands and data over the same link. 

In this aspect, **FTP** is unique since it utilizes multiple ports at a time. 
* FTP uses ports ``20`` and ``21`` over ``TCP``. 
	* Port ``20`` is used for data transfer, 
	* While port ``21`` is utilized for issuing commands controlling the **FTP** session. 
* In regards to authentication:
	* **FTP** supports user authentication as well as allowing **anonymous** access if configured.
* **FTP** is capable of running in two different modes, 
	* `active` 
	* and `passive`. 
* **Active** is the default operational method utilized by **FTP**, meaning that the server listens for a control command `PORT` from the client, stating what port to use for data transfer. 
* **Passive** mode enables us to access **FTP** servers located behind firewalls or a NAT-enabled link that makes direct ``TCP`` connections impossible. 
	* In this instance, the client would send the `PASV` command and wait for a response from the server informing the client what IP and port to utilize for the data transfer channel connection.
#### FTP Command & Response Examples
![[ftp-example.png]]
* In the image above
	* **Green arrows** show several examples of requests issued over the FTP command channel, 
	* **Blue arrows** are the responses sent back from the FTP server. 

When looking at FTP traffic, some common commands we can see passed over port ``21`` include:
#### FTP Commands

|**Command**|**Description**|
|---|---|
|`USER`|specifies the user to log in as.|
|`PASS`|sends the password for the user attempting to log in.|
|`PORT`|when in active mode, this will change the data port used.|
|`PASV`|switches the connection to the server from active mode to passive.|
|`LIST`|displays a list of the files in the current directory.|
|`CWD`|will change the current working directory to one specified.|
|`PWD`|prints out the directory you are currently working in.|
|`SIZE`|will return the size of a file specified.|
|`RETR`|retrieves the file from the FTP server.|
|`QUIT`|ends the session.|

This is not an exhaustive list of the possible FTP control commands that could be seen. These can vary based on the FTP application or shell in use. 
## SMB
Server Message Block (`SMB`) is a protocol most widely seen in Windows enterprise environments that enables sharing resources between hosts over common networking architectures. 
* **SMB** is a connection-oriented protocol that requires user authentication from the host to the resource. 
	* This is to ensure the user has permissions to use that resource or perform actions. 
* In the past, **SMB** utilized **NetBIOS** as its transport mechanism over ``UDP`` ports ``137`` and ``138``. 
* Since modern changes, **SMB** now supports direct ``TCP`` transport over port ``445``, **NetBIOS** over ``TCP`` port ``139``, and even the ``QUIC`` protocol.

As a user, **SMB** provides us easy and convenient access to resources like printers, shared drives, authentication servers, and more. For this reason, **SMB** is very attractive to potential attackers as well.

Like any other application that uses ``TCP`` as its transport mechanism, it will perform standard functions like the **three-way handshake** and acknowledging (`ACK`) received packets. 

Let us take a second to look at some SMB traffic to familiarize ourselves.
#### SMB On The Wire
![image](https://academy.hackthebox.com/storage/modules/81/smb-actions.png)
* In the image above: 
	* In the **orange boxes** it performs the ``TCP`` handshake each time it establishes a session. 
	* When looking at the source and destination ports in the **blue box**:
		* Port ``445`` is being utilized, signaling SMB traffic over ``TCP``. 
	* The **Green box's** info field tells us a bit about what is happening in the SMB communication. 
		* In this example, there are many errors, which is an example of something to dig deeper into. 
		* One or two auth failures from a user is relatively common, but a large cluster of them repeating can signal a potential unauthorized individual trying to access a user's account or use their credentials to move. 

This is just one example of **SMB** use. 

Another common thing we will see is **file-share access** between servers and hosts. For the most part, this is regular communication. However, if we see a host access file shares on other hosts, this is not common. Please pay attention to who is requesting connections, where to, and what they are doing.
# Analysis
## The Analysis Process
Traffic Analysis is a **detailed examination of an event or process**, determining its origin and impact, which can be used to trigger specific precautions and/or actions to support or prevent future occurrences. 

With network traffic, this means:
* breaking down the data into understandable chunks, 
* Examining it for anything that deviates from regular traffic, 
* Examining for potentially malicious traffic such as unauthorized remote communications from the internet over ``RDP``, ``SSH``, or ``Telnet``, 
* Unique instances preceding network issues. 

While performing our analysis, we are also looking to see what the trends look like within the traffic and determine if it matches typical operational traffic.

Without the ability to monitor traffic, we are working with a massive piece of the puzzle missing. 

Analytics on the below are all crucial pieces that provide us a way to see and correct issues before or soon after they happen: 
* network usage, 
* top-talking hosts and servers, and 
* internal communications 

Visibility is probably the most beneficial thing it provides. With this visibility, we can capture traffic over different periods to set a baseline for our environment.

This baseline makes it easier to see when a change has occurred. 
* In more advanced implementations for ``NTA`` that include other tools like:
	* ``IDS``/``IPS``, 
	* **firewalls**, 
	* **host and network logs**, 
	* and **additional information** being fed into tools like ``Splunk`` or ``ELK Stack``, 

The tools help us quickly alert on malicious actions happening since many defensive tools have signatures built for most of the common attacks and toolkits.

* Having proper defensive capabilities is vital for everyone, but what about daily operations? How can NTA help us? 
	* Watching network traffic live can make it easy to troubleshoot a connection issue or determine if our infrastructure and the corresponding protocols are functioning correctly. 
	* If we can see where the traffic is going, we can determine if there is an issue.

Lastly, this is a dynamic skill, and using automated tools to aid us is perfectly fine. Just do not rely on them solely since malicious actors are finding ways to bypass security measures all the time. 
### Analysis Dependencies
Traffic capturing and analysis can be performed in two different ways, `active` or `passive`. Each has its dependencies. 
* With: 
	* ``Passive``: We are just copying data that we can see without directly interacting with the packets. 
	* `Active`: For active traffic capture and analysis, the needs are a bit different. 
		* Active capture requires us to take a more hands-on approach. 
		* This process can also be referred to as `in-line` traffic captures.

With both, how we analyze the data is up to us. We can perform the capture and analysis once done, or we can perform analysis in real-time while the traffic is live. 

The table below lays out the dependencies for each.
#### Traffic Capture Dependencies

| **Dependencies**                          | **Passive** | **Active** | **Description**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| ----------------------------------------- | ----------- | ---------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Permission`                              | ☑           | ☑          | Depending on the organization, capturing data can be against policy or even against the law in some sensitive areas like healthcare or banking. Be sure always to obtain permission in writing from someone with the proper authority to grant it to you..                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| `Mirrored Port`                           | ☑           | ☐          | A switch or router network interface configured to copy data from other sources to that specific interface, along with the capability to place your ``NIC`` into **promiscuous** mode. Having packets copied to our port allows us to inspect any traffic destined to the other links we could normally not have visibility over. Since ``VLANs`` and **switch ports** will not forward traffic outside of their **broadcast domain**, we have to be connected to the segment or have that traffic copied to our specific port. When dealing with wireless, ``passive`` can be a bit more complicated. We must be connected to the ``SSID`` we wish to capture traffic off of. Just passively listening to the airwaves around us will present us with many ``SSID`` broadcast advertisements, but not much else. |
| `Capture Tool`                            | ☑           | ☑          | A way to ingest the traffic. A computer with access to tools like ``TCPDump``, ``Wireshark``, ``Netminer``, or others is sufficient. Keep in mind that when dealing with ``PCAP`` data, these files can get pretty large quickly. Each time we apply a filter can be a resource-intensive process, so make sure the host has abundant resources.                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| `In-line Placement`                       | ☐           | ☑          | Placing a **Tap in-line** requires a topology change for the network you are working in. The source and destination hosts will not notice a difference in the traffic, but for the sake of routing and switching, it will be an invisible next hop the traffic passes through on its way to the destination.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| `Network Tap or Host With Multiple NIC's` | ☐           | ☑          | A computer with two NIC's, or a device such as a **Network Tap** is required to allow the data we are inspecting to flow still. Think of it as adding another router in the middle of a link. To **actively** capture the traffic, we will be duplicating data directly from the sources. The best placement for a tap is in a ``Layer 3`` link between switched segments. It allows for the capture of any traffic routing outside of the local network. A switched port or ``VLAN`` segmentation does not filter our view here.                                                                                                                                                                                                                                                                                 |
| `Storage and Processing Power`            | ☑           | ☑          | You will need plenty of storage space and processing power for traffic capture off a tap. Much more traffic is traversing a ``Layer 3`` link than just inside a switched ``LAN``. Think of it like this; When we **passively** capture traffic inside a ``LAN``, it's like pouring water into a cup from a water fountain. It's a steady stream but manageable. **Actively** grabbing traffic from a routed link is more like using a water hose to fill up a teacup. There is a lot more pressure behind the flow, and it can be a lot for the host to process and store.                                                                                                                                                                                                                                        |

The last dependency is more of a recommendation than a requirement. 
>[!Important]
>**Having an understanding of how day-to-day traffic flows is critical to being successful.** 

It is possible to perform traffic analysis without one, but it will be much harder and time-consuming. 

The baseline will enable us to quickly filter out common traffic for that network while performing our analysis. Doing so can speed our process up and help spot the outliers or issues much sooner. 

**Let us look at this scenario for a second:**
>[!Info]
You are a network admin for a large corporation with several thousand employees on campus. It has been brought to your attention that a segment of your network is having connectivity issues. Several of those hosts are reporting extremely high latency, along with new files appearing on their desktops. To start getting a picture of what is happening, you attach a computer to that segment and start a capture. After a few minutes have passed, you stop the capture and start your analysis.

**Now consider this:** 
* Without a baseline of our daily network traffic, how do we know what is typical for that network? 
* We grabbed a ton of information during the capture timeframe, and we need to clear some of it away. 
* This clearing process can take a lot of time since we will have to examine every conversation to ensure it is O.K., determine if the hosts we see belong on the network or are rogue assets, among much more. 

This process quickly became a daunting task, right?

With this scenario and access to a network baseline, we can quickly strip away known-good communications. 

Utilizing data analysis tools such as the ``top talkers'`` module in ``Wireshark`` can help identify hosts that may be sending a large amount of data. We can check this against the host's normal baseline to determine if it is out of character. 

Another way could be to look at connections between internal hosts or common and uncommon ports. Since we could clear our view, we can now see that several user hosts connect on ports ``8080`` and ``445``. The ports themselves are not weird, but the fact that it is two user PCs talking to each other over these ports is. 

Web traffic usually flows from a **host to a hosted web server** or an intranet web server hosting business applications. The same can be said for SMB traffic. It is very suspicious to see two hosts talking to each other over this port. With what we now know, we can quickly send up a trouble ticket looking for help handling a potential breach now.

When talking about network intrusions, the faster we can get visibility, the less potential damage to our network. Be sure to clearly understand how traffic flows in our networks and how protocols commonly act.
## Analysis in Practice
This section will break down a workflow for performing traffic analysis.

This is not an exact science. It can be a very dynamic process and is not a direct loop. It is greatly influenced by what we are looking for (network errors vs. malicious actions) and where you have visibility into your network. 

Analysis can be distilled down to a few basic tenets, however.
### Descriptive Analysis
**Descriptive analysis** is an essential step in any data analysis. It serves to describe a data set based on individual characteristics. It helps to detect possible errors in data collection and/or outliers in the data set.
1. `What is the issue?`
    - Suspected breach? Networking issue?
2. `Define our scope and the goal.` (**what are we looking for? which time period?**)
    - **Target:** multiple hosts potentially downloading a malicious file from ``bad.example.com``
    - **When:** within the last **48 hours + 2 hours from now**.
    - **Supporting info:** filenames/types '``superbad.exe``' '``new-crypto-miner.exe``'
3. `Define our target(s) (net / host(s) / protocol)`
    - Scope: ``192.168.100.0/24`` network, protocols used were ``HTTP`` and ``FTP``.

Using our workflow, we will determine our issue, what we are looking for, when, and where to find it. Descriptive analysis covers these critical concepts for our analysis.
### Diagnostic Analysis
**Diagnostic analysis** clarifies the causes, effects, and interactions of conditions. In doing so, it provides insights that are obtained through correlations and interpretation. 

Characteristic here is a backward-looking view, as in the closely related descriptive analytics, with the subtle difference that it tries to find reasons for events and developments.
1. `Capture network traffic`
    - Plug into a link with access to the ``192.168.100.0/24`` network to capture live traffic to try and grab one of the **executable** in transfer. See if an admin can pull ``PCAP`` and/or **net-flow data** from our ``SIEM`` for the historical data.
2. `Identification of required network traffic components (filtering)`
    - Once we have traffic, filter out any packets not needed for this investigation to include; any traffic that matches our common baseline and keep anything relevant to the scope of the investigation. For example, ``HTTP`` and ``FTP`` from the **subnet**, anything transferring or containing a ``GET`` request for the suspected **executable** files.
3. `An understanding of captured network traffic`
    - Once we have filtered out the noise, it is time to dig for our targets—filter on things like `ftp-data` to find any files transferred and reconstruct them. For ``HTTP``, we can filter on `http.request.method == "GET"` to see any ``GET`` requests that match the filenames we are searching for. This can show us who has acquired the files and potentially other transfers internal to the network on the same protocols.

By capturing traffic around the source of our issue, clearing out any known good data, and then taking the time to inspect and understand what is left, we can determine if it is the cause of our problem. 

We are validating the cause of our problems and examining the events surrounding them.
### Predictive Analysis
By evaluating historical and current data, predictive analysis creates a predictive model for future probabilities. 

Based on the results of **descriptive** and **diagnostic analyses**, this method of data analysis makes it possible to identify trends, detect deviations from expected values at an early stage, and predict future occurrences as accurately as possible.
1. `Note-taking and mind mapping of the found results`
    - Annotating everything we do, see, or find throughout the investigation is crucial. Ensure we are taking ample notes, including:
	    - Timeframes we captured traffic during.
	    - Suspicious hosts within the network.
	    - Conversations containing the files in question. (to include timestamps and packet numbers)
2. `Summary of the analysis (what did we find?)`
    - Finally, summarize what we have found explaining the relevant details so that superiors can decide to quarantine the affected hosts or perform more significant incident response.
    - Our analysis will affect decisions made, so it is essential to be as clear and concise as possible.

By performing an evaluation of the data we have found, comparing it to our baseline traffic, and known bad data such as markers of infiltration or exploitation (like signatures for viruses and other hacking tools), we are performing **Predictive Analysis**. 

In this process, we paint a clear picture so that appropriate actions can be taken in response.
### Prescriptive Analysis
Prescriptive analysis aims to narrow down what actions to take to eliminate or prevent a future problem or trigger a specific activity or process. 

Using the results of our workflow, we can make sound decisions as to what actions are required to solve the problem and prevent it from happening again.

To **prescribe** a solution is the culmination of this workflow. Once done and the problem is solved, it is prudent to reflect on the entire process and develop lessons learned. 

These lessons, when documented, will enable us to make our processes stronger—document what was done correctly, what actions failed to help, and what could improve.

This workflow is an example of how to begin the analysis process on captured traffic. Above we broke it down into its parts to explain where they fit within the analysis process and with which type of analysis it belongs. We include it here again as a whole so that it can serve as a template:
1. `What is the issue?`
    - Suspected breach? Networking issue?
2. `Define our scope and the goal (what are we looking for? which time period?)`
    - **target**: multiple hosts potentially downloading a malicious file from ``bad.example.com``
    - **when**: within the last **48 hours + 2 hours from now**.
    - supporting info: filenames/types '``superbad.exe``' '``new-crypto-miner.exe``'
3. `Define our target(s) (net / host(s) / protocol)`
    - scope: ``192.168.100.0/24`` network protocols used were ``HTTP`` and ``FTP``.
4. `Capture network traffic`
    - plug into a link with access to the ``192.168.100.0/24`` network to capture live traffic to try and grab one of the **executable** in transfer. See if an admin can pull ``PCAP`` and/or **net-flow data** from our ``SIEM`` for the historical data.
5. `Identification of required network traffic components (filtering)`
    - once we have traffic, filter out any traffic not needed for this investigation to include; any traffic that matches our common baseline and keep anything relevant to the scope. **`HTTP` and ``FTP`` from the subnet, anything transferring or containing a ``GET`` request for the suspected executable files.**
6. `An understanding of captured network traffic`
    - Once we have filtered out the noise, it's time to dig for our targets—filter on things like `ftp-data` to find any files transferred and reconstruct them. For ``HTTP``, we can filter on `http.request.method == "GET"` to see any ``GET`` requests that match the filenames we are searching for. This can show us who has acquired the files and potential other transfers internal to the network on the same protocols.
7. `Note-taking and mind mapping of the found results.`
    - Annotating everything we do, see, or find throughout the investigation is crucial. Ensure we are taking ample notes, including:
	    - Timeframes we captured traffic during.
	    - Suspicious hosts within the network.
	    - Conversations containing the files in question. (to include timestamps and packet numbers)
8. `Summary of the analysis (what did we find?)`
    - Finally, **summarize** what has been found, explaining the relevant details so that superiors can make an informed decision to quarantine the affected hosts or perform more significant incident response.
    - Our analysis will affect decisions made, so it is essential to be as clear and concise as possible.

Often this process is not a once-and-done kind of thing. It is usually **cyclic**, and we will need to rerun steps based on our analysis of the original capture to build a bigger picture. 

This could have been a much larger attack than what is in the examples.

Suppose a full-scale incident response is deemed necessary. In that case, we may have to reanalyze the ``PCAP`` previously captured to look at any conversations that involve the affected hosts within several minutes of the **executable** transfer to ensure it did not spread over another route, as an example.
### Key Components of an Effective Analysis
#### 1. Know your environment
First, know the environment. 

If we are unsure if a host belongs in the network, how can we determine if it is rogue or not? 

Keeping asset inventories and network maps is vital. These will aid in the analysis process.
#### 2. Placement is Key
Next, the placement of our host for capturing traffic is a critical thing. 

Closest to the source of the issue is the ideal placement of our capturing tool. If the traffic in question is coming from the internet, listening to the inbound links is a great way to see the complete picture. It is as close to the source as we can get. 

If the problem seems to be isolated to one host on our internal network, try placing the capture tools in the same segment as the problem host and see what traffic is happening within the segment.
#### 3. Persistence
Persistence is the next critical component for us. The issue will not always be easy to spot. It may not even be a frequent event on the network. 

**For example:**
An attacker's **Command and Control server** reaching out to the victim's computers may only happen on a time interval of **once every several hours**, or even **once a day** or less. This means that if we did not catch it the first time around, it might be a while before it appears in our logs. 

Don't lose the drive to find the problem. It could mean the difference between stopping the attacker and a full-scale breach like a ransomware attack.
### Analysis Approach
We have spent some time discussing the analysis process and how to start a basic workflow when performing our tasks. Let's take a second to discuss some easy wins when looking at traffic and finding problems.

Start with `standard protocols first` and work our way into the `austere and specific` only to the organization. Most attacks will come from the internet, so it has to access the internal net somehow. This means there will be traffic generated and logs written about it. HTTP/S, FTP, E-mail, and basic TCP and UDP traffic will be the most common things seen coming from the world. Start at these and clear out anything that is not necessary to the investigation. After these, check standard protocols that allow for communications between networks, such as SSH, RDP, or Telnet. When looking for these types of anomalies, be mindful of the security policy of the network. Does our organization's security plan and implementations allow for RDP sessions that are initiated outside the enterprise? What about the use of Telnet?

Look for `patterns`. Is a specific host or set of hosts checking in with something on the internet at the same time daily? This is a typical Command and Control profile setup that can easily be spotted by looking for patterns in our traffic data.

Check anything `host to host` within our network. In a standard setup, the user's hosts will rarely talk to each other. So be suspicious of any traffic that appears like this. Typically hosts will talk to infrastructure for IP address leases, DNS requests, enterprise services and to find its route out. We will also see hosts talking with local webservers, file shares, and other critical infrastructure for the environment to function like Domain controllers and authentication apps.

Look for `unique` events. Things like a host who usually visits a specific site ten times a day changing its pattern and only doing so once is curious. Seeing a different User-Agent string not matching our applications or hosts talking to a server out on the internet is also something to be concerned with. A random port only being bound once or twice on a host is also of note. This could be an opening for things like C2 callbacks, someone opening a port to do something non-standard, or an application showing abnormal behavior. In large environments, patterns are expected, so anything sticking out warrants a look.

`Don't be afraid to ask for help.` This may seem overstated and obvious, but after a bit of time staring at packet captures, things can blend together, and we may not see the whole picture. Having a second set of eyes on the data can be a huge help in spotting stuff that may get glossed over.

---
#### Summary
In summary, the analysis process is a very dynamic task, and our days will never be the same. Keep learning, understand what is going on around us, and as your skills grow, so will the ability to detect threats. 

This process does not solely rely on the use of tools such as ``tcpdump`` and ``Wireshark``. There are many helpful tools like ``Snort``, ``Security Onion``, ``Firewalls``, and ``SIEMs`` that can help enrich our understanding of the environment and provide better protection.