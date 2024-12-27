# HTML Introduction
## What is HTML?
* HTML stands for Hyper Text Markup Language
* It's the standard markup for creating Web pages.
* HTML describes the structure of a Web page
* HTML Consists of a series of elements.
* HTML elements tell the browser how to display the content
* HTML elements label content such as "this is a heading", "this is a paragraph", "this is a link", etc
## A Simple HTML Document
```html
<!DOCTYPE html>
<html>
<head>
<title>Page Title</title>
</head>
<body>

<h1>"My first heading"</h1>
<p>"My First paragraph"</p>

</body>
</html>
```
Here's a breakdown of what each element does:
* `<!DOCTYPE html>`: declares that the document is an HTML5 document.
* `<html>`: This is the **root** element of the page.
* `<head>`: This element contains meta information about the HTML page.
* `<title>`: This element specifies the title for the page (show in the title bar).
* `<body>`: This element defines the documents' body, and is a container for **all visible content** (headings, paragraphs, images, hyperlinks, tables, lists.).
* `<h1>`: This element defines a large header.
* `<p>`: This element defines a paragraph.

This is what the site would look like currently:
![[Pasted image 20241227181132.png]]
## What is an HTML element?
An **HTML** element is defined by a **start tag**, **some content**, and an **end tag**:
```html
<tagname> Some Content Goes Here </tagname>
```
The **HTML** element is everything from the **start tag to the end tag**:
```html
<h1> My First Heading </h1>
<p> My First Paragraph. </p>
```

| Start Tag | Element Content     | End Tag | What It Does                          |
| --------- | ------------------- | ------- | ------------------------------------- |
| `<h1>`    | My First Heading    | `</h1>` | Defines a heading.                    |
| `<p>`     | My First Paragraph. | `</p>`  | Defines a paragraph.                  |
| `<br>`    | _none_              | _none_  | Break the line, so create a new line. |
>[!important] **Note** that some elements have no content (i.e. `<br>`). These elements are called **empty elements**. Empty elements **don't have end tags**)

## HTML Page Structure
Below we have a visualization of an HTML page structure:
![[Pasted image 20241227181745.png]]
>[!important] **Note** that only the content inside the `<body>` section will be displayed in a browser. The content inside the `<title>` element will be shown in the browser´s title bar, or the page's tab.

# HTML Basic Examples
Here we will go through some basic HTML examples. Don't worry if we use tags we haven't learned about yet.
## HTML Documents
* All HTML documents must start with a **document type declaration**: `<!DOCTYPE html>`.
* The HTML document itself begins with `<html>` and ends with `</html>`.
* The visible part of the HTML document is between `<body>` and `</body>`.
**Example:**
```html
<!DOCTYPE html>
<html>
<body>

<h1> My Heading </h1>
<p> My Paragraph </p>

</body>
</html>
```
## The `<!DOCTYPE>` Declaration
* The `<!DOCTYPE>` declares the **document type**, and helps the browser display the pages correctly.
* It must appear only once, and at the top (before any HTML tags).
* The `<!DOCTYPE>` is not case sensitive.
* The `<!DOCTYPE>` for HTML5 is: `<!DOCTYPE html>`.
## HTML Headings
* HTML headings are defined with the `<h1>` to `<h6>` tags.
* The `<h1>` defines the most important (largest) heading, and `<h6>` defines the least important heading.
**Example**:
```html
<h1>This is heading 1</h1>
<h2>This is heading 2</h2>
<h3>This is heading 3</h3>
```
![[Pasted image 20241227183248.png]]
## HTML Paragraphs
HTML Paragraphs are defined via the `<p>` tag:
```html
<p>This is a paragraph</p>
<p>This is another paragraph.</p>
```
## HTML Links
HTML Links are defined via the `<a>` tag:
```html
<a href="https://google.com">This is a link</a>
```
* The links' destination is specified in the `href` attribute.
* Attributes are used to provide additional information about HTML elements.
![[Pasted image 20241227183604.png]]
## HTML Images
HTML Images are defined with the `<img>` tag.
```html
<img src="photo.jpg" alt="MyPhoto" width="600" height="364">
```
Where:
* `src`: The source of the photo, usually stored in the same folder as the site.
* `alt`: The text displayed if the photo can't be loaded.
* `width`: The width of the photo.
* `height`: The height of the photo.

The source file (`src`), alternative text (`alt`), `width`, and `height` are provided as attributes.
![[Pasted image 20241227184119.png]]
## Empty HTML Elements
HTML elements with no content are called empty elements. The `<br>` tag defines a line break, and is an empty element without a closing tag.
```html
<p>This is a <br> paragraph with a line break</p>
```
## HTML is Not Case Sensitive
HTML tags are not case sensitive, so `<p>` is equally the same thing as `<P>`.
# HTML Attributes
HTML attributes provide additional information about HTML elements.
* All HTML elements can have **attributes**.
* Attributes provide additional information about elements.
* Attributes are always specified in **the start tag**.
* Attributes usually come in name/value pairs, like: name="value".
## The href Attribute
The `<a>` tag defines a hyperlink. The `href` attribute specifies the **URL** of the page that the link points to:
```html
<a href="https://google.com/">Visit Google</a>
```
>[!important] **Notice** how the `href` is inside the start tag.
More about links: [[HTML - AIO - TODO#HTML Links]]
## The src Attribute
The `<img>` tag is used to embed an image in an HTML page. The `src` attribute specifies the path to the image to be displayed:
```html
<img src="my_photo.png">
```
**Note**, that there are two ways to specify the URL in the `src` attribute:
* **Absolute URL**: Links to an external image, that is hosted on another website. **Example**: `src="https://mysite.com/images/my_photo.png"`. 
>[!important] **Note**, that you can't control external images, as they can suddenly be removed or changed.
* **Relative URL**: Links to an image that is hosted **within the website**. Here, the URL doesn't include the domain name. If the URL begins without a slash (`/`), it will be relative to the current page. **Example**: `src="myphoto.png"`. If the URL begin with a slash, it will be relative to the domain. **Example**: `src="/images/myphoto.png"`.
**Tip**: It's advised to almost always use relative URLs, as they don't break on domain change.
## The width And height Attributes.
The `<img>` tag should also contain the `width` and `height` attributes, which specify the width and height of the image (**in pixels**):
```html
<img src="myphoto.png" width="500" height="600">
```
## The alt Attribute
The **required** `alt` attribute for the `<img>` tag specifies the alternate text for an image, if the image for some reason cannot be displayed. This can be due to a slow connection, the user using a screen reader, or an error in the `src` attribute:
```html
<img src="myphoto.png" alt="My very cool photo">
```
## The style Attribute
The `style` attribute is used to **add styles to an element**, such as **colour, font, size**, and more.
```html
<p style="color:red;">This is a red Paragraph.</p>
```
## The lang Attribute
You should always include the `lang` attribute inside the `<html>` tag, to declare the language of the Web page. This is meant to assist search engines and browsers.

The following example specifies **English** as the language:
```html
<!DOCTYPE html>
<html lang="en">
<body>
...
</body>
</html>
```
Country codes (`en-US`) can also be added to the languge code in the `lang` attribute. So, the first two characters define the language of the HTML page, and the last two characters define the country.

The following example specifies English as the language and United States as the country:
```html
<!DOCTYPE html>
<html lang="en-US">
<body>
...
</body>
</html>
```
## The title Attribute
The `title` attribute defines some extra information about an element. The value of the `title` attribute will be displayed as a **tooltip** when the mouse if hovered over the element:
```html
<p title="I'm a tooltip">This is a paragraph.</p>
```
## Summary
- All HTML elements can have **attributes**.
- The `href` attribute of `<a>` specifies the **URL of the page the link goes to**.
- The `src` attribute of `<img>` specifies the **path to the image to be displayed**.
- The `width` and `height` attributes of `<img>` **provide size information for images**.
- The `alt` attribute of `<img>` **provides an alternate text for an image**.
- The `style` attribute is used to **add styles to an element, such as colour, font, size, and more**.
- The `lang` attribute of the `<html>` tag **declares the language of the Web page**.
- The `title` attribute **defines some extra information about an element**.

# HTML Headings
HTML headings are titles or subtitles that you want to display on a webpage. 
* HTML headings are defined with the `<h1>` to `<h6>` tags.
* `<h1>` defines the most important heading. `<h6>` defines the least important heading.
```html
<h1>Heading 1</h1>  
<h2>Heading 2</h2>  
<h3>Heading 3</h3>  
<h4>Heading 4</h4>  
<h5>Heading 5</h5>  
<h6>Heading 6</h6>
```
>[!important] **Note:** Browsers automatically add some space above and below a heading.
## Why Headings Are Important
* Search engines use headings to index the structure and content of the page.
* Users often skim a page by the headings. It's important to use them to show the document structure
* `<h1>` headings should be used for main headings, followed by `<h2>` headings, then the less important `<h3>`, and so on.
* Use HTML headings **only for headings**. Don't use them to make text **BIG** or **BOLD**
## Bigger Headings
Each HTML heading has a default size. However, we can specify the heading size with the `style` attribute, using the **CSS** `font-size` property:
```html
<h1 style="font-size:60px;"> Heading 1 </h1>
```
## HTML Display
* You can't be sure how HTML will be displayed.
* Large or small screens, and resized windows will create different results.
* With HTML, you can't change the display by adding extra spaces or extra lines in your HTML code.
* The browser will automatically remove any extra spaces and lines when the page is displayed.
```html
<p>  
This paragraph  
contains a lot of lines  
in the source code,  
but the browser  
ignores it.  
</p>  

<p>  
This paragraph  
contains         a lot of spaces  
in the source         code,  
but the        browser  
ignores it.  
</p>
```
# HTML Paragraphs
## HTML Horizontal Rules
The `<br>` tag defines a thematic break in an HTML page, and is most often displayed as a horizontal rule
The `<hr>` element is used to separate content (or define a change) in an HTML page:
```html
<h1>This is a heading 1</h1>
<p>This is some text</p>
<hr>
<h2>This is heading 2</h2>
<p>This is some other text.</p>
<hr>
```
**Note**, that the `<hr>` is an empty tag, so it has no end tag.
## HTML Line Breaks
The HTML `<br>` element defines a line break.

Use `<br>` if you want a line break (a new line) without starting a new paragraph:
```html
<p>This is<br>a paragraph<br>with line breaks</p>
```
**Note**, that the `<br>` is an empty tag, so it has no end tag.
## The Poem Problem
This poem will display on a single line:
```html
<p>  
  My Bonnie lies over the ocean.  
  
  My Bonnie lies over the sea.  
  
  My Bonnie lies over the ocean.  
  
  Oh, bring back my Bonnie to me.  
</p>
```
### Solution - The HTML `<pre>` Element.
The HTML `<pre>` element defines a preformatted text.

The text inside a `<pre>` element is displayed in **a fixed-width font** (usually **Courier**), and it preserves both spaces and line breaks.
# HTML Styles
https://www.w3schools.com/html/html_styles.asp