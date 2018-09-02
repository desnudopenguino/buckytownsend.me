---
layout: post
title: "Adding TLS Certs on an OpenBSD httpd Server using acme-client"
date: 2017-01-13 17:52:59 -0700
permalink: /blog/:year-:month-:day-:slug/
categories: OpenBSD letsencrypt tls ssl httpd acme-client
---
I finally got around to one of my long-time mini-projects, adding TLS to my website(s) using [Let's Encrypt](https://letsencrypt.org). Let's Encrypt is a free Certificate Authority. One thing that must be noted with Let's Encrypt is that it does not verify the owner of the site, they just provide a signed certificate to use https on your website to encrypt your traffic, which is a necessity for any website storing user data or processing payments. On OpenBSD there is a package called [acme-client](https://kristaps.bsd.lv/acme-client/) which helps automate generating and signing  TLS certificates.

You can find the guide I followed at ["Easy Secure Web Serving with OpenBSD's acme-client and Let's Encrypt"](https://spin.atomicobject.com/2016/09/20/openbsd-acme-client-lets-encrypt/). It worked awesomely, and now I have a certificate for all of my sites, and a way to generate certificates for projects that need secure connections. One small annoyance is that you can't generate wildcard certificates (\*.buckytownsend.me for example). You have to generate a certificate for each domain/subdomain, or combine them into one certificate (as shown in the guide).

On a side note, I also set up a [httpd(8)](http://man.openbsd.org/cgi-bin/man.cgi/OpenBSD-5.9/man8/httpd.8) server and moved my jekyll-generated content found in /\_site to a web directory instead of running jekyll server through [relayd(8)](http://man.openbsd.org/cgi-bin/man.cgi/OpenBSD-5.9/man8/relayd.8). I'm trying to simplify and consolidate my services and get more comfortable with httpd which should be able to handle my other projects through fastcgi and the [slowcgi(8)](http://man.openbsd.org/cgi-bin/man.cgi/OpenBSD-5.9/man8/slowcgi.8) service. That's going to have to wait for another day though.
