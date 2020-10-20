---
layout: post
category : development
tags : [openssl, tips]
---
{% include JB/setup %}
We found we needed to change some certificate formats at work to support a specific 3rd party. Googling found this excellent cheat-sheet for working with `openssl`: 
https://www.sslshopper.com/article-most-common-openssl-commands.html

There's a lot of useful stuff here, ones we needed were:

**Check if one of our private keys was in a compatible format**

~~~bash
openssl rsa -in privateKey.key -check
~~~
