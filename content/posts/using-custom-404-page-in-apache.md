---
title: "Custom 404 page in Apache"
date: 2014-01-29
description: "How to use custom 404 error page in Apache"
categories: [linux]
slug: linux
tags: ["apache"]
draft: false
---

## What is 404 error?

![404 error](/images/404_post.png)

When you fire up your web-browser and type in, say `google.com/foo`, your web-browser makes a request to the server for page "foo". In return, server returns a response with a special status code indicating the status of the request. If the page `foo` is not found on the server, `404` status code is sent in the respone. Some other common status codes are:

    200 : OK
    301 : Moved Permanently
    400 : Bad Request
    401 : Unauthorized
    403 : Forbidden
    404 : Not Found
    500 : Internal Server Error

## What is .htaccess file?

`.htaccess` is short name for Hypertext Access. They are apache server configuration files at directory level *i.e* they control the settings for the directory in which they are placed in as well as the sub-directories underneath. You can use them to over-ride the parent configuration settings. It is not recommended to use `.htaccess` files as they create certain performance issues. By default they are disabled.

## How to enable .htaccess ?

Open the file "default" placed inside `sites-available`

`$ vim /etc/apache2/sites-available/default`

Search for the section:

    <Directory /var/www/>
                Options Indexes FollowSymLinks MultiViews
                AllowOverride None
                Order allow,deny
                allow from all
    </Directory>

Modify `AllowOverride None` to `AllowOverride All`

Restart the apache server:

`$ sudo service apache2 restart`

You are done!

## How to use custom 404 page using .htaccess ?

Create a file with name `.htaccess` in your document web root. If it already exists skip to next step.

Place the following line at the end of the file :

`ErrorDocument 404 /404.html`

Save and exit.

Now whenever a visitor request a page which does not exists, they will see 404.html instead of apache default 404 error message.
