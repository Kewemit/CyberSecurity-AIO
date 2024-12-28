
- Self-signed certificates ✅
- Man-in-the-middle attacks ✅
- Using Burp Suite proxy to intercept traffic ✅

# TLDR
**What is a Certificate?**
Essentially a certificate is a digital stamp of approval which consists of:
- **Public Key:** Used to **encrypt** data, shared openly.
- **Private Key:** Kept secret by the server, used to **decrypt** data.
- **Metadata:** Includes details like the Certificate Authority (CA), website info, unique ID, validity period, and signature.

**What is a Certificate Authority (CA)?**
- A **trusted entity** that issues and verifies certificates (e.g., GlobalSign, Let’s Encrypt).
- Ensures **authentication, encryption**, and **data integrity** for secure communication, forming the backbone of **HTTPS**.

 **Self-Signed vs. Trusted CA Certificates:**
* **Trusted CA Certificates:** Verified by a trusted **CA**, ensuring the website’s authenticity and security.
- **Self-Signed Certificates:** Created and signed by the same entity. Generally **not trusted by browsers** due to lack of third-party verification, posing security risks.

# Exploitation example
## Preperations
To prevent any DNS logs from alerting his enemies, he will resolve the Gift Scheduler’s FQDN locally on his machine.
To achieve this, let’s add the following line to the `/etc/hosts` file:
`10.10.34.0 gift-scheduler.thm`

We can use the following command:
![[Pasted image 20241214203105.png]]
Or alternatively add the line `10.10.34.0 gift-scheduler.thm` manually to ``/etc/hosts`

Now, Mayor Malware can navigate to the **Gift Scheduler** website without leaving a trace on Wareville’s DNS logs.

Let’s open the Firefox browser and navigate to `https://gift-scheduler.thm`. We’ll be presented with the following warning page:
![[5f04259cf9bf5b57aed2c476-1732081797042.png]]
We can click on the `Advanced` button to expand the warning’s details.
![[5f04259cf9bf5b57aed2c476-1732081843216.png]]
When we click on the `View Certificate` link marked with a **1** in the screenshot above, a new tab opens with the certificate details. And we see that the Gift Scheduler web server uses a self-signed certificate.
This means that the townspeople and all the elves will be used to clicking on the `Accept the Risk and Continue` button (marked with **2** on the screenshot above) to access the website, to the point it’s become a habit.

Now we press the same button and login, then get presented with this:
![[5f04259cf9bf5b57aed2c476-1732081963676.png]]
We will need to sniff some **admin credentials**. Maybe some of the elves’ passwords. Or even—if we gets lucky—**Marta May Ware’s** account!

To sniff the elves’ traffic, the next step will be to start a **proxy** on our machine and route all of **Wareville’s traffic** to it. This way, we will be running a **Man In The Middle** (**MITM**) attack. This position will allow us to sniff all requests forwarded to the website.

Let’s start by opening burpsuite:
![[5f04259cf9bf5b57aed2c476-1732082529688.png]]
Once **Burp Suite** loads, we will select `Proxy` (1 in the screenshot) and then **toggle off** the `Intercept on` option (2) to prevent users from noticing any **delays** in the website responses. Finally, let’s open the `Proxy Settings` (3) to set a new listener on our Kali Linux IP address:
![[5f04259cf9bf5b57aed2c476-1732082054712.png]]
We can click on the `Add` button highlighted in the screenshot above. Burp Suite will prompt us for the new listener’s configuration:
![[5f04259cf9bf5b57aed2c476-1732082457072.png]]
We must set the listening port to `8080` and toggle the `Specific address` option. The box next to it will automatically specify the IP address of our Kali Linux, `CONNECTION_IP`. Finally, we can click on `OK` to apply the configuration.

The previous settings window will get displayed and we can see that the new listener has been added under the proxy listeners list:
![[5f04259cf9bf5b57aed2c476-1732081911457.png]]
> NOTE: Burp Suite already comes with a **self-signed certificate**. The users will be **prompted** to **accept** it and continue. 

## **Man In The Middle Attack**
Now that our machine is ready to listen, we must **reroute** all Wareville traffic to **our machine**.

Mayor Malware has a wonderful idea to achieve this: he will set his own machine as a gateway for all other Wareville’s machines!

Let’s add another line to the `/etc/hosts` file.
> **Note:** The `CONNECTION_IP` address in the snippet should reflect the IP of our machine, which can be found at the top of the page.
![[Pasted image 20241214204424.png]]

This will divert all of Wareville’s traffic, usually routed through the legitimate Wareville Gateway, to the attacker's machine, effectively putting him “**In The Middle**” of the requests. 
> **Note:** In practice, the adversary can launch a similar attack if they can control the user’s gateway and their attack can easily succeed against websites not using properly signed certificates. This attack requires more than adding an entry into the `/etc/hosts` file; however, this task aims to emulate parts of the attack.

As a last step, we must start a custom script to simulate the users’ requests to the **Gift Scheduler**. 
> **Note:** Keep the script running so that new user requests will constantly be captured in Burp Suite.
![[Pasted image 20241214204655.png]]

**PWN the Scheduler**
We can return to the open **Burp Suite** window and click on the `HTTP History` tab:
![[5f04259cf9bf5b57aed2c476-1732085268640.png]]
Now we can see POST requests containing clear-text credentials.
# What is a certificate
- **Public key**: At its core, a certificate contains a **public key**, part of a pair of cryptographic keys: a public key and a private key. The public key is made available to anyone and is used to encrypt data.
- **Private key**: The private key remains secret and is used by the website or server to decrypt the data.
- **Metadata**: Along with the key, it includes metadata that provides additional information about the certificate holder (the website) and the certificate. You usually find information about the Certificate Authority (CA), subject (information about the website, e.g. www.meow.thm), a uniquely identifiable number, validity period, signature, and hashing algorithm.
# What is a Certificate Authority (CA)
A **CA** is a trusted entity that **issues certificates**; for example, **GlobalSign**, **Let’s Encrypt**, and **DigiCert** are very common ones. The browser trusts these entities and performs a series of checks to ensure it is a **trusted CA**. Here is a breakdown of what happens with a certificate:
- **Handshake**: Your browser requests a secure connection, and the website responds by sending a certificate, but in this case, it only requires the public key and metadata.
  
- **Verification:** Your browser checks the certificate for its validity by checking if it was issued by a trusted CA. If the certificate hasn’t expired or been tampered with, and the CA is trusted, then the browser gives the green light.
  
- **Key exchange**: The browser uses the public key to encrypt a session key, which encrypts all communications between the browser and the website.
  
- **Decryption**: The website (server) uses its private key to decrypt the session key, which is [symmetric](https://deviceauthority.com/symmetric-encryption-vs-asymmetric-encryption/). Now that both the browser and the website share a secret key (**session key**), we have established a secure and encrypted communication!
Ever wonder what makes **HTTPS** be **S** (**secure**)? Thanks to certificates, we can now have authentication, encryption, and data integrity.
### Self-Signed Certificates vs. Trusted CA Certificates
The process of acquiring a certificate with a **CA** is long
1. you create the certificate
2. send it to a CA to sign it for you.
3. If you don’t have tools and automation in place, this process can take weeks. 

Self-signed certificates are signed by an entity usually the same one that authenticates. For example, **Wareville** owns the **GiftScheduler** site, and if they create a certificate and sign it with **Wareville** as a **CA**, that becomes a **self-signed certificate**.
- **Browsers** generally do **not** trust **self-signed certificates** because there is no third-party verification. The browser has no way of knowing if the certificate is authentic or if it’s being used for malicious purposes (like a **man-in-the-middle attack**).
- **Trusted CA certificates**, on the other hand, are verified by a **CA**, which acts as a trusted third party to confirm the website’s identity.
