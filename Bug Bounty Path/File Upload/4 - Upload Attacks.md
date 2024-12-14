## Limited File Uploads
So far, we have been mainly dealing with filter bypasses to obtain arbitrary file uploads through a vulnerable web application, which is the main focus of this module at this level. While file upload forms with **weak filters** can be exploited to upload arbitrary files, some upload forms have **secure filters** that may **not** be exploitable with the techniques we discussed. However, even if we are dealing with a limited (i.e., **non-arbitrary**) file upload form, which only allows us to upload **specific file types**, we may still be able to perform **some** attacks on the web app.

Certain file types, like **SVG, HTML, XML, and even some image and document files**, may allow us to introduce new vulnerabilities to the web application by uploading **malicious** versions of these files. This is why **fuzzing allowed file extensions is an important exercise for any file upload attack**. It enables us to explore **what** attacks may be achievable on the web server. So, let's explore some of these attacks.
### XSS
Many file types may allow us to introduce a **Stored XSS** vulnerability to the web application by uploading **maliciously crafted** versions of them.

The most basic example is when a web app **allows us to upload HTML files**. Although **HTML** files won't allow us to execute code (e.g., **PHP**), it would still be possible to implement **JavaScript** code within them to carry an **XSS** or **CSRF** attack on whoever visits the uploaded **HTML** page. If the target sees a **link** from a website they trust, and the website is vulnerable to uploading **HTML documents**, it may be possible to trick them into visiting the link and carry the attack on their machines.

Another example of **XSS** attacks is web apps that display an **image's metadata after its upload**. For such web apps, we can include an **XSS** payload in one of the **Metadata** parameters that accept raw text, like the **Comment** or **Artist parameters**, as follows:
![[S1OYFiONJl.png]]
We can see that the **Comment** parameter was updated to our **XSS** payload. When the image's **metadata** is displayed, the **XSS** payload should be triggered, and the **JavaScript** code will be executed to carry the **XSS** attack. Furthermore, if we change the image's **MIME**-Type to **text/html**, some web apps may show it as an **HTML** document instead of an **image**, in which case the **XSS** payload would be triggered even if the **metadata** wasn't directly displayed.

Finally, **XSS** attacks can also be carried with **SVG** images, along with several other attacks. **Scalable Vector Graphics (SVG)** images are **XML**-based, and **they describe 2D vector graphics**, which the **browser renders into an image**. For this reason, we can modify their **XML** data to include an **XSS** payload. For example, we can write the following to **HTB.svg**:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE svg PUBLIC "-//W3C//DTD SVG 1.1//EN" "http://www.w3.org/Graphics/SVG/1.1/DTD/svg11.dtd">
<svg xmlns="http://www.w3.org/2000/svg" version="1.1" width="1" height="1">
    <rect x="1" y="1" width="1" height="1" fill="green" stroke="black" />
    <script type="text/javascript">alert(window.origin);</script>
</svg>

```
Once we upload the image to the web app, the **XSS** payload will be triggered whenever the image is displayed.


### XXE
Similar attacks can be carried to lead to **XXE** exploitation. With **SVG** images, we can also include malicious **XML** data to leak the source code of the web application, and other **internal documents** within the server. The following example can be used for an **SVG** image that leaks the content of (**/etc/passwd**): 
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE svg [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]>
<svg>&xxe;</svg>
```
Once the above **SVG** image is uploaded and viewed, the **XML** document would get processed, and we should get the info of (**/etc/passwd**) printed on the page or shown in the **page source**. Similarly, if the web app allows the upload of **XML** documents, then the same payload can carry the same attack when the **XML** data is displayed on the web application.

While reading systems files like **/etc/passwd** can be very useful for server enumeration, it can have an even more significant benefit for web pentesting, as it allows us to read the web app's source. Access to the **source code** will enable us to find more vulnerabilities to exploit within the web app through **Whitebox Pentesting**. For File Upload exploitation, it may allow us to locate the upload directory, identify allowed extensions, or find the file naming scheme, which may become handy for further exploitation.

To use **XXE** to read source code in **PHP** web applications, we can use the following payload in our **SVG** image:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE svg [ <!ENTITY xxe SYSTEM "php://filter/convert.base64-encode/resource=index.php"> ]>
<svg>&xxe;</svg>
```
Once the **SVG** image is displayed, we should get the base64 encoded content of index.php, which we can decode to read the source code.

Using **XML** data is **not** unique to **SVG** images, as it is also utilized by many types of documents, like **PDF, Word Documents, PowerPoint Documents, among many others**. All of these documents include **XML** data within them to specify their format and structure. Suppose a web app used a **document viewer** that is vulnerable to **XXE** and allowed uploading any of these documents. In that case, we may also modify their **XML** data to include the **malicious XXE elements**, and we would be able to carry a **blind XXE attack** on the back-end web server.

Another similar attack that is also achievable through these file types is an **SSRF** attack. We may utilize the **XXE** vulnerability to enumerate the internally available services or even call private APIs to perform private actions.
## Other Notable Attacks 
There are a few other techniques and attacks worth mentioning, as they may become handy in some web penetration tests or bug bounty tests.
### Injections in File Name
A common file upload attack uses a malicious string for the uploaded file name, which may get executed or processed if the uploaded file name is displayed (i.e., **reflected**) on the page. We can try **injecting a command** in the file name, and if the web app uses the file name within an **OS command**, it may lead to a **command injection attack**.

For example, if we name a file ```file$(whoami).jpg``` or file```whoami.jpg``` or ```file.jpg||whoami```, and then the web app attempts to **move** the uploaded file with an **OS command** (e.g. ```mv file /tmp```), then our file name would inject the ```whoami``` command, which would get executed, leading to RCE.

Similarly, we may use an **XSS** payload in the file name (e.g. ```<script>alert(window.origin);</script>)```, which would get executed on the target's machine if the file name is displayed to them. We may also inject an **SQL** query in the file name (e.g. ```file';select+sleep(5);--.jpg```), which may lead to an **SQL injection** if the file name is insecurely used in an **SQL query**.

### Upload Directory Disclosure
In some file upload forms, like a **feedback form** or a **submission form**, we may not have access to the link of our uploaded file and may not know the uploads directory. In such cases, we may utilize **fuzzing** to look for the uploads directory or even use other vulnerabilities (e.g., **LFI/XXE**) to find where the uploaded files are by reading the web app's source code. 

Another method we can use is through **forcing error messages**, as they often reveal helpful information for further exploitation. One attack we can use to cause such errors is **uploading a file with a name that already exists** or **sending two identical requests simultaneously**. This may lead the web server to show an error that it could not write the file, which may disclose the uploads directory. We may also try uploading a **file with an overly long name** (e.g., **5,000 characters**). If the web app doesn't handle this correctly, it may also error out and disclose the upload directory.

### Advanced File Upload Attacks
Any automatic processing that occurs to an uploaded file, like **encoding a video**, **compressing a file**, or **renaming a file**, may be exploited if not securely coded.

Some commonly used libraries may have public exploits for such vulnerabilities, like the **AVI upload vulnerability** leading to **XXE** in **ffmpeg**.