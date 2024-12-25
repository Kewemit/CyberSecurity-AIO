- The basics of the MQTT protocol ✅
- How to use Wireshark to analyse MQTT traffic ✅
- Reverse engineering a simple network protocol ✅
# Is It Smart to be Smart
While smart devices make our lives easier, most of them need a **network connection** to provide control. Many smart devices are connected over the Internet, hence the term ``Internet of Things`` or ``IoT``, which means that anyone can potentially take control of these devices. To prevent that, we can limit the exposure of these devices by adding security controls such as **network isolation** and **authentication mechanisms**. 

# The Language of IoT
Although different ``IoT`` and smart devices use various programming languages, depending on the platform and vendor, they often need to speak the same language to be able to communicate with each other. 

For example, while ``IoT`` devices might use ``C++`` or ``Java`` to talk to the **compiler** and the **underlying hardware**, they still need a language like ``HTTP`` or ``MQTT`` to talk with your system or mobile device.

# How to Speak MQTT
``MQTT`` stands for ``Message Queuing Telemetry Transport``. It is a language very commonly used in ``IoT`` devices for communication purposes. 

``MQTT`` works on a **publish/subscribe model**, where any **client** device can **publish messages**, and **other client** devices can **subscribe** to the messages if they are related to a topic of interest. An ``MQTT broker`` connects the different clients, publishing and subscribing to messages.
![[Pasted image 20241225010701.png]]
To further understand ``MQTT``, let’s explore some key concepts used in ``MQTT protocols``:
1. **MQTT Clients:** ``MQTT clients`` are ``IoT`` devices, such as **sensors** and **controllers**, that publish or subscribe to messages using the ``MQTT`` protocol. **For example**: a **temperature sensor** can be a client that **publishes** temperature sensors at different places. An **HVAC controller** can also act as a client that **subscribes to messages** from the **temperature sensor** and turns the HVAC system **on*' or **off** based on the input received.

3. **MQTT Broker:** An ``MQTT broker`` receives messages from **publishing clients** and **distributes** them to the **subscribing clients** based on their preferences.

4. **MQTT Topics:** Topics are used to classify the different types of messages. Clients can subscribe to messages based on their topics of interest. **For example**: a **temperature sensor** sending temperature readings can use the topic of “``room temperature``”, while an HVAC controller would **subscribe** to messages under the topic of “``room temperature``”. However, a **light sensor** can publish messages with the topic “``light readings``”. An HVAC controller does not need to subscribe to this topic. On the other hand, a light controller would subscribe to “``light readings``” but not to the topic of “``room temperature``”.
#  Demonstration
Let’s see how it works in practice. The files related to this task are in the `~/Desktop/MQTTSIM/` directory. The walkthrough and the challenge files are shown in the terminal below:
```bash
ubuntu@tryhackme:~/Desktop/MQTTSIM$ tree
.
├── challenge
|   ├── challenge.pcapng
|   ├── challenge.sh
|   ├── lights.py
└── walkthrough
    ├── hvac.py
    ├── walkthrough.sh
```

As we discussed, different ``IoT`` devices communicate with each other using ``MQTT``, a network protocol. Therefore, we must **monitor** network communication to see what is going on. We will use ``Wireshark`` to monitor network communication. 

After starting ``wireshark``, on the home screen we can select ‘``any``’ for the **network interface** to see traffic from all the network interfaces:
![[Pasted image 20241225011501.png]]
The screenshot below shows the traffic from all the interfaces on the network as we have selected '``any``' for the **network interface**:
![[Pasted image 20241225011557.png]]

Since we only want to see traffic from the ``MQTT`` protocol, we can filter for this protocol only. To do that, we can type `mqtt` in the filter box and press enter:
![[Pasted image 20241225011625.png]]

There will be **no traffic for now** as no ``MQTT broker`` or ``client`` is running. Let’s start the **MQTT broker and client** to see the traffic. 

For that, there is a shortcut for a directory on the Desktop named `Link to MQTT`. The directory has different scripts in it. We will start the `walkthrough.sh` script to learn how the ``MQTT protocol`` works.

Let’s run the `walkthrough.sh` script:
```bash
ubuntu@tryhackme:~$ cd Desktop/MQTTSIM/walkthrough/  

ubuntu@tryhackme:~/Desktop/MQTTSIM/walkthrough$ ./walkthrough.sh  
Starting MQTT broker in Docker... 
Starting the HVAC controller script... 
All processes started. 

ubuntu@tryhackme:~/MQTTSIM$
```
Once we run it, **three** windows will pop up. 
1. The window with the red text is the ``MQTT broker``.
2. The window with the blue text is the ``MQTT client``.
3. The third window is the ``application UI`` we might see as the end user.
We can look at the logs on each of these windows if we like, but as an end user, we will only interact with the ``application UI``:
![[Pasted image 20241225012204.png]]
Currently, the application is in **automatic mode**, the **heater is off**, the **target temperature is 22** degrees, and the **current temperature is 22.3** degrees. 

Let’s head over to ``Wireshark`` to see what kind of communication we can see. The below screenshot shows how the communication started:
![[Pasted image 20241225012319.png]]
The first two events show the **connection establishment**, showing the `Connect Command` and `Connect Ack` text in the info. The next two events show that one of the clients wants to subscribe to the `home/temperature` topic, shown in the square brackets. This must be the **HVAC** controller, which needs to know the temperature to turn the heater **on** or **off**. 

Below, we can see the messages published by different clients, with their topics in the square brackets. The events from the `home/temperature` topic are from the temperature sensor, and the events from the `home/heater` are published by the **HVAC** controller. The screenshot below shows the details of the `home/temperature` topic message.
![[Pasted image 20241225012538.png]]
In the lower pane, we can see that it is publishing a temperature of ``-9.6877475608946``. That is very cold. Therefore, we see a message from the heater right after the temperature is broadcast.

This message shows that the heater was turned on, as seen in the highlighted part in the lower pane:
![[Pasted image 20241225012707.png]]

The illustration below shows the overall communication between the heater controller and the temperature sensor, as we have deduced from ``Wireshark`` communication:
![[Pasted image 20241225012826.png]]
# Challenge
Great! Now we understand how the ``MQTT publish/subscribe`` model works with a broker. In this challenge, the lights have gone off in all the major factories and halls. Mayor Malware has sabotaged the lighting system implemented by a contractor company. 

To see what Mayor Malware has done, we can run the script `challenge.sh` as shown below. This will open three windows, including the lights controller interface:
```bash
ubuntu@tryhackme:~/Desktop/MQTTSIM/walkthrough$ cd ~/Desktop/MQTTSIM/challenge/ ubuntu@tryhackme:~/Desktop/MQTTSIM/challenge$ ./challenge.sh 
[...]
```

We can see the lights controller interface; however, nothing works, and there is no way to turn the lights back on:
![[Pasted image 20241225013122.png]]
Now that we have basic knowledge of the ``MQTT`` protocol, we must find the **command to turn the lights back on**. The `challenge.pcapng` file is inside the `challenge` directory. This packet capture file contains various ``MQTT protocol messages`` related to turning the lights “on” and “off.” 

Start ``Wireshark`` then go to the **File** menu, choose **Open**, locate the `challenge.pcapng` file, and open it in Wireshark to take a closer look.

You plan to publish a message to the ``MQTT broker`` in order to turn on the lights. As a result, all the lighting devices subscribed to this ``MQTT broker`` will turn their lights on.

You can achieve this from the command line using the `mosquitto_pub` command. Consider the following example:
```bash
$ mosquitto_pub -h localhost -t "some_topic" -m "message"
```
We are publishing a message under a specific topic to a broker.
- `mosquitto_pub` is the command-line utility to publish an MQTT message
- `-h localhost` refers to the MQTT broker, which is `localhost` in this task
- `-t "some_topic"` specifies the **topic**
- `-m "message"` sets the **message**, such as `"on"` and `"off"`

To determine the correct topic and message to turn on the lights, you must study the captured packets in `challenge.pcapng`. Once you figure it out, ensure you are running `challenge.sh` and use `mosquitto_pub` to publish a message to turn on the lights. Once you successfully manage to turn on the lights, a flag will appear on the lights controller interface.

We get the flag with the command:
```bash
$ mosquitto_pub -h localhost -t "d2FyZXZpbGxl/Y2hyaXN0bWFzbGlnaHRz" -m "on"
```