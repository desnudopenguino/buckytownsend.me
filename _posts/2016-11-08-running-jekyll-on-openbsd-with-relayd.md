---
layout: post
title:  "Running Jekyll on OpenBSD (maybe pt. 1)"
date:   2016-11-07 10:23:00 -0700
permalink: /blog/:year-:month-:day-:slug/
categories: jekyll update hosting relayd httpd OpenBSD
---
First I'll give you a little background. In the past, I messed around with FreeBSD for web development, fell in love with jails and ZFS, then fell out into the world of linux. I spent some time messing in the Debian realm, playing with the clean, simple system. I had heard of OpenBSD for years, and was always interested in this supposed security lockbox of an OS. A little over a year ago, I decided to throw OpenBSD on a VM and see what happened. It was different to set up than other OSes I had worked with in the past, and was surprisingly simple. A not-so-quick perusing of the FAQ and a few select man pages was enough to get me down a nice path. Now, a year later, I am finally digging into some of the more useful and practical tools that OpenBSD offers out of the box.

Of course Jekyll doesn't come out of the box, but there are many other useful tools. My goal is to learn, apply, and share my experiences with these built in tools so that others may also be able to take advantage of the simple, clean, powerful, and secure operating system that is OpenBSD. Many of the things I will be sharing are figured out through time spent reading man pages, bugging the guys on the #openbsd channel on freenode (thanks you guys if you ever read this), and putting together some googling.

So... Why Jekyll for a website/blog? I chose Jekyll because I have spent a good bit of time playing with Ruby and Ruby on Rails in the past. I'm no expert by any means, but I am becoming more dangerous as the days go on. The Ruby ecosystem can be a mess, especially on OpenBSD. Hopefully some of these posts will be able to guide others through some of the mazes and avoid some of the pitfalls that I have confronted. I also chose Jekyll because it isn't a big bloated system like WordPress. I can just type my posts and save them and go. Not having to manage a database is another plus. This site, as it stands, is very light and I like that. Having bells and whistles is nice for some people, but I am a utilitarian. I work in Vim, and have been for years. I love my terminal, and being able to do things efficiently. Jekyll fulfills this requirement for me so I am using it. Also I don't have to worry about people complaining or bots posting garbage to my site. There are more efficient ways to get in contact with me than by commenting on a blog post I have written. It would be too much work for me to go back and review such things anyway.

Now onto some more technical bits of this post. I am building this thing, and also documenting it as well as many of my other projects as they progress. I am a total newb when it comes to Jekyll, but I am figuring things out. I am following the Jekyll guide on their website, which is written well and has some great info. The first issue that I ran into is how to get my blog running for the world to see. I do have a domain, and a hosting service set up running OpenBSD of course, and it has Jekyll and all of its dependencies installed, and a Jekyll blog set up (or you wouldn't be seeing this). Running jekyll server starts the instance of course, on the local loop-back connection (127.0.0.1) on port 4000. This is nice for testing, but I want you all to see what I'm doing as well. You can't run a service on port 80 (the http port) without root. I didn't want to start the Jekyll service as root so I had to figure out a way around this little bug.

First I set up the server with a soft link from the \_sites directory to a directory in /var/www/htdocs and ran a simple httpd config, but after figuring out some simple stuff with relayd I was able to set up my blog to run Jekyll in the default config (127.0.0.1:4000) and run a simple reverse proxy to my domain name. This is nice because I don't have to use httpd for now, and I can run everything through the service it was originally designed to be run on, and still be able to have multiple web services/sites running. Though this may be open to change in the future after playing around with things.

Below are my initial httpd config and my relayd config(copied from ... and heavily edited).

My initial httpd.conf:

{% highlight config file %}
server ???.???.???.??? {
	listen on * port 80
	root "htdocs/buckytownsend.me/_site"
}
{% endhighlight %}

My current relayd.conf:

{% highlight config file %}
# Buckytownsend.me reverse proxy for jekyll blog to run on port 80 
# run through loop-back (127.0.0.1) on port 4000
 
## Macros
#
relayd_addr="buckytownsend.me"
relayd_port="80"

## If you have a back-end web cluster or a single machine,
## you can specify each host here. Then use the table name
## with load balancing in the "relay" directive below.
#
web_port="4000"
table <web_hosts> { 127.0.0.1 }

## Global Options
#
# Interval in seconds at which the back-end hosts
# will be checked (default: 10 seconds)
interval 10
 
# Timeout for back-end servers to respond. Set to
# 200 for local servers and around 1000 for servers
# on other subnets. (default: 200 milliseconds)
timeout 200
 
# Number of child processes to run. (default: 5)
prefork 5
 
# Log state notifications after completed host
# checks. State can be up, down or unknown.
log updates

## Reverse layer 7 HTTP proxy with filtering
#
http protocol "httpfilter" {

   ### TCP performance options 
    tcp { nodelay, sack, socket buffer 65536, backlog 100 }

   ### Return HTTP/HTML error pages
    return error
}

relay httpproxy {
   ### listen and accept redirected connections from pf. For most
   ### protocol types you can also use the synproxy flag in your pf.conf rules.
    listen on $relayd_addr port $relayd_port

   ### apply web filters listed above
    protocol "httpfilter"

   ### forward to webserver(s) with load balancing and
   ### check the "/" path to make sure it comes back
   ### with code 200. You can check any path and also execute scripts
   ### with,  check http '/path/test_script.php' code 200
    forward to <web_hosts> port $web_port mode loadbalance check http "/" code 200 
}
{% endhighlight %}

This is what is letting you see my current blog at buckytownsend.me. I think it is pretty cool. I can set up a bunch of other domain name based reverse proxies and even have load balancing services pretty easily if I ever end up with a beast of a web system to manage in the future, or even to obfuscate real service addresses from you damn dirty apes out there. Of course PF will help with that as well, and I've got that on my to do list.

I think this is enough rambling for today. The short and skinny is that you can easily set up relayd to run your Jekyll site on an OpenBSD system with default, or near default settings in you Jekyll site's config file (\_config.yml). I still have to play around with relayd to get exactly what I want, but this is a good start for me.
