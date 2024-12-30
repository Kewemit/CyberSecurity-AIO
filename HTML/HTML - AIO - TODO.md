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
The HTML `style` attribute is used to add **styles to an element** (i.e. colour, font or size).
```html
<p style="color:red">This text is red</p>
<p style="color:blue">This text is blue</p>

<p style="font-size:30px">This text is big</p>

<p style="font-size:30px;color:red">This text is big and red</p>
```
## Style attribute
Setting the style can be done with the `style` attribute, and it has the following syntax
```html
<tagname style="property:value;">
```
Here, the _**property**_ is a **CSS** property. The _**value**_ is a **CSS** value.
## Background Color
The **CSS** `background-color` property defines the background colour for an HTML element.

Set the background for the **whole site**
```html
<body style="background-color:red;">
<h1>My Heading</h1>
<p>My paragraph</p>
</body>
```

Or alternatively set the background color for **two different elements**:
```html
<body>

<h1 style="background-color:red">This is my heading</h1>
<p style="background-color:powderblue">This is my paragraph</p>

</body>
```

## Text Color
The **CSS** ``color`` property defines the text color for an HTML element:
```html
<body>

<h1 style="color:green">This is my heading</h1>
<p style="color:brown">This is my paragraph</p>

</body>
```

## Fonts
The **CSS** `font-family` property defines the font to be used for an HTML element:
```html
<body>

<h1 style="font-family:verdana"> This Is My Heading</h1>
<p style="font-family:courier">This is my paragraph</p>

</body>
```

## Text Size
The **CSS** `font-size` property defines the text size for an HTML element:
```html
<h1 style="font-size:300%">This is my heading</h1>
<p style="font-size:180%">This is my paragraph</p>
```

## Text Alignment
The `text-align` property defines the horizontal text alignment for an HTML element:
```html
<h1 style="text-align:center">Centered Heading</h1>
<p style="text-align:right">This is my paragraph on the right</p>
```

## Summary
- Use the `style` for styling HTML elements
- Use `background-color` for background color
- Use `color` for text colours
- Use `font-family` for text fonts
- Use `font-size` for text sizes
- Use `text-align` for text alignment
# HTML Formatting
Formatting elements were designed to display special types of text:
- `<b>` - Bold text
- `<strong>` - Important text
- `<i>` - Italic text
- `<em>` - Emphasized text
- `<mark>` - Marked text
- `<small>` - Smaller text
- `<del>` - Deleted text
- `<ins>` - Inserted text
- `<sub>` - Subscript text
- `<sup>` - Superscript text
## The `<b>` and `<strong>` Elements
The `<b>` element defines bold text:
```html
<b>This text is bold</b>

<!-- Or -->
<p style="font-family:courier"><b>This is my paragraph</b></p>
```

The `<strong>` element defines text with strong importance. The content is usually just bold:
```html
<strong>This text is important!</strong>
```

## The `<i>` and `<em>` Elements
The `<i>` element defines text in italic. 
Info: (Often used to indicate a technical term, a phrase from another language, a thought, a ship name, etc).
```html
<i>My text is in italic</i>
```

The `<em>` element defines emphasized text. Typically italic:
```html
<em>My text is emphasized</em>
```

## The `<small>` Element
The `<small` element defines smaller text:
```html
<small>This text is smaller</small>
```
## The `<mark>` Element
The `<mark>` element  defines text that should be marked or highlighted:
```html
<p>Dont forget to buy <mark>Toilet Paper</mark> from the store</p>
```
## The `<del>` Element
The `<del>` element defines text that has been deleted. Browsers usually strike a line through deleted text:
```html
<p>My favorite color is <del>green</del> Purple</p>
```
## The `<ins>` Element
The `<ins>` element defines text that has been inserted. Browsers will underline this:
```html
<p>My favorite color is <del>purple</del> <ins>Green</ins>.</p>
```
## The `<sub>` Element
The `<sub>` element defines subscript text. Usually used for chemical formulas: H<sub>2</sub>O
```html
<p>This is <sub>Subscripted</sub> text</p>
```
## The `<sup>` Element
The `<sup>` element defines superscript text. Usually for footnotes: WWW<sup>[1]</sup>
```html
<p> This is <sup>superscripted</sup> text </p>
```
## Summary
| Tag          | Description                         |
| ------------ | ----------------------------------- |
| ``<b>``      | Defines **bold** text               |
| ``<em>``     | Defines **emphasized** text         |
| ``<i>``      | Defines **italic** text             |
| ``<small>``  | Defines **smaller** text            |
| ``<strong>`` | Defines **important** text          |
| ``<sub>``    | Defines **subscripted** text        |
| ``<sup>``    | Defines **superscripted** text      |
| ``<ins>``    | Defines **inserted** text           |
| ``<del>``    | Defines **deleted** text            |
| ``<mark>``   | Defines **marked/highlighted** text |
# HTML Quotation and Citation
Here, we will go through the `<blockquote>`,`<q>`, `<abbr>`, `<address>`, `<cite>`, and `<bdo>`  elements.
## The `<blockquote>` for Quotations
The `<blockquote>` element defines a section that is quoted from another source. 
**Browsers usually indent these elements**:
```html
<p>Here is a quote from WWF´s website</p>
<blockquote cite="http://www.worldwildlife.org/who/index.html">
For 60 years, WWF has worked to help people and nature thrive. As the world´s leading conservation organization, WWF works in nearly 100 countries. At every level, we collaborate with people around the world to develop and deliver innovative solutions that protect communities, wildlife, and the places in which they live.
</blockquote>
```
## The `<q>` for Short Quotations
The `<q>` tag defines *short* quotation. Browsers usually insert quotation marks around the text:
```html
<p>WWF's goal is to: <q>Build a future where people...</q></p>
```

## The `<abbr>` for Abbreviations
The `<abbr>` tag defines an abbreviation or an acronym, like "HTML", "CSS", "Mr.", "Dr.", "ASAP", "ATM".

Marking abbreviations can give useful information to browsers.
```html
<p>The <abbr title="World Health Organization">WHO</abbr> was founded in 1948</p>
```
>[!important] **Tip:** use the [[#The title Attribute| global title]] attribute to show the description for the abbreviation when you hover over the element.
## The `<address>` for Contact Information
The `<address>` tag defines the contact information for the author of a document or an article.

The contact information can be an **email address, URL, physical address, phone number, social media handle, etc**.

The text in the `<address>` element usually renders in _italic,_ and browsers will always add a line break before and after the `<address>` element.
```html
<address>
Written by Me <br>
Visit us at: <br>
MySite.com <br>
MyAddress <br>
Finland
</address>
```
## The ``<cite>`` for Work Title
The `<cite>` tag defines the **title** of a creative work (e.g. a book, a poem, a song, a movie, a painting, a sculpture, etc.).

**Note:** A person's name is not the title of a work.

The text in the `<cite>` element usually renders in _italic_.
```html
<p><cite>The Scream</cite> by Edvard Munch. Painted in 1893.<p>
```

## The `<bdo>` for Bi-Directional Override
The `<bdo>` tag is used to override the current text direction:
(_BDO stands for Bi-Directional Override_)
```html
<bdo dir="rtl">This text will be written from right to left</bdo>
```
## Summary

| Tag            | Description                                          |
| -------------- | ---------------------------------------------------- |
| `<abbr>`       | Defines an abbreviation or acronym                   |
| `<address>`    | Defines contact information                          |
| `<bdo>`        | Defines text direction                               |
| `<blockquote>` | Defines a section that is quoted from another source |
| `<cite>`       | Defines the title of a work                          |
| `<q>`          | Defines a short inline quotation                     |
# HTML Comments
Comments are not displayed in the browser, but they can help document your HTML source code.
## Comment Tag
You can add comments to the source code with:
```html
<!-- Write your comment here -->
```
Note 2 things. 
	* There is an exclamation mark at the start (`!`)
	* Comments are not displayed by the browser, they only appear in source code
### Add Comments
```html
<!-- This is a comment -->

<p>My paragraph</p>

<!-- Another comment -->
```
### Hide Content
```html
<!-- <p>My Hidden Paragraph</p> -->
```

Or hide multiple sections with:
```html
<!--
These are all commented
<p>Another commented paragraph</p>
-->
```

Or hide inline content with:
```html
<p>This is my <!-- paragraph ---> right here!</p>
```
# HTML Colours
In this category we will explore different ways to color with HTML.
## Background color
You can set the background for elements:
```html
<h1 style="background-color:DodgerBlue">Hello everyone!</h1>
<p style="background-color:Tomato">My red text<p>
```
## Text Color
You can set the color of text:
```html
<h1 style="color:Tomato">Hello everyone!</h1>
<p style="color:blue">My blue text</p>
<p style="color:green">My green text</p>
```
## Border Color
```html
<h1 style="border:8px solid Green">My bordered Green header</h1>
<h1 style="border:6px solid Blue">My bordered Blue header</h1>
<h1 style="border:2px solid Violet">My bordered Violet header</h1>
```
>[!important] To change the thickness of the border modify the `px` value.
## Colour Values
In HTML, colours can also be specified using **RGB** values, **HEX** values, **HSL** values, **RGBA** values, and **HSLA** values:
```html
<h1 style="background-color:rgb(255, 99, 71);">...</h1>  
<h1 style="background-color:#ff6347;">...</h1>  
<h1 style="background-color:hsl(9, 100%, 64%);">...</h1>  
  
<h1 style="background-color:rgba(255, 99, 71, 0.5);">...</h1>  
<h1 style="background-color:hsla(9, 100%, 64%, 0.5);">...</h1>
```
# HTML Styles - CSS
## What is CSS
Cascading Style Sheets (CSS) is used to format the layout of a webpage.

With **CSS**, you can control the **color**, **font**, **the size of text**, **the spacing between elements**, **how elements are positioned and laid out, what background images or background colours are to be used, different displays for different devices and screen sizes, and much more**!
>[!important] **Tip:** The word **cascading** means that a style applied to a parent element will also apply to all children elements within the parent. So, if you set the color of the body text to "blue", all headings, paragraphs, and other text elements within the body will also get the same color (unless you specify something else)!
## Ways of Using CSS
CSS can be added to the HTML document in 3 ways:
* **Inline**: By using the `style` attribute inside HTML elements.
* **Internal**: By using a `<style>` element in the `<head>` section.
* **External**: By using a `<link>` element to link to an external CSS file.
The most **common way** to add CSS, is to keep the styles in **external CSS files**. However, this time we will use **inline** and **internal styles**.
## Inline CSS
* It's used to apply a unique style to a single HTML element.
* An inline CSS uses the `style` attribute of an HTML element.
The following example sets the text color of the `<h1>` element to blue, and the text color of the `<p>` element to red:
```html
<h1 style="color:blue">This is my header!</h1>
<p style="color:red">This is my paragraph</p> 
```
## Internal CSS
* Used to define a style for a **single HTML page**.
* An internal CSS is defined in the `<head>` section of an HTML page, within a `<style>` element.

The following example sets:
* The text color of **ALL** the `<h1>` elements (on that page) to blue, 
* The text color of **ALL** the `<p>` elements to red, and
* In addition, the page will be displayed with a "powderblue" background color:
```html
<!DOCTYPE html>
<html>
<head>
<style>
body {background-color: powderblue;}
h1 {color: blue}
p {color: red}
</style>
</head>

<body>
<h1>This Is My Header</h1>
<p>This is my paragraph</p
</body>
</html>
```

## External CSS
 An external style sheet is used to define the style for **many** HTML pages.

To use an external style sheet, add a link to it in the `<head>` section of each HTML page:
```html
<!DOCTYPE html>
<html>
<head>
	<link rel="stylesheet" href="styles.css">
</head>
<body>

<h1>This is My Header</h1>
<p>my paragraph</p>

</body>
</html>
```
The **external style sheet** can be written in any text editor. The file must **not** contain any HTML code, and must be saved with a ``.css`` extension.

Here is what the "**styles.css**" file looks like:
```css
body {
	background-color: powderblue;
}
h1 {
	color: blue;
}
p {
	color: red;
}
```
>[!important] **Tip:** With an external style sheet, you can change the look of an entire web site, by changing one file!

## CSS Colours, Fonts and Sizes
Here, are some commonly used CSS properties:
* The CSS `color` property defines the text color to be used.
* The CSS `font-family` property defines the font to be used.
* The CSS `font-size` property defines the text size to be used.
```css
<!DOCTYPE html>
<html>
<head>
<style>
h1 {
	color: blue
	font-family: verdana;
	font-size: 300%;
}
p {
	color: red
	font-family: courier:
	font-size: 160%;
	}
</style>
</head>

<body>
	<h1>This Is My Header</h1>
	<p>This is my paragraph</p>
</body>
</html>
```
## CSS Border
The `border` property defines a border around an HTML element.
```css
p {
	border: 2px solid violet;
}
```
>[!important] **Tip:** You can define a border for nearly all HTML elements.
## CSS Padding
The `padding` property defines a padding (space) between the text and the border.
```css
p {
	border: 2px solid violet;
	padding: 30px;
}
```
## CSS Margin
The `margin` property defines a margin (space) outside the border.
```css
p {
	border: 2px solid violet;
	margin: 50px;
}
```
## Link to External CSS
External style sheets can be referenced with a **full URL** or with a path **relative** to the current web page:
**Full URL**:
```html
<link rel="stylesheet" href="https://mysite.com/html/styles.css">
```

**Relative in html folder**:
```html
<link rel="stylesheet" href="/html/styles.css">
```

**Relative in same folder**:
```html
<link rel="stylesheet" href="styles.css">
```
## Chapter Summary
- Use the HTML `style` attribute for inline styling
- Use the HTML `<style>` element to define internal CSS
- Use the HTML `<link>` element to refer to an external CSS file
- Use the HTML `<head>` element to store ``<style>`` and ``<link>`` elements

- Use the CSS `color` property for text colours
- Use the CSS `font-family` property for text fonts
- Use the CSS `font-size` property for text sizes
- Use the CSS `border` property for borders
- Use the CSS `padding` property for space inside the border
- Use the CSS `margin` property for space outside the border