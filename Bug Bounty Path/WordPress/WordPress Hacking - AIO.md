# Introduction
## What is WordPress
[WordPress](https://wordpress.org/) is the most popular open source **Content Management System** (**CMS**), powering nearly one-third of all websites in the world. 
* It can be used for multiple purposes, such as hosting 
	* blogs, 
	* forums, 
	* e-commerce,
	* project management,
	* document management, 
	* and much more. 
* WordPress is highly customizable as well as SEO (Search Engine Optimization) friendly, which makes it popular among companies. 
* It has a large library of extensions called **themes** and **plugins**, both free and paid, that can be added to enhance the website. Some examples of plugins are ``WPForms``, a robust contact form, ``MonsterInsights`` that interfaces with Google Analytics, and Constant Contact, a popular email marketing service. 
* However, its customizability and extensible nature make it prone to vulnerabilities through third-party themes and plugins. WordPress is **written in PHP** and usually runs on ``Apache`` with ``MySQL`` as the backend. 
* Many hosting companies offer WordPress as an option when creating a new website and even assist with backend tasks such as security updates.
### What is a CMS?
A **CMS** is a powerful tool that helps build a website without the need to code everything from scratch (or even know how to write code at all). 

The CMS does most of the "heavy lifting" on the infrastructure side to focus more on the design and presentation aspects of the website instead of the backend structure.

Most CMS' provide a rich [What You See Is What You Get (WYSIWYG)](https://en.wikipedia.org/wiki/WYSIWYG) editor where users can edit content as if they were working in a word processing tool such as Microsoft Word. 

Users can upload media directly from a media library interface instead of interacting with the webserver either from a management portal or via ``FTP`` or ``SFTP``.

A CMS is made up of two key components:
- A Content Management Application (**CMA**) - The interface used to add and manage content.
- A Content Delivery Application (**CDA**) - The backend that takes the input entered into the **CMA** and assembles the code into a working, visually appealing website.

A good **CMS** will provide extensibility, allowing you to add functionality and design elements to the site without needing to work with the website code, rich user management to provide fine-grained control over access permissions and roles, media management to allow the user to easily upload and embed photos and videos, and proper version control. 

When looking for a CMS, we should also confirm that it is well-maintained, receives periodic updates and upgrades, and has sufficient built-in security settings to harden the website from attackers.
## WordPress Structure
## Default WordPress File Structure
WordPress can be installed on a ``Windows``, ``Linux``, or ``Mac OSX`` host. 

Here we will focus on a default WordPress installation on an ``Ubuntu Linux`` web server. WordPress requires a fully installed and configured ``LAMP stack`` (``Linux operating system, Apache HTTP Server, MySQL database, and the PHP programming language``) before installation on a Linux host. 

After installation, all WordPress supporting files and directories will be accessible in the Webroot located at `/var/www/html`.

Below is the directory structure of a default WordPress install, showing the key files and subdirectories necessary for the website to function properly:
```bash
$ tree -L 1 /var/www/html
.
├── index.php
├── license.txt
├── readme.html
├── wp-activate.php
├── wp-admin
├── wp-blog-header.php
├── wp-comments-post.php
├── wp-config.php
├── wp-config-sample.php
├── wp-content
├── wp-cron.php
├── wp-includes
├── wp-links-opml.php
├── wp-load.php
├── wp-login.php
├── wp-mail.php
├── wp-settings.php
├── wp-signup.php
├── wp-trackback.php
└── xmlrpc.php
```
### Key WordPress Files
The root directory of WordPress contains files that are needed to configure WordPress to function correctly.
- `index.php` is the homepage of WordPress.
- `license.txt` contains useful information such as the version WordPress installed.
- `wp-activate.php` is used for the email activation process when setting up a new WordPress site.
- `wp-admin/` folder contains the login page for administrator access and the backend dashboard. Once a user has logged in, they can make changes to the site based on their assigned permissions. The login page can be located at one of the following paths:
    - `/wp-admin/login.php`
    - `/wp-admin/wp-login.php`
    - `/login.php`
    - `/wp-login.php`

This file can also be renamed to make it more challenging to find the login page.

- `xmlrpc.php` is a file representing a feature of WordPress that enables data to be transmitted with HTTP acting as the transport mechanism and XML as the encoding mechanism. This type of communication has been replaced by the WordPress [REST API](https://developer.wordpress.org/rest-api/reference).
### WordPress Configuration File
The `wp-config.php` file contains information required by WordPress to connect to the database, such as the **database name, database host, username and password, authentication keys and salts, and the database table prefix**. This configuration file can also be used to activate ``DEBUG`` mode, which can useful in troubleshooting.
```php
<?php
/** <SNIP> */
/** The name of the database for WordPress */
define( 'DB_NAME', 'database_name_here' );

/** MySQL database username */
define( 'DB_USER', 'username_here' );

/** MySQL database password */
define( 'DB_PASSWORD', 'password_here' );

/** MySQL hostname */
define( 'DB_HOST', 'localhost' );

/** Authentication Unique Keys and Salts */
/* <SNIP> */
define( 'AUTH_KEY',         'put your unique phrase here' );
define( 'SECURE_AUTH_KEY',  'put your unique phrase here' );
define( 'LOGGED_IN_KEY',    'put your unique phrase here' );
define( 'NONCE_KEY',        'put your unique phrase here' );
define( 'AUTH_SALT',        'put your unique phrase here' );
define( 'SECURE_AUTH_SALT', 'put your unique phrase here' );
define( 'LOGGED_IN_SALT',   'put your unique phrase here' );
define( 'NONCE_SALT',       'put your unique phrase here' );

/** WordPress Database Table prefix */
$table_prefix = 'wp_';

/** For developers: WordPress debugging mode. */
/** <SNIP> */
define( 'WP_DEBUG', false );

/** Absolute path to the WordPress directory. */
if ( ! defined( 'ABSPATH' ) ) {
	define( 'ABSPATH', __DIR__ . '/' );
}

/** Sets up WordPress vars and included files. */
require_once ABSPATH . 'wp-settings.php';
```
### Key WordPress Directories
The `wp-content/` folder is the main directory where plugins and themes are stored and the subdirectory `uploads/` is usually where any files uploaded to the platform are stored. 

These directories and files should be carefully enumerated as they may lead to contain sensitive data that could lead to RCE or exploitation of other vulnerabilities or misconfigurations.
```bash
$ tree -L 1 /var/www/html/wp-content
.
├── index.php
├── plugins
└── themes
```


`wp-includes` contains everything except for the administrative components and the themes that belong to the website. This is the directory where core files are stored, such as certificates, fonts, JavaScript files, and widgets.
```bash
]$ tree -L 1 /var/www/html/wp-includes
.
├── <SNIP>
├── theme.php
├── update.php
├── user.php
├── vars.php
├── version.php
├── widgets
├── widgets.php
├── wlwmanifest.xml
├── wp-db.php
└── wp-diff.php
```
## WordPress User Roles
There are five types of users in a standard WordPress installation.

| Role              | Description                                                                                                                                  |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------------- |
| **Administrator** | Has access to administrative features within the website. This includes adding and deleting users and posts, as well as editing source code. |
| **Editor**        | Can publish and manage posts, including the posts of other users.                                                                            |
| **Author**        | Can publish and manage their own posts.                                                                                                      |
| **Contributor**   | Can write and manage their own posts but cannot publish them.                                                                                |
| **Subscriber**    | Normal users who can browse posts and edit their profiles.                                                                                   |

Gaining access as an **administrator** is usually needed to obtain code execution on the server. However, editors and authors might have access to certain vulnerable plugins that normal users do not.
# Enumeration
## WordPress Core Version Enumeration
It is always important to know what type of application we are working with. An essential part of the enumeration phase is uncovering the **software version number**. This is helpful when searching for common misconfigurations such as default passwords that may be set for certain versions of an application and searching for known vulnerabilities for a particular version number.

We can use a variety of methods to discover the version number manually. The first and easiest step is **reviewing the page source code**. We can do this by right-clicking anywhere on the current page and selecting "``View page source``" from the menu or using the keyboard shortcut `[CTRL + U]`.

We can search for the `meta generator` tag using the shortcut `[CTRL + F]` in the browser or use `curl` along with `grep` from the command line to filter for this information:
```html
...SNIP...
<link rel='https://api.w.org/' href='http://blog.inlanefreight.com/index.php/wp-json/' />
<link rel="EditURI" type="application/rsd+xml" title="RSD" href="http://blog.inlanefreight.com/xmlrpc.php?rsd" />
<link rel="wlwmanifest" type="application/wlwmanifest+xml" href="http://blog.inlanefreight.com/wp-includes/wlwmanifest.xml" /> 
<meta name="generator" content="WordPress 5.3.3" />
...SNIP...
```
**Or**
```bash
$ curl -s -X GET http://blog.inlanefreight.com | grep '<meta name="generator"'

<meta name="generator" content="WordPress 5.3.3" />
```
Aside from version information, the source code may also contain comments that may be useful. Links to **CSS** (**style sheets**) and **JS** (**JavaScript**) can also provide hints about the version number.
**CSS**
```html
...SNIP...
<link rel='stylesheet' id='bootstrap-css'  href='http://blog.inlanefreight.com/wp-content/themes/ben_theme/css/bootstrap.css?ver=5.3.3' type='text/css' media='all' />
<link rel='stylesheet' id='transportex-style-css'  href='http://blog.inlanefreight.com/wp-content/themes/ben_theme/style.css?ver=5.3.3' type='text/css' media='all' />
...SNIP...
```

**JS**
```html
...SNIP...
<script type='text/javascript' src='http://blog.inlanefreight.com/wp-includes/js/jquery/jquery.js?ver=1.12.4-wp'></script>
<script type='text/javascript' src='http://blog.inlanefreight.com/wp-includes/js/jquery/jquery-migrate.min.js?ver=1.4.1'></script>
..SNIP..
```
In older WordPress versions, another source for uncovering version information is the `readme.html` file in WordPress's root directory.
## Plugins and Themes Enumeration












# Exploitation






# Security Measures




