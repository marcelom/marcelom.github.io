Title: Postfix Encrypted SMTP
Tags: Postfix, SMTP
Author: Marcelo Moreira
Summary: Enabling encrypted SMTP on postfix

As many of you know, the email servers at Stanford will only accept encrypted connections, starting June 24th, 2010.

I use postfix extensively. This is what I did in order to enable TLS connections:

Edit main.cf and add the following two lines:

    smtp_tls_security_level = encrypt
    smtp_use_tls=yes

These will force all outgoing connections to be made exclusively through TLS.

This works well because I use Stanford SMTP servers as my smart relay (see [relayhost](http://www.postfix.org/postconf.5.html#relayhost) option). If you relay directly to other destinations, then you might want to use [smtpd_tls_per_site](http://www.postfix.org/postconf.5.html#smtp_tls_per_site) instead.

To debug the connections, use the following:

    debug_peer_list = stanford.edu
    debug_peer_level = 2

And then check your log files. Make sure to disable them later (just put a # in front of them) to avoid filling up your logs too quickly.
