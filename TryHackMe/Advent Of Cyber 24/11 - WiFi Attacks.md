- Understand what Wi-Fi is ✅
- Explore its importance for an organisation✅
- Learn the different Wi-Fi attacks✅
- Learn about the WPA/WPA2 cracking attack✅
## What is Wi-Fi
  * Wi-Fi connects us to the **global network** through **access points** (often **routers**) that broadcast **Wi-Fi signals** with a unique **SSID** (network name). 
* Wi-Fi **password** is called **PSK** (**Pre-Shared Key**). 
* Once you connect to a Wi-Fi you will be assigned an **IP address**, to help you communicate with other devices and identify yourself, which is like becoming a member of a family assigned with a name that the whole family trusts.
* The image below is an example of what connecting to **OK443S SSID** requesting a **PSK** looks like on Windows:
  ![[618b3fa52f0acc0061fb0172-1730574010264.png]]
  
## Wi-Fi's Pivotal Role in Organisations
* Most companies rely on Internet for their business functioning.
* When connecting to the organisation's network, employees form a family of interconnected devices
* The devices inside the network can communicate with each other and request/respond to any request.
## Attacks on Wi-Fi
* **Evil Twin attack:** Attacker creates **fake** access point with a similar name to your Wi-Fi, it cannot be the exact same so if your Wi-Fi is called **"Home_Internet"** the attacker creates a **fake** one called **"Home_Internnet"** so it's difficult to differentiate. The attacker sends **de-authentication packets** to all users connected to the legitimate Wi-Fi access point. When the users get frustrated due to repeated disconnections, they are likely to open the Wi-Fi access point listing and will find a Wi-Fi with almost similar name and greater signal strength. They would connect to it and the attacker could see all their traffic.
* **Rogue Access Point (RAP):** Similar to **Evil Twin**. In this attack, the attacker sets up an open Wi-Fi access point near or inside the organisation's premises to make it available to users with good signal strength. The users in the organisation may accidentally join this network if their devices are set to connect automatically to open Wi-Fi. The attacker can intercept all communication of users within this Wi-Fi
* **WPS attack:** WPS or **Wi-Fi protected setup** was created to allow users to connect to their Wi-Fi using an 8-digit PIN without having to remember complex passwords. However, this 8-digit PIN is vulnerable in some networks due to its insecure configuration. The attack is made my initiating a **WPS Handshake** with the router and capturing the router's response, which contains some data related to the PIN and is vulnerable to brute-force attacks. Some of the captured data is brute-forced, and the PIN is successfully extracted along with the Pre-Shared Key (**PSK**)
* **WPA/WPA2 cracking**: Wi-Fi Protected Access (**WPA**) was created to secure wireless communications via a strong encryption algorithm. However, the security of this algorithm is heavily influenced by the length and complexity of the Pre-Shared Key (PSK/Password). While cracking WPA, attackers start by sending de-authentication packets to a legitimate user of the Wi-Fi network. Once the user disconnects, they try to reconnect to the network, and a 4-way handshake with the router takes place during this time. Meanwhile the attacker turns its Wi-Fi adapter into **monitoring mode** and captures the handshake. After the handshake is captured, the attacker can crack the password by using brute-force or dictionary attacks on the captured handshake file

| Evil Twin attack                                                                                                   | Rogue Access Point                                                                                   | WPS Attack                                                                                     | WPA/WPA2 cracking                                                                            |
| ------------------------------------------------------------------------------------------------------------------ | ---------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------- |
| 1 - Attacker creates fake access point with similar name to a real one (Home -> Honne)                             | 1 - Attacker creates a public open Wi-Fi with good signal.                                           | 1 - Attacker finds a network which utilizes insecurely configured WPS (8-digit PIN)            | 1 - Attacker sends de-auth packets to user of Wi-Fi.                                         |
| 2 - Attacker sends de-auth packets to disconnect users from real Wi-Fi                                             | 2 - Victims might accidentally join network due to the setting "Automatically connect to open Wi-Fi" | 2 - Attacker initiates a **WPS** handshake with the router and captures the router's response  | 2 - When the user reconnects to the network, a 4-way handshake takes place during this time. |
| 3 - Users get frustrated and connect to the fake Wi-Fi thinking its the real deal but better due to better signal. | 3 - Attacker can now Intercept all traffic inside the fake Wi-Fi                                     | 3 - Router's response contains some data related to the PIN and attacker brute-forces the data | 3 - Attacker monitors and captures the handshake into a file.                                |
| 4 - Attacker can now monitor all traffic inside the fake Wi-Fi.                                                    |                                                                                                      | 4 - Attacker successfully extracts PIN and PSK                                                 | 4 - Attacker cracks the password via brute-forcing the handshake file.                       |
**The 4-way Handshake** is a process that helps a client device (e.g. your laptop) and a router confirm they both have the right password or **PSK** before securely connecting. 
**4-Way Handshake:**
* **Router send a challenge:** ask the client to prove it knows the network's password without directly sharing it.
* **Client responds with encrypted info:** Client takes the challenge and uses **PSK** to create encrypted response that only the router can verify if it also has the correct **PSK**.
* **Router verifies and sends confirmation:** If the client's response matches what it expects, it knows the client has the right **PSK**. The router then sends its own confirmation to the client.
* **Final check and connection established:** The client verifies the router's response and if everything matches, they finish setting up the secure connection.
This handshake doesn't **directly** reveal the **PSK** itself but involves encrypted exchanges that depend on the **PSK**.

## WPA2 Cracking example:
With the handshake data, the attacker can use it as a basis to attempt offline brute-force or dictionary attacks. Essentially, they try different possible passwords and test each one to see if it would produce the captured handshake data, eventually cracking the PSK if they get a match.

**The practical** 
Run the command `iw dev`. This will show any wireless devices and their configuration that we have available for us to use:
![[Pasted image 20241214171920.png]]
The device/interface ``wlan2`` is available to us, but there are two important details to take from this output:
* The ``addr`` is the **MAC/BSSID** of our device. **BSSID** stands for Basic Service Set Identifier, and it's a **unique identifier** for a **wireless device** or **access point's** physical address.
* The ``type`` is shown as **managed**. This is the standard mode for most devices. In managed mode, the device acts as a client, connecting to an access point to join a network. 
Now we would like to scan for nearby Wi-Fi networks using out ``wlan2`` device. We can use `sudo iw dev wlan2 scan`. Where the `dev wlan2` specifies the wireless device you want to work with,  and `scan` tells **iw** to scan the area for available networks.
![[Pasted image 20241214172453.png]]
There is **a lot** of info here. The most important details here that indicate this device is an access point:
- The **BSSID** and **SSID** of the device are `02:00:00:00:00:00` and `MalwareM_AP` respectively. Since the **SSID** is shown, this means the device is advertising a network name, which access points do to allow clients to discover and connect to the network.

- The presence of **RSN (Robust Security Network)** indicates the network is using **WPA2**, as **RSN** is a part of the **WPA2** standard. **WPA2** networks typically use **RSN** to define the encryption and authentication settings.

- The `Group and Pairwise ciphers` are **CCMP**. Counter Mode with Cipher Block Chaining Message Authentication Code Protocol (CCMP) is the encryption method used by **WPA2**.

- The `Authentication suites` value inside **RSN** is **PSK** indicating that this is a **WPA2-Personal** network, where **a shared password** is used for authentication.

- Another important detail is the `DS Parameter set` value, which shows **channel 6**. The channel, in terms of Wi-Fi, refers to a specific frequency range within the broader Wi-Fi spectrum that allows wireless devices to communicate with each other. There are various Wi-Fi channels, and they all help distribute network traffic across various frequency ranges, which reduces interference. The two most common Wi-Fi channels are 2.4 GHz and 5GHz. In the 2.4 GHz band, channels 1, 6, and 11 are commonly used because they don’t overlap, minimising interference. In the 5 GHz band, there are many more channels available, allowing more networks to coexist without interference.

Now will be a good time to discuss another type that we can use on **some** wireless devices: **monitor** mode. This is a special mode primarily used for **network analysis** and **security auditing**. In this mode, the **Wi-Fi interface** listens to all wireless traffic on a specific channel, regardless of whether it is directed to the device or not. It passively captures all network traffic within range for analysis without joining a network. 

We want to check if our `wlan2` interface can use monitor mode. To achieve this, we will run the commands:
1.  `sudo ip link set dev wlan2 down` to turn our device off. 
2.  `sudo iw dev wlan2 set type monitor` to change wlan2 to monitor mode.
3. `sudo ip link set dev wlan2 up` to turn our device back on .
![[Pasted image 20241214173344.png]]
We can confirm that our interface is in **monitor** mode with the command:
`sudo iw dev wlan2 info`
![[Pasted image 20241214173726.png]]
Now let us create one more SSH session. We want to have **2 separate terminals** in order to see how the attack works:
![[Pasted image 20241214173500.png]]
On the first terminal, we start by capturing Wi-Fi traffic in the area, specifically targeting the **WPA** handshake packets. We can do this with the command `sudo airodump-ng wlan2`. 
This command provides **a list of nearby Wi-Fi networks (SSIDs)** and shows important details like **signal strength, channel, and encryption type**. 

> **Note:** By default, `airodump-ng` will automatically switch the selected wireless interface into **monitor** mode if the interface supports it.

![[Pasted image 20241214173937.png]]
The output reveals the information we already knew before, such as the **BSSID**, **SSID**, and the **channel**. However, in this particular output, we are also given the channel where our target **SSID** is listening (**channel 6**). Now, we will focus on the **MalwareM_AP** access point and capture the **WPA** handshake; this is crucial for the **PSK** (password) cracking process.

First, in the current terminal, let us cancel **airodump-ng** using `CTRL+C` and then execute the command `sudo airodump-ng -c 6 --bssid 02:00:00:00:00:00 -w output-file wlan2`.
This command targets the **specific network channel** and **MAC address (BSSID)** of the access point for which you want to capture the traffic and saves the information to a few files that start with the name output-file.
These files will be used to crack the **PSK**. The ultimate goal of this command is to capture the **4-way handshake**. 
It will first check for any clients that may be connected to the access point. If a client is already connected, then we can perform a de-authentication attack; otherwise, for any new client that connects, we will capture the 4-way handshake.
In **this** particular scenario, a client is already connected. The output will look the same at first until we receive the information about the connected client, which will be displayed at the bottom of our output. It is important to leave this **command running** until we are done with the attack.
![[Pasted image 20241214174330.png]]
It should take around **1-5 minutes** before receiving client information.
![[Pasted image 20241214174358.png]]
Note that the `STATION` section shows the device's **BSSID** (**MAC**) of `02:00:00:00:01:00` that is connected to the access point. This is the connection that we will be attacking.

On the **second terminal**, we will launch the de-authentication attack. Because the client is already connected, we want to force them to reconnect to the access point, forcing it to send the handshake packets. We can break this down into 3 simple steps:
1. **De-authentication packets:** The tool **aireplay-ng** sends **de-authentication** packets to either a specific client (**targeted attack**) or to all clients connected to an access point (**broadcast attack**). These packets are essentially "disconnect" commands that force the client to drop its current Wi-Fi connection.
2. **Forcing a reconnection:** When the client is disconnected, it automatically tries to reconnect to the Wi-Fi network. During this reconnection, the client and access point perform the **4-way handshake** as part of the reauthentication process.
3. **Capturing the handshake:** This is where **airodump-ng** comes into play because it will capture this handshake as it happens, providing the data needed to attempt the **WPA/WPA2** cracking.
   
We can do the de-auth with the command:
`sudo aireplay-ng -0 1 -a 02:00:00:00:00:00 -c 02:00:00:00:01:00 wlan2`
Where:
1. The `-0` flag indicates that we are using the de-authentication attack, and the `1` value is the number of **de-auths** to send.
2. The `-a` indicates the BSSID of the **access point**.
3. `-c` indicates the BSSID of the **client** to **de-authenticate**.
![[Pasted image 20241214174834.png]]
Now, if we look back on our first terminal, we will see the **WPA** handshake shown on the top-right of our output as `WPA handshake: 02:00:00:00:00:00`:
![[Pasted image 20241214175008.png]]
In the second terminal, we can use the captured **WPA** handshake to attempt to crack the **WPA/WP2** passphrase. 

We will be performing a **dictionary attack** in order to match the passphrase against each entry in a specified wordlist file. A shortened version of the infamous `rockyou.txt` wordlist has already been provided for us to use. This is located in the `/home/glitch/` directory. 

If the passphrase is **weak** and appears in the **wordlist**, it will eventually be cracked. The command: `sudo aircrack-ng -a 2 -b 02:00:00:00:00:00 -w /home/glitch/rockyou.txt output*cap` will do this for us where the:
1. `-a 2` flag indicates the **WPA/WPA2** attack mode. 
   2.  `-b` indicates the **BSSID of the access point**.
    3. the `-w` flag indicates the **dictionary list** to use for the attack. 
     4.  `output*cap`, which contain the 4-way handshake that we will be cracking.
![[Pasted image 20241214175353.png]]
> **Note:** If you get an `Packets contained no EAPOL data; unable to process this AP` error, this means that you ran **aircrack-ng** prior to the handshake being captured or that the handshake was not captured at all. If that's the case, then re-do all of the steps in order to capture the `WPA handshake`.

![[Pasted image 20241214175530.png]]
With the **PSK**, we can now join the **MalwareM_AP** access point. In a typical engagement, we would do this to inspect the new network, or in some cases, joining the access point is enough to show impact. 
First, press `CTRL+C` on the terminal that has `airodump-ng` running in order to stop the **airodump-ng** process. We do this because we will not be able to join the Wi-Fi network due to the fact that we are using the interface in monitor mode. Then execute the following commands:
1. `wpa_passphrase MalwareM_AP 'fluffy/champ24' > config`
2. `sudo wpa_supplicant -B -c config -i wlan2`
![[Pasted image 20241214175631.png]]
> **Note:** If you get a `rfkill: Cannot get wiphy information` error, you can ignore it. You will also notice that `wpa_supplicant` has automatically switched our **wlan2** interface to **managed mode**.

Giving it about **10 seconds** and checking the wireless interfaces once again with `iw dev` shows that we have joined the **MalwareM_AP** SSID.
![[Pasted image 20241214175901.png]]

Wi-Fi hacking note done;