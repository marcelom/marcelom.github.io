---
layout: post
title: Encrypting a file with OpenSSL
tags: Encryption OpenSSL Security
author: Marcelo Moreira
excerpt: Quickly encrypting a sinple file with OpenSSL
---

Let's assume you want to transfer a file securily to a friend, for exmaple.

There are basically 2 ways to accomplish this: with a symmetric CBC (Cyclic Block Cipher), like for example AES-256-CBC; or with a keypair, 

We will cover here only the first one.

In short, if you want the encryption to be platform independent (and you want that, right ?) you should openssl.

To encrypt the file:

{% highlight console %}
openssl aes-256-cbc -in secrets.txt -out secrets.txt.enc
{% endhighlight %}

To decrypt, use the inverse operation:

{% highlight console %}
openssl aes-256-cbc -d -in secrets.txt.enc -out secrets.txt
{% endhighlight %}

If the message is simple enough (like a one liner, or just a few ones), you can even dump it to a base64 string, so that you can attach it directly to the body of an email:

{% highlight console %}
$ echo "this is a serious secret" | openssl aes-256-cbc -base64
enter aes-256-cbc encryption password: XXX
Verifying - enter aes-256-cbc encryption password: XXX
U2FsdGVkX18QYRCy52o6GQIHx9TaW8VgCJKEwKf8tFdGmpqKhPx30pFDLYkrbQw5

$ echo "U2FsdGVkX18QYRCy52o6GQIHx9TaW8VgCJKEwKf8tFdGmpqKhPx30pFDLYkrbQw5" | openssl aes-256-cbc -d -base64
enter aes-256-cbc decryption password: XXX
this is a serious secret
{% endhighlight %}

Make sure to always use a strong cipher with a CBC type. At a minimum, pick the aes-256-cbc. To list all available ciphers do:

{% highlight console %}
$ openssl enc --help
options are
-in <file>     input file
-out <file>    output file
-pass <arg>    pass phrase source
-e             encrypt
-d             decrypt
-a/-base64     base64 encode/decode, depending on encryption flag
-k             passphrase is the next argument
-kfile         passphrase is the first line of the file argument
-md            the next argument is the md to use to create a key
                 from a passphrase.  One of md2, md5, sha or sha1
-S             salt in hex is the next argument
-K/-iv         key/iv in hex is the next argument
-[pP]          print the iv/key (then exit if -P)
-bufsize <n>   buffer size
-nopad         disable standard block padding
-engine e      use engine e, possibly a hardware device.
Cipher Types
-aes-256-cbc
-bf-cbc
 (im listing only the important ones here...)
{% endhighlight %}

For more infromation visit:

* http://en.wikipedia.org/wiki/Symmetric-key_algorithm
* http://en.wikipedia.org/wiki/Block_cipher_modes_of_operation

The sites below will give you a good glimpse of how safe is your password:

* https://www.grc.com/haystack.htm (this doesn't take into account any dictionary attacks!)
* http://howsecureismypassword.net (this at least check for common passwords)
