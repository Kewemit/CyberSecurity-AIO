# Bypassing Filters
## Client-Side Validation
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

## Back-end Request Modification
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

## Disabling Front-end Validation
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

## Blacklist Filters
Previously we saw an example of a web app that only applied type validation controls on the **front-end** (i.e., client-side), which made it trivial to bypass these controls.

If the type validation controls on the **back-end** server were **not** securely coded, an attacker can utilize multiple techniques to **bypass** them and reach **PHP** file uploads.

The example below is similar to the one we saw previously, but it has a blacklist of **disallowed extensions** to prevent uploading web scripts. We will see why using a blacklist of common extensions may **not** be enough.

## Blacklisting Extensions
Let's start with the client-side bypasses we learned in the previous section to upload a **PHP** script to the **back-end** server. We'll **intercept** an image upload request with Burp, replace the file content and filename with our **PHP** script's, and forward the request: 
![[HyA79VuNyx.png]]

Our attack did not succeed, as we got "Extension not allowed". This indicates that the web application may have some form of **file type validation** on the **back-end**, in addition to the front-end validations.

There are generally **two** common forms of validating a file extension on the back-end:
![[H1CP9EdV1e.png]]

The validation may check the **file type** or the **file content** for type matching. The **weakest** form of validation amongst these is testing the file extension against a **blacklist** of extension. For example, the following code checks if the uploaded file extension is **PHP** and drops the request if it is:
![[HyExiNONkg.png]]

The code is taking the file extension (```$extension```) from the uploaded file name (```$fileName```) and then **comparing** it against a **list** of blacklisted extensions (```$blacklist```). However, this validation method has a **major flaw**. It is **not comprehensive**, as many other extensions are not included in this list, which may still be used to execute **PHP** code on the back-end server if uploaded.
![[HyZKo4d4Jl.png]]

## Fuzzing Extensions
The web app seems to be testing the file extension, our first step is to fuzz the upload functionality with a list of **potential** extensions and see which of them return the previous error message. Any upload requests that do not return an error message, return a different message, or succeed in uploading the file, may indicate an allowed file extension.

[PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Upload%20Insecure%20Files/Extension%20PHP/extensions.lst) provides lists of extensions for **PHP** and **.NET** web apps. Also [SecLists](https://github.com/danielmiessler/SecLists/blob/master/Discovery/Web-Content/web-extensions.txt) has a list of common Web Extensions.

As we are testing a **PHP** application, we will use the above PHP list. Then, from Burp we can intercept the upload request and send it to Intruder. From the Positions tab select the **.php** extension in **filename="HTB.php"** and click the **Add** button to add it as a fuzzing position:
![[ByJ42E_Nkg.png]]

We'll keep the file content for this attack, as we are only interested in fuzzing file extensions. Finally, we can Load the PHP extensions list from above in the **Payloads** tab under **Payload Options**. We will also **un-tick** the **URL Encoding** option to avoid encoding the (.) before the file extension:
![[B1Zp2EuNJl.png]]

We can sort the results by **Length**, and we will see that all requests with the **Content-Length** (**193**) passed the extension validation, as they all responded with "File successfully uploaded", and the rest responded with "Extension not allowed".

## Non-Blacklisted Extensions
We can try uploading a file using any of the **allowed extensions** from above, and some of them may allow us to execute **PHP** code.

Let's use the **.phtml** extension, which **PHP** web servers often allow for **code execution** rights. Now we have to change the file name to use the **.phtml** extension and changing the content to that of a **PHP web shell**:
![[HkLua4_Vkg.png]]

As we can see, our file seems to have indeed been uploaded:
![[rJaFaE_NJx.png]]


## Whitelist Filters
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

## Double Extensions
The code only tests whether the file name **contains** an image extension; a straightforward method of passing the regex test is through Double Extensions. For example, if the **.jpg** extension was allowed, we can add it in our uploaded file name and still end our filename with **.php** (e.g. **shell.jpg.php**), in which case we should be able to **pass** the whitelist test, while still uploading a **PHP** script that can execute **PHP** code.

Let's intercept a request and modify the file name to (**shell.jpg.php**), and modify its content to that of a web shell: 
![[B1GPZYdV1x.png]]

Now, if we visit the uploaded file and try to send a command, we can see that it does indeed successfully execute system commands: 
![[B1gy_WKu4yx.png]]

However, this may **not** always work, as some web apps may use a **strict regex pattern**, as mentioned earlier, like the following:
![[HJBcZYuNyl.png]]

This pattern only considers the final file extension, as it uses (**^.*\.**) to match everything up to the last (**.**), and then uses (```$```) at the end to only match extensions that end the file name. So, the **above attack would not work**. Nevertheless, some exploitation techniques **may** allow us to bypass this pattern, but most rely on misconfigurations or outdated systems.

## Reverse Double Extension
In some cases, the **file upload functionality** itself may not be vulnerable, but the **web server configuration** may lead to a vulnerability. For example, an organization may use an open-source web app, which has a file upload functionality. Even if the file upload functionality uses a **strict regex** pattern that only matches **the final extension** in the file name, the organization may use the insecure configurations for the web server.

For example, the **/etc/apache2/mods-enabled/php7.4.conf** for the **Apache2 web server** may include the following configuration:
![[rJH8MK_41g.png]]
The above configuration is how the **web server** determines which files to allow **PHP code execution**. It specifies a whitelist with a **regex** pattern that matches **.phar**, **.php**, and **.phtml**. However, this **regex** pattern can have the same mistake we saw earlier if we forget to end it with (```$```). In such cases, any file that contains the above extensions will be allowed **PHP code execution**, even if it does not end with the **PHP** extension. For example, the file name (**shell.php.jpg**) should pass the earlier whitelist test as it ends with (**.jpg**), and it would be able to execute **PHP** code due to the above misconfiguration, as it contains (**.php**) in its name.

Let's try to intercept a normal image upload request, and use the above file name to pass the strict whitelist test: 
![[SkMTMYuVJg.png]]

Now, we can visit the uploaded file, and attempt to execute a command: 
![[BJipMKuVJe.png]]

## Character Injection
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

#### Type Filters
So far, we have only been dealing with **type filters** that only consider the **file extension** in the file name. However, as we saw in the previous section, we may still be able to gain control over the back-end server even with image extensions (e.g. **shell.php.jpg**). Furthermore, we may utilize some allowed extensions (e.g., **SVG**) to perform **other attacks**.

This is why many modern web servers and web apps also test the **content** of the uploaded file to ensure it matches the specified type. While **extension filters** may accept several extensions, content filters usually specify a single category (e.g., **images, videos, documents**), which is why they do not typically use blacklists or whitelists. This is because web servers provide functions to check for the **file content type**, and it usually falls under a specific category.

There are **two** common methods for validating the file content: **Content-Type Header** or **File Content**. Let's see how we can identify each filter and how to bypass both of them.

##### Content-Type
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

##### MIME-Type
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
