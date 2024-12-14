# File Upload Attacks
## Pre info
If the user input and uploaded files are not correctly filtered and validated, attackers may be able to exploit the file upload feature to perform malicious activities, like executing arbitrary commands on the **back-end** server to take control over it.

File upload vulnerabilities are amongst the **most common** vulnerabilities found in **web** and **mobile** applications. We will also notice that most of these vulnerabilities are scored as **High** or **Critical** vulnerabilities, showing the level of risk caused by insecure file upload.
## Types of File Upload Attacks
The most common reason behind file upload vulnerabilities is **weak file validation** and **verification**, which may not be well secured to prevent unwanted file types or could be missing altogether. The **worst** possible kind of file upload vulnerability is an **unauthenticated arbitrary file upload** vulnerability. With this type of vulnerability, a web app allows any unauthenticated user to upload any file type, making it one step away from allowing any user to execute code on the back-end server.

Many web developers employ various types of tests to validate the extension or content of the uploaded file. However, as we will see, if these filters are not secure, we may be able to **bypass** them and still reach arbitrary file uploads to perform our attacks.

The most common and **critical** attack caused by arbitrary file uploads is **gaining remote command execution** over the back-end server by uploading a **web shell** or uploading a script that sends a **reverse shell**. A web shell allows us to execute **any** command we specify and can be turned into an **interactive shell** to enumerate the system easily and further exploit the network. It may also be possible to upload a script that sends a **reverse shell** to a listener on our machine and then interact with the remote server that way.

In some cases, we may not have arbitrary file uploads and may only be able to upload a specific file type. Even in these cases, there are various attacks we may be able to perform to exploit the file upload functionality if certain security protections were missing from the web application.

Examples of these attacks include:
![[Attachments (images)/BydrrwD4yl.png]]

Finally, a **file upload vulnerability** is not only caused by **writing insecure functions** but is also often caused by the use of **outdated libraries** that may be vulnerable to these attacks.
## Exploitation
### Absent Validation
The most basic type of file upload vulnerability occurs when the web app **does not have any form of validation filters** on the uploaded files, allowing the upload of any file type by default.

With these types of vulnerable web apps, we may directly upload our **web shell** or **reverse shell** script to the web app, and then by just visiting the uploaded script, we can interact with our **web shell** or send the **reverse shell**.

Below we will see an **Employee File Manager** web app, which allows us to upload **personal files** to the web app: 
![[Attachments (images)/H1RWIDDEyg.png]]

The web app does not mention anything about what file types are allowed, and we can drag and drop any file we want, and its name will appear on the upload form, including **.php** files: 
![[Attachments (images)/rJWV8DPNyg.png]]

### Identifying Web Framework
 A **web shell** has to be written in the **same** programming language that runs the **web server**, as it runs platform-specific functions and commands to execute system commands on the back-end server, making web shells non-cross-platform scripts. So, the **first step** would be to identify what language runs the web app.
  
One easy method to determine what language runs the web app is to visit the **/index.ext** page, where we would swap out **ext** with various common web extensions, like **php**, **asp**, **aspx**, among others, to see whether any of them exist.
 
Several other techniques may help identify the technologies running the web app, like using the **Wappalyzer** extension. Once added to our browser, we can click its icon to view all technologies running the web application: 
![[Attachments (images)/Sk_6DDwNJe.png]]

Not only did the extension tell us that the web application runs on **PHP**, but it also identified the **type** and **version** of the **web server**, the **back-end** operating system, and **other technologies** in use.

### Vulnerability Identification
Now that we have identified the **web framework** running the web app and its **programming language**, we can test whether we can upload a file with the same extension. As an initial test, we can upload arbitrary **PHP** files, let's create a basic **Hello World** script to test whether we can execute **PHP** code with our uploaded file.
To do so, we will write ```<?php echo "Hello HTB";?>``` to **test.php**, and try uploading it to the web application:
![[Attachments (images)/HkyYdwDNJe.png]]
we get a message saying File successfully uploaded, which means that the web application has **no** file validation whatsoever on the **back-end**. Now, we can click the **Download** button, and the web app will take us to our uploaded file: 
![[Attachments (images)/B1zh_PwVke.png]]

## Upload Exploitation
The final step in exploiting this web app is to upload the **malicious** script in the same language as the web app. Once we upload our **malicious** script and visit its link, we should be able to interact with it to take control over the back-end server.
### Web Shells
We can find many excellent **web shells** online that provide useful features, like directory traversal or file transfer. One good option for **PHP** is **phpbash**, which provides a terminal-like, semi-interactive **web shell**. Furthermore, SecLists provides a plethora of web shells for different frameworks and languages, which can be found in the **/opt/useful/seclists/Web-Shells** directory in a **PwnBox** (HTB).

We can download any of these web shells for the **language** of our web app, then upload it, and visit the uploaded file to interact with the **web shell**. For example, let's try to upload **phpbash.php** from **phpbash** to our web app:
![[Attachments (images)/rkg5bqPVJg.png]]
This web shell provides a **terminal**-like experience, which makes it very easy to **enumerate** the **back-end** server for further exploitation.

### Writing Custom Web Shell

Although using **web shells** from online resources can provide a great experience, we should also know **how** to write a **simple web shell** manually. This is because we may **not** have access to online tools during some penetration tests.

For example, with **PHP** web apps, we can use the **system()** function that **executes** system commands and **prints** their output, and pass it the **cmd** parameter with ```$_REQUEST['cmd']```, as follows:
![[Attachments (images)/SJP-MqDNkg.png]]
If we write the above script to **shell.php** and upload it to our web app, we can execute **system** commands with the **?cmd=**, a **GET** parameter (e.g. **?cmd=id**), as follows: 
![[Attachments (images)/HkHsM9wVyl.png]]

Web shells are **not** exclusive to **PHP**, and the same applies to other web frameworks, with the only difference being the **functions** used to execute **system** commands. For **.NET** web apps, we can pass the **cmd** parameter with request(```'cmd'```) to the **eval()** function, and it should also execute the command specified in **?cmd=** and print its output, as follows:
![[Sk6b79vV1l.png]]

It **must** be noted that **in certain cases, web shells may not work**. This may be due to the web server **preventing** the use of some functions utilized by the web shell (e.g. **system()**), or due to a Web App Firewall, among other reasons. In these cases, we may need to use advanced techniques to **bypass** these security mitigations.

### Reverse Shell
Finally, let's see how we can receive **reverse shells** through the vulnerable upload functionality. We should start by downloading a **reverse shell** script in the language of the web app. One reliable **reverse shell** for **PHP** is the ([pentestmonkey PHP reverse shell](https://github.com/pentestmonkey/php-reverse-shell)). 
[SecLists](https://github.com/danielmiessler/SecLists/tree/master/Web-Shells) also contains various **reverse shell** scripts, and we can utilize any of them to receive a reverse shell as well.

Let's download pentestmonkey's reverse shell and then open it in a text editor to input our **IP** and **listening PORT**, which the script will connect to:
![[r1ag45DNJg.png]]

Next, we can start a **netcat** listener on our machine (with the above **port**), upload our script to the web app, and then visit its link to get a reverse shell connection:
![[B1E749v4ke.png]]

We **successfully** received a connection back from the **back-end** server that hosts the vulnerable web app. The same concept can be used for other web frameworks and languages, with the only **difference** being the reverse shell script we use.

### Generating Custom Reverse Shell Scripts
Just like web shells, we can also create our own reverse shell scripts. While it is possible to use the same previous **system** function and pass it a **reverse shell** command, this may not always be very reliable, as the command may fail for many reasons.

This is why it is always better to use core web framework functions to connect to our machine. However, this may not be as easy to memorize as a web shell script. Luckily, tools like **msfvenom** can **generate** a **reverse shell** script in many languages and may even attempt to **bypass** certain restrictions in place. We can do so as follows for **PHP**:
![[ByLhVcD4kg.png]]

Once our **reverse.php** script is generated, we once again start a **netcat** listener on the port we specified above, upload the **reverse.php** script and visit its link, and we should receive a reverse shell as well:
![[HJBkSqvEkl.png]]

Reverse shells may **not** always work, and we may have to rely on web shells instead. This can be for **several** reasons, like having a **firewall** on the **back-end** network that **prevents** outgoing connections or if the web server **disables** the necessary functions to initiate a connection back to us.

## Bypassing Filters
#### Client-Side Validation
Many web apps only rely on **front-end JavaScript** code to validate the selected file format before it is uploaded and would **not** upload it if the file is **not** in the required format (e.g., **not** an image).

However, as the file format validation is happening on the **client-side**, we can easily bypass it by directly interacting with the server, skipping the front-end validations altogether. We may also **modify** the **front-end** code through our browser's **dev tools** to disable any validation in place.

The exercise has a basic **Profile Image** functionality, **frequently** seen in web apps that utilize user profile features, like social media web apps: 
![[Hyxf99wEJl.png]]

However, this time, when we get the file selection dialog, we cannot see our **PHP** scripts, as the dialog appears to be **limited** to image formats only: 
![[S15Xc9PNJx.png]]

We may still select the **All Files** option to select our **PHP** script anyway, but when we do so, we get an error message saying ("Only images are allowed!"), and the Upload button gets disabled: 
![[rJ5w95P4kx.png]]

This indicates some form of **file type validation**, so we cannot **just** upload a web shell through the upload form as we did previously. Luckily, all validation appears to be happening on the **front-end**, as the page never refreshes or sends any **HTTP** requests after selecting our file. So, we should be able to have complete control over these **client-side** validations.

Any code that runs on the **client-side** is under our control. While the web server is responsible for **sending** the **front-end** code, the **rendering** and **execution** of the **front-end** code happen within our browser. If the web app does **not** apply any of these validations on the **back-end**, we should be able to upload any file type.

To bypass these protections, we can either **modify the upload request to the back-end server**, or we can **manipulate the front-end code to disable these type validations**.

### Back-end Request Modification
Let's start by examining a normal request through **Burp**. When we select an image, we see that it gets **reflected** as our profile image, and when we click on **Upload**, our profile image gets updated and **persists** through refreshes. This indicates that our image was uploaded to the **server**, which is now displaying it back to us: 
![[BkVIi9wEJg.png]]

If we capture the upload request with **Burp**, we see the following request being sent by the web app: 
![[H11OsqD4Je.png]]

The web app appears to be sending a standard **HTTP upload** request to **/upload.php**. We can now **modify** this request to meet our **needs** without having the front-end type validation restrictions. If the **back-end server** does not validate the uploaded file type, then we should theoretically be able to send **any** file type/content, and it would be uploaded to the server.

The **two** important parts in the request are **filename="HTB.png"** and the **file content** at the end of the request. If we modify the filename to **shell.php** and modify the content to the **web shell** we used in the previous section; we would be uploading a **PHP** web shell instead of an image.

So, let's capture another image upload request, and then modify it accordingly: 
![[ryW12qPNke.png]]
> **Note:** We may also modify the **Content-Type** of the uploaded file, though this should not play an important role at this stage, so we don't modify it.

As we can see, our upload request went through, and we got **File successfully uploaded** in the response. So, we may now visit our uploaded file and interact with it and gain RCE.

### Disabling Front-end Validation
Another method to bypass client-side validations is through **manipulating the front-end code**. As these functions are being completely processed within our web browser, we have complete control over them. So, we can **modify** these scripts or **disable** them entirely. Then, we may use the upload functionality to upload any file type without needing to utilize **Burp** to capture and modify our requests.

To start, we can click **[CTRL+SHIFT+C]** to toggle the browser's **Page Inspector**, and then click on the **profile image**, which is where we trigger the file selector for the upload form: 
![[SyC9pqPVJg.png]]

This will highlight the following **HTML** file input on line 18:
```<input type="file" name="uploadFile" id="uploadFile" onchange="checkFile(this)" accept=".jpg,.jpeg,.png">```

Here, we see that the file input specifies (**.jpg,.jpeg,.png**) as the **allowed** file types within the file selection dialog. However, we can easily modify this and select **All Files** as we did before, so it is unnecessary to change this part of the page.

The more interesting part is **onchange="checkFile(this)"**, which runs a **JavaScript** code whenever we select a file, which does **file type validation**. To get the details of this function, we can go to the browser's Console by clicking **[CTRL+SHIFT+K]**, and then we can type the function's name (**checkFile**) to get its details:
![[BJ_rEm_Eyx.png]]

The key thing we take from this function is where it checks whether the file extension is an image, and if it is not, it prints the error message we saw earlier (**"Only images are allowed!"**) and disables the **Upload** button. We can add **PHP** as one of the allowed extensions or modify the function to remove the extension check.

Luckily, we do not need to get into writing and modifying **JavaScript** code. We can **remove** this function from the **HTML** code since its primary use appears to be file type validation, and removing it should not break anything.

To do so, we can go back to our inspector, click on the profile image again, double-click on the function name (**checkFile**) on **line 18**, and delete it:
![[ryUHvX_Nkx.png]]

With the **checkFile** function removed, we should be able to select our **PHP web shell** through the file selection dialog and upload it normally with no validations.

Once we upload our **web shell** and then refresh the page, we can open Page Inspector, click on the profile image, and we should see the URL of our uploaded web shell:
```<img src="/profile_images/shell.php" class="profile-image" id="profile-image">```

### Blacklist Filters
Previously we saw an example of a web app that only applied type validation controls on the **front-end** (i.e., client-side), which made it trivial to bypass these controls.

If the type validation controls on the **back-end** server were **not** securely coded, an attacker can utilize multiple techniques to **bypass** them and reach **PHP** file uploads.

The example below is similar to the one we saw previously, but it has a blacklist of **disallowed extensions** to prevent uploading web scripts. We will see why using a blacklist of common extensions may **not** be enough.

### Blacklisting Extensions
Let's start with the client-side bypasses we learned in the previous section to upload a **PHP** script to the **back-end** server. We'll **intercept** an image upload request with Burp, replace the file content and filename with our **PHP** script's, and forward the request: 
![[HyA79VuNyx.png]]

Our attack did not succeed, as we got "Extension not allowed". This indicates that the web application may have some form of **file type validation** on the **back-end**, in addition to the front-end validations.

There are generally **two** common forms of validating a file extension on the back-end:
![[H1CP9EdV1e.png]]

The validation may check the **file type** or the **file content** for type matching. The **weakest** form of validation amongst these is testing the file extension against a **blacklist** of extension. For example, the following code checks if the uploaded file extension is **PHP** and drops the request if it is:
![[HyExiNONkg.png]]

The code is taking the file extension (```$extension```) from the uploaded file name (```$fileName```) and then **comparing** it against a **list** of blacklisted extensions (```$blacklist```). However, this validation method has a **major flaw**. It is **not comprehensive**, as many other extensions are not included in this list, which may still be used to execute **PHP** code on the back-end server if uploaded.
![[HyZKo4d4Jl.png]]

### Fuzzing Extensions
The web app seems to be testing the file extension, our first step is to fuzz the upload functionality with a list of **potential** extensions and see which of them return the previous error message. Any upload requests that do not return an error message, return a different message, or succeed in uploading the file, may indicate an allowed file extension.

[PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Upload%20Insecure%20Files/Extension%20PHP/extensions.lst) provides lists of extensions for **PHP** and **.NET** web apps. Also [SecLists](https://github.com/danielmiessler/SecLists/blob/master/Discovery/Web-Content/web-extensions.txt) has a list of common Web Extensions.

As we are testing a **PHP** application, we will use the above PHP list. Then, from Burp we can intercept the upload request and send it to Intruder. From the Positions tab select the **.php** extension in **filename="HTB.php"** and click the **Add** button to add it as a fuzzing position:
![[ByJ42E_Nkg.png]]

We'll keep the file content for this attack, as we are only interested in fuzzing file extensions. Finally, we can Load the PHP extensions list from above in the **Payloads** tab under **Payload Options**. We will also **un-tick** the **URL Encoding** option to avoid encoding the (.) before the file extension:
![[B1Zp2EuNJl.png]]

We can sort the results by **Length**, and we will see that all requests with the **Content-Length** (**193**) passed the extension validation, as they all responded with "File successfully uploaded", and the rest responded with "Extension not allowed".

### Non-Blacklisted Extensions
We can try uploading a file using any of the **allowed extensions** from above, and some of them may allow us to execute **PHP** code.

Let's use the **.phtml** extension, which **PHP** web servers often allow for **code execution** rights. Now we have to change the file name to use the **.phtml** extension and changing the content to that of a **PHP web shell**:
![[HkLua4_Vkg.png]]

As we can see, our file seems to have indeed been uploaded:
![[rJaFaE_NJx.png]]


### Whitelist Filters
The other type of **file extension validation** is by utilizing a whitelist of allowed file extensions. The web server would only allow the specified extensions, and the list would not need to be comprehensive in covering uncommon extensions.

Still, there are different use cases for a blacklist and for a whitelist. A blacklist may be helpful in cases where the upload functionality needs to allow a wide variety of file types (e.g., **File Manager**), while a whitelist is usually only used with upload functionalities where only a few file types are allowed. Both may also be used in tandem.

Let's start the exercise at the end of this section and attempt to upload an uncommon **PHP** extension, like **.phtml**, and see if we are still able to upload it as we did in the previous section: 
![[SJoThL_4Je.png]]

We get a message saying "Only images are allowed", which may be more common in web apps than seeing a blocked extension type. However, error messages do not always reflect which form of validation is being utilized, so let's try to fuzz for allowed extensions as we did in the previous section, using the same **wordlist** that we used previously: 
![[BJyrpUdV1x.png]]

We can see that all variations of **PHP** extensions are blocked (e.g.**php5, php7, phtml**). However, the wordlist we used also contained other 'malicious' extensions that were not blocked and were successfully uploaded. So, let's try to understand how we were able to upload these extensions and in which cases we may be able to utilize them to execute **PHP** code on the **back-end** server.

The following is an example of a file extension whitelist test:
![[ryaVCI_Eke.png]]

We see that the script uses a Regular Expression (**regex**) to test whether the filename contains any whitelisted image extensions. The **issue** here lies within the **regex**, as it only checks whether the file name contains the extension and not if it actually ends with it. Many developers make such mistakes due to a **weak understanding** of **regex** patterns.

So, let's see in Double Extensions chapter, how we can bypass these tests to upload **PHP** scripts.

### Double Extensions
The code only tests whether the file name **contains** an image extension; a straightforward method of passing the regex test is through Double Extensions. For example, if the **.jpg** extension was allowed, we can add it in our uploaded file name and still end our filename with **.php** (e.g. **shell.jpg.php**), in which case we should be able to **pass** the whitelist test, while still uploading a **PHP** script that can execute **PHP** code.

Let's intercept a request and modify the file name to (**shell.jpg.php**), and modify its content to that of a web shell: 
![[B1GPZYdV1x.png]]

Now, if we visit the uploaded file and try to send a command, we can see that it does indeed successfully execute system commands: 
![[B1gy_WKu4yx.png]]

However, this may **not** always work, as some web apps may use a **strict regex pattern**, as mentioned earlier, like the following:
![[HJBcZYuNyl.png]]

This pattern only considers the final file extension, as it uses (**^.*\.**) to match everything up to the last (**.**), and then uses (```$```) at the end to only match extensions that end the file name. So, the **above attack would not work**. Nevertheless, some exploitation techniques **may** allow us to bypass this pattern, but most rely on misconfigurations or outdated systems.

### Reverse Double Extension
In some cases, the **file upload functionality** itself may not be vulnerable, but the **web server configuration** may lead to a vulnerability. For example, an organization may use an open-source web app, which has a file upload functionality. Even if the file upload functionality uses a **strict regex** pattern that only matches **the final extension** in the file name, the organization may use the insecure configurations for the web server.

For example, the **/etc/apache2/mods-enabled/php7.4.conf** for the **Apache2 web server** may include the following configuration:
![[rJH8MK_41g.png]]
The above configuration is how the **web server** determines which files to allow **PHP code execution**. It specifies a whitelist with a **regex** pattern that matches **.phar**, **.php**, and **.phtml**. However, this **regex** pattern can have the same mistake we saw earlier if we forget to end it with (```$```). In such cases, any file that contains the above extensions will be allowed **PHP code execution**, even if it does not end with the **PHP** extension. For example, the file name (**shell.php.jpg**) should pass the earlier whitelist test as it ends with (**.jpg**), and it would be able to execute **PHP** code due to the above misconfiguration, as it contains (**.php**) in its name.

Let's try to intercept a normal image upload request, and use the above file name to pass the strict whitelist test: 
![[SkMTMYuVJg.png]]

Now, we can visit the uploaded file, and attempt to execute a command: 
![[BJipMKuVJe.png]]

### Character Injection
We can inject several characters before or after the **final extension** to cause the web app to **misinterpret** the filename and execute the uploaded file as a **PHP** script.
The following are some of the characters we may try injecting:
![[S1BbQtu41l.png]]

Each character has a specific use case that may trick the web application to misinterpret the file extension. For example, (**shell.php%00.jpg**) works with **PHP servers with version 5.X or earlier**, as it causes the **PHP** web server to end the file name after the (**%00**), and store it as (**shell.php**), while still passing the whitelist. The same may be used with web apps hosted on a **Windows** server by injecting a colon (**:**) before the allowed file extension (e.g. **shell.aspx:.jpg**), which should also write the file as (**shell.aspx**). Similarly, each of the other characters has a use case that may allow us to upload a **PHP** script while bypassing the type validation test.

We can write a small **bash** script that generates all permutations of the file name, where the above characters would be injected before and after both the **PHP** and **JPG** extensions, as follows:
``` bash
for char in '%20' '%0a' '%00' '%0d0a' '/' '.\\' '.' '…' ':'; do
    for ext in '.php' '.phps'; do
        echo "shell$char$ext.jpg" >> wordlist.txt
        echo "shell$ext$char.jpg" >> wordlist.txt
        echo "shell.jpg$char$ext" >> wordlist.txt
        echo "shell.jpg$ext$char" >> wordlist.txt
    done
done
```

With this custom wordlist, we can run a **fuzzing** scan with Burp Intruder, similar to the ones we did earlier. If either the back-end or the web server is **outdated** or has certain **misconfigurations**, some of the generated filenames may bypass the whitelist test and execute **PHP** code.

### Type Filters
So far, we have only been dealing with **type filters** that only consider the **file extension** in the file name. However, as we saw in the previous section, we may still be able to gain control over the back-end server even with image extensions (e.g. **shell.php.jpg**). Furthermore, we may utilize some allowed extensions (e.g., **SVG**) to perform **other attacks**.

This is why many modern web servers and web apps also test the **content** of the uploaded file to ensure it matches the specified type. While **extension filters** may accept several extensions, content filters usually specify a single category (e.g., **images, videos, documents**), which is why they do not typically use blacklists or whitelists. This is because web servers provide functions to check for the **file content type**, and it usually falls under a specific category.

There are **two** common methods for validating the file content: **Content-Type Header** or **File Content**. Let's see how we can identify each filter and how to bypass both of them.

#### Content-Type
Let's start and attempt to upload a PHP script:
![[SypLQqdNkg.png]]

We see that we get a message saying "**Only images are allowed**". The error message persists, and our file fails to upload even with the tricks we learned previously. If we change the file name to **shell.jpg.phtml** or **shell.php.jpg**, or even if we use **shell.jpg** with a **web shell content**, our upload will fail. As the file extension does **not** affect the error message, the web app must be testing the file content for type validation. As mentioned earlier, this can be either in the **Content-Type Header** or the **File Content**.

The following is an example of how a **PHP** web app tests the **Content-Type** header to validate the file type:
``` php
$type = $_FILES['uploadFile']['type'];

if (!in_array($type, array('image/jpg', 'image/jpeg', 'image/png', 'image/gif'))) {
    echo "Only images are allowed";
    die();
}

```

The code sets the (```$type```) variable from the uploaded file's **Content-Type** header. Our browsers automatically set the **Content-Type** header when selecting a file through the file selector dialog, **usually derived from the file extension**. However, since our browsers set this, this operation is a client-side operation, and we can manipulate it to change the perceived file type and **potentially** bypass the type filter.

We may start by fuzzing the Content-Type header with SecLists' ([Content-Type Wordlist](https://github.com/danielmiessler/SecLists/blob/master/Discovery/Web-Content/web-all-content-types.txt)) through **Burp Intruder**, to see which types are allowed. However, the message tells us that only **images are allowed**, so we can **limit** our scan to **image types**, which reduces the wordlist to **45** types only (compared to around **700** originally). We can do so as follows:
![[Pasted image 20241214232952.png]]

For the sake of simplicity, let's just pick an image type (e.g. **image/jpg**), then intercept our upload request and change the **Content-Type** header to it: 
![[rJq3B5dEke.png]]

This time we get "**File successfully uploaded**", and if we visit our file, we see that it was successfully uploaded: 
![[HyGAH5O4yg.png]]
![[H1iCHc_Eye.png]]

#### MIME-Type
The second and more common type of **file content validation** is testing the uploaded file's **MIME-Type**. **Multipurpose Internet Mail Extensions (MIME)** is an internet standard that determines the type of a file through its **general format** and **bytes structure**.

This is usually done by inspecting the **first few bytes** of the file's content, which contain the **File Signature** or **Magic Bytes**. For example, if a file starts with (**GIF87a** or **GIF89a**), this indicates that it is a **GIF** image, while a file starting with **plaintext** is usually considered a **Text file**. If we change the first bytes of any file to the **GIF magic bytes**, its **MIME** type would be changed to a **GIF** image, regardless of its remaining content or extension.
![[HJe-qc_4yg.png]]

Let's take a basic example to demonstrate this. The **file** command on **Unix** systems finds the file type through the **MIME** type. If we create a basic file with text in it, it would be considered as a text file, as follows:
![[BkqGccdN1x.png]]

As we see, the file's **MIME** type is **ASCII** text, even though its extension is **.jpg**. However, if we write **GIF8** to the beginning of the file, it will be considered as a **GIF** image instead, even though its extension is still **.jpg**:
![[r1XH55uN1g.png]]

**Web servers** can also utilize this standard to determine file types, which is usually more accurate than testing the **file extension**. The following example shows how a **PHP** web app can test the **MIME** type of an uploaded file:
```php
$type = mime_content_type($_FILES['uploadFile']['tmp_name']);

if (!in_array($type, array('image/jpg', 'image/jpeg', 'image/png', 'image/gif'))) {
    echo "Only images are allowed";
    die();
}
```
As we can see, the **MIME** types are similar to the ones found in the **Content-Type** headers, but their **source** is different, as **PHP** uses the ```mime_content_type()``` function to get a file's **MIME** type. Let's try to repeat our last attack, but now with an exercise that tests both the **Content-Type** header and the **MIME** type:
![[ByVWacONJe.png]]

Once we forward our request, we notice that we get the error message "**Only images are allowed**". Now, let's try to add **GIF8** before our **PHP** code to try to imitate a **GIF** image while keeping our file extension as **.php**, so it would execute **PHP** code regardless:
![[BkFFa5_NJe.png]]

We can now visit our uploaded file, and we will see that we can successfully execute system commands: 
![[SJnqacd4ye.png]]

We can use a combination of the two methods discussed in this section, which **may help us bypass some more robust content filters**. For example, we can try using an **Allowed MIME type** with a **disallowed Content-Type**, an **Allowed MIME/Content-Type with a disallowed extension**, or a **Disallowed MIME/Content-Type with an allowed extension**, and so on. Similarly, we can attempt other combinations and permutations to try to confuse the web server, and depending on the level of code security, we may be able to bypass various filters.

###  Upload Attacks
#### Limited File Uploads
So far, we have been mainly dealing with filter bypasses to obtain arbitrary file uploads through a vulnerable web application, which is the main focus of this module at this level. While file upload forms with **weak filters** can be exploited to upload arbitrary files, some upload forms have **secure filters** that may **not** be exploitable with the techniques we discussed. However, even if we are dealing with a limited (i.e., **non-arbitrary**) file upload form, which only allows us to upload **specific file types**, we may still be able to perform **some** attacks on the web app.

Certain file types, like **SVG, HTML, XML, and even some image and document files**, may allow us to introduce new vulnerabilities to the web application by uploading **malicious** versions of these files. This is why **fuzzing allowed file extensions is an important exercise for any file upload attack**. It enables us to explore **what** attacks may be achievable on the web server. So, let's explore some of these attacks.
##### XSS
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


##### XXE
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
### Other Notable Attacks 
There are a few other techniques and attacks worth mentioning, as they may become handy in some web penetration tests or bug bounty tests.
##### Injections in File Name
A common file upload attack uses a malicious string for the uploaded file name, which may get executed or processed if the uploaded file name is displayed (i.e., **reflected**) on the page. We can try **injecting a command** in the file name, and if the web app uses the file name within an **OS command**, it may lead to a **command injection attack**.

For example, if we name a file ```file$(whoami).jpg``` or file```whoami.jpg``` or ```file.jpg||whoami```, and then the web app attempts to **move** the uploaded file with an **OS command** (e.g. ```mv file /tmp```), then our file name would inject the ```whoami``` command, which would get executed, leading to RCE.

Similarly, we may use an **XSS** payload in the file name (e.g. ```<script>alert(window.origin);</script>)```, which would get executed on the target's machine if the file name is displayed to them. We may also inject an **SQL** query in the file name (e.g. ```file';select+sleep(5);--.jpg```), which may lead to an **SQL injection** if the file name is insecurely used in an **SQL query**.

##### Upload Directory Disclosure
In some file upload forms, like a **feedback form** or a **submission form**, we may not have access to the link of our uploaded file and may not know the uploads directory. In such cases, we may utilize **fuzzing** to look for the uploads directory or even use other vulnerabilities (e.g., **LFI/XXE**) to find where the uploaded files are by reading the web app's source code. 

Another method we can use is through **forcing error messages**, as they often reveal helpful information for further exploitation. One attack we can use to cause such errors is **uploading a file with a name that already exists** or **sending two identical requests simultaneously**. This may lead the web server to show an error that it could not write the file, which may disclose the uploads directory. We may also try uploading a **file with an overly long name** (e.g., **5,000 characters**). If the web app doesn't handle this correctly, it may also error out and disclose the upload directory.

##### Advanced File Upload Attacks
Any automatic processing that occurs to an uploaded file, like **encoding a video**, **compressing a file**, or **renaming a file**, may be exploited if not securely coded.

Some commonly used libraries may have public exploits for such vulnerabilities, like the **AVI upload vulnerability** leading to **XXE** in **ffmpeg**.