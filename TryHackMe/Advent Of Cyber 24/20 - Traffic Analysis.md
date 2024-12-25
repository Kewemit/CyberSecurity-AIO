
- Investigate network traffic using Wireshark ✅
- Identify indicators of compromise (IOCs) in captured network traffic ✅
- Understand how C2 servers operate and communicate with compromised systems ✅

**C2** = **Command and Control** (**C2**) Infrastructure are a set of programs used to communicate with a **victim machine**. This is comparable to a **reverse shell**, but is generally more advanced and often communicate via common network protocols, like **HTTP, HTTPS** and **DNS**.

The main difference between a reverse shell and C2, for reverse shells the attackers become the listeners, waiting for a connection. In contrast for **C2s**, the targets become the listeners, similarly waiting for a connection. Likewise, for reverse shells, the attackers have to open their own port for inbound connections, whereas for C2s, the attackers find an open port on the machine and connect with that port.
# Investigating the Depths
Before we dig deeper, we must learn a few essential things about **C2 communication**. Whenever a machine is compromised, the **command and control server** (**C2**) drops its **secret agent** (**payload**) into the **target machine**. 

This **secret agent** is meant to obey the instructions of the **C2 server**. These instructions include **executing malicious commands, exfiltrating essential files**, and **much more**.

Interestingly, after getting into the system, the **secret agent** has a way to keep the **C2** updated on its current status. It sends a packet to the **C2** every few seconds to let it know it is active. These packets are known as **beacons**.

For this section, we will use ``Wireshark``. It is beneficial for understanding the communications between a **compromised machine and a C2 server**.

If you are unfamiliar with it, here are some key capabilities you’ll see in this room:
- Wireshark can analyze traffic and display the information in an easy-to-navigate format regardless of the protocols used (e.g., **HTTP**, **TCP**, **DNS**).
- Wireshark can reconstruct back-and-forth conversations in a network.
- Wireshark allows easy filtering to narrow down essential details.
- Wireshark can also export and analyse objects that are transferred over the network.
- Of course, Wireshark has more capabilities not listed here.
# Diving Deeper
Now that we have a better idea of what **C2** traffic looks like, we double-click on the file “``C2_Traffic_Analysis``” on the Desktop. This will automatically open the file via `Wireshark`:
![[Pasted image 20241225191025.png]]
That's traffic! 

We already suspect that this machine is compromised. So, let’s narrow down our list so that it will only show traffic coming from the **IP address** of **Marta May Ware’s machine**. 
To do this, click inside the **Display Filter Bar** on the top, type "`ip.src == 10.10.229.217`":
![[Pasted image 20241225191119.png]]
It still leaves a lot of traffic, but at least we can now focus our analysis on outbound traffic.

If we scroll down a bit, we will find some interesting packets, specifically those highlighted with an arrow, as shown below:
![[Pasted image 20241225191210.png]]
**Initial**? **Command**? **Exfiltrate**? That is sure to be something!
# Message Received
If we click on the `POST /initial` packet, more details will be shown on the bottom panes. These panes will show more detailed information about the **packet frame**. It shows relevant details such as frame number (**440**), the destination IP (**10.10.123.224**), and more:

You can expand each detail if you want, but the critical area to focus on is the **lower-right view**, the “``Packet Bytes``” pane:
![[Pasted image 20241225191405.png]]
This pane shows the bytes used in the communication in **hexadecimal** and **ASCII** character formats. The **latter** format shows **readable text**, which can be helpful in investigations.

If we right-click on the `POST /initial` packet (**Frame 440**) and select `Follow` > `HTTP Stream`, a new pop-up window will appear containing the **back-and-forth HTTP communication** relevant to the specific session:
![[Pasted image 20241225191652.png]]
This feature is useful when you need to view all **requests** and **responses** between the **client** and the **server**, as it helps you understand the complete context of the communication. 

The text highlighted in **red** is the message sent from the source to the destination, and **blue** is the opposite. So, based on the screenshot above, we can see that after the message “``I am in Mayor!``” was sent, a response “``Perfect!``" was sent back. But let’s **not** stop here. 

Other interesting **HTTP** packets were sent to the same destination IP. If you follow the **HTTP Stream** for the `GET /command` packet, you’ll see a request to the same IP destination:
![[Pasted image 20241225192051.png]]
Interestingly, the reply that came back was a **command** commonly used in **Windows and Linux** systems to **display the current user’s information**. This communication suggests that the destination is attempting to **gather information** about the compromised system.

Usually, the reply from a **C2 server** contains the command, instructing the malicious program what to do next. However, the type of instruction **depends on** the malicious actor’s **configuration**, intention, and capabilities. These instructions often fall into several categories:

1. **Getting system information:** The attacker may want to know more about the compromised machine to tailor their next moves. This is what we are seeing above.
2. **Executing commands:** If the attacker needs to perform specific actions, they can also send commands directly. However, this is **less stealthy** and easily attracts attention.
3. **Downloading and executing payloads:** The attacker can also send additional payloads to the machine containing additional functionality or tools.
4. **Exfiltrating data:** This is one of the most common objectives. The program may be instructed to steal valuable data such as **sensitive files**, **credentials**, or **personal information**.
# Exfiltrating the Package
If we follow the **HTTP Stream** for the `POST /exfiltrate` packet, we will see a **file exfiltrated to the C2 server**. We can also find some clues inside this file. 

If you check the rest of the ``PCAP``, you’ll find that more interesting packets were captured. Let’s break these down and dive deeper into what we’ve uncovered.

# What's in the Beacon
A typical **C2 beacon** returns regular status updates from the compromised machine to its **C2 server**. The beacons may be sent after regular or irregular intervals to the **C2** as a heartbeat. Here’s how this exchange might look:
- **Secret agent (payload):** “I am still alive. Awaiting any instructions. Over.”
- **C2 server:** “Glad to hear that! Stand by for any further instructions. Over.”

In this scenario, Mayor Malware’s **agent** (**payload**) inside **Marta May Wares’** computer has sent a message that is sent inside all the beacons. Since the content is highly confidential, the **secret agent** encrypts it inside all the beacons, leaving a clue for the **Mayor’s C2** to decrypt it. 

In the current scenario, we can **identify the beacons** by the multiple requests sent to the **C2** from the target machine after regular intervals of time.

The exfiltrated file's content hints at how these encrypted beacons can be decrypted. Using the encryption algorithm with the provided key, we have a potential way to unlock the beacon’s message.
![[Pasted image 20241225193248.png]]
But what exactly are we about to reveal?

Since the beacon is now **encrypted** and we have the key to decrypt it, the [CyberChef](https://gchq.github.io/CyberChef/) tool could help us. We can use this tool for encoding, decoding, encrypting, decrypting, hashing, and much more.

From the [CyberChef's](https://gchq.github.io/CyberChef/) dashboard, you would be utilizing the following panes for decrypting your beacon:
![[Pasted image 20241225193313.png]]
1. **Operations:** Search for AES Decrypt and drag it to the **Recipe** area, which is in the second pane.
2. **Recipe:** This is the area where you would select the mode of encryption, **ECB**, and enter the decryption key you have. Keep the other options as they are.
3. **Input:** Once the Recipe is done, it is time to enter our encrypted beacon into the **Input** area. Copy your encrypted string and paste it here.
4. **Output:** Once you have completed the above steps, you need to click the "Bake" button in the Recipe area. Your encrypted string will be decrypted using the AES ECB decryption with the key you provided, and the output will be displayed in the **Output** area.
![[Pasted image 20241225193513.png]]
