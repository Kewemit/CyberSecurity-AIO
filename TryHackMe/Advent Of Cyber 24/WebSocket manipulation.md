- Learn about WebSocket's and their vulnerabilities. ✅
- Learn how WebSocket Message Manipulation can be done.✅

## TLDR:
What are WebSocket's
* WebSocket's let your browser and the server keep a constant line of communication open.
* WebSocket's are used mostly for **live chat apps**, **real-time games** or any **live data feed where you need constant updates

HTTP vs WebSocket
* **HTTP:** Requires repeated requests for updates (polling), which is resource-intensive.
* **WebSocket:** Keeps the connection open for immediate updates, reducing resource usage and improving efficiency.

WebSocket Vulnerabilities
1. **Weak Authentication & Authorization:** causes possible access to sensitive data.
2. **Message Tampering:** Messages can be intercepted and altered if not encrypted.
3. **Cross-Site WebSocket Hijacking (CSWSH):** Attacker tricks a victims browser into connecting to a malicious WebSocket server.
4. **Denial of Service (DoS):** Open connections are vulnerable to flooding attacks.

WebSocket Message Manipulation:**
- **What it is:** Attackers intercept and alter WebSocket messages in real-time.
- **Potential Consequences:**
    - **Unauthorised Actions:** Impersonating users, making purchases, or transferring funds.
    - **Privilege Escalation:** Gaining admin rights or access to restricted data.
* Without good security checks, message tampering can lead to anything from unauthorised actions to the downing of an entire service.
## Exploitation Example:
Navigating to a vulnerable site: http://10.10.220.230 (Just an example, wont open):
![[Pasted image 20241214195923.png]]
Make sure to proxy the traffic of the application with burpsuite & FoxyProxy:
![[Pasted image 20241214200134.png]]
Enable the proxy intercept:
![[645b19f5d5848d004ab9c9e2-1731078617275.png]]
Go back to your browser and click the Track button and then Burp Proxy will intercept the WebSocket traffic:
![[645b19f5d5848d004ab9c9e2-1731078744669.png]]
Change the value of the **userId** parameter from **5** to **8** and click the **Forward** button:
![[645b19f5d5848d004ab9c9e2-1731078745018.png]]
This results in a successful modification.
## Introduction to WebSocket
WebSocket's let your browser and the server keep a constant line of communication open.

Unlike the old-school method of asking for something, getting a response, and then hanging up, WebSocket's are like keeping the phone line open so you can chat whenever you need to. Once that connection is set up, the client and server can talk back and forth without all the extra requests.

WebSocket's are great for **live chat apps**, **real-time games**, or any **live data feed** where you want constant updates. After a quick handshake to get things started, both sides can send messages whenever. This means less overhead and faster communication when you need data flowing in real-time.
#  Traditional HTTP Requests vs. WebSocket
When you use regular **HTTP**, your browser sends a request to the server, and the server responds, then closes the connection. If you need new data, you have to make another request. 
**Think of it like knocking on someone's door every time you want something—they'll answer, but it can get tiring if you need updates constantly.**

Take a **chat app** as an example. With **HTTP**, your browser would keep asking, "Any new messages?" every few seconds. This method, known as polling, works but isn’t efficient. Both the browser and the server end up doing a lot of unnecessary work just to stay updated.

WebSocket's handle things differently. Once the connection is established, it remains open, allowing the server to push updates to you whenever there’s something new. It’s more like leaving the door open so updates can come in immediately without the constant back-and-forth. This approach is faster and uses fewer resources.

# WebSocket Vulnerabilities
Since WebSocket connections stay open and active, they can be taken advantage of if the proper security measures aren't in place. Here are some common vulnerabilities:

- **Weak Authentication and Authorisation:** Unlike regular **HTTP**, WebSocket's don't have built-in ways to handle **user authentication** or **session validation**. If you don't set these controls up properly, attackers could slip in and get access to sensitive data or mess with the connection.

- **Message Tampering:** WebSocket's let data flow back and forth constantly, which means attackers could intercept and change messages if encryption isn't used. This could allow them to inject harmful commands, perform actions they shouldn't, or mess with the sent data.

- **Cross-Site WebSocket Hijacking (CSWSH):** This happens when an attacker tricks a user's browser into opening a WebSocket connection to another site. If successful, the attacker might be able to hijack that connection or access data meant for the legitimate server.

- **Denial of Service (DoS):** Because WebSocket connections stay open, they can be targeted by DoS attacks. An attacker could flood the server with a ton of messages, potentially slowing it down or crashing it altogether.
## What Is WebSocket Message Manipulation?

WebSocket Message Manipulation is when an attacker intercepts and changes the messages sent between a web app and its server. Unlike regular HTTP requests that go back and forth one at a time, WebSocket's maintain an open connection for continuous two-way communication, which is ideal for real-time apps but also vulnerable without proper security.

In this type of attack, a hacker could intercept and tweak these WebSocket messages as they're being sent. Let's say the app is sending sensitive info, like transaction details or user commands—an attacker could change those messages to make the app behave differently. They could **bypass security checks, send unauthorised requests, or alter key data like usernames, payment amounts, or access levels.**

For example, imagine a web app using WebSocket's to handle **money transfers** between accounts. If an attacker gets hold of the message before it hits the server, they could change the **amount** being transferred or even send the money to a **different account**. Since WebSocket connections happen in real-time, these changes would take effect instantly without the user or server noticing immediately.

This kind of manipulation can also lead to more significant problems. Hackers could **inject harmful code** or **try to get higher-level access.** For instance, they might change a message to give themselves admin rights or insert malicious commands to take control of the server.

What makes this attack so dangerous is that WebSocket connections often don't have the same security protections as traditional **HTTP** connections, like **End-to-End Encryption**, which encrypts the request body of an **HTTP** request using **JavaScript** using an **AES key or RSA public key** stored in the **JavaScript** file. If developers don't add vigorous checks like message validation or encryption, it's easy for attackers to exploit these gaps. By tampering with the data being sent, attackers can cause all sorts of damage, from unauthorised actions to full system compromises.

The impact of changing WebSocket messages depends on how the app uses them and what kind of data is being sent. Here's a breakdown of what can happen:

- **Doing Things Without Permission:** If someone can tamper with WebSocket messages, they could impersonate another user and carry out unauthorised actions such as making purchases, transferring funds, or changing account settings.
  
- **Gaining Extra Privileges:** Attackers could also manipulate messages to make the system think they have more privileges than they actually do. This could let them access admin controls, change user data, view sensitive info, or mess with system settings.
  
- **Messing Up Data:** One of the significant risks is data corruption. If someone is changing the messages, they could feed bad data into the system. They could change things in real-time and disrupt everyone's work in circumstances such as a shared document or tool.
  
- **Crashing the System:** An attacker could also spam the server with bad requests, causing it to slow down or crash. If this happens enough, the system could go offline, causing serious downtime for users and businesses.

