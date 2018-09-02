---
layout: post
title:  "Running Jekyll on OpenBSD (pt. 2)"
date:   2016-11-18 18:08:55 -0700
permalink: /blog/:year-:month-:day-:slug/
categories: jekyll OpenBSD relayd jekyll-archive
---
Back to this thing again... So I've been running this site for a little under a month now, and I have decided to try to get more working with relayd. Mainly subdomain/multiple domains working on one host. I have yet to turn on the httpd daemon, hosting everything through jekyll, with a reverse proxy through relayd. I found, through some digging and looking around that my thought for how to run multiple domains/subdomains was incorrect. I now present you with my new relayd.conf file.


{% highlight config file %}
# Buckytownsend.me reverse proxy for jekyll blog to run on port 80 
# run through loop-back (127.0.0.1) on port 4000
 
## Macros
#
table <bt_hosts> { 127.0.0.1 }
table <cf_hosts> { 127.0.0.1 }

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

# forward to other service
http protocol "cragfinder" {
    match request quick header "Host" value "cragfinder.buckytownsend.*"\
    forward to <cf_hosts>
}

relay buckytownsend.me {
   ### listen and accept redirected connections from pf. For most
   ### protocol types you can also use the synproxy flag in your pf.conf rules.
   # listen on $bt_proxy_addr port $bt_proxy_port
    listen on 0.0.0.0 port 80

   ### apply web filters listed above
    protocol "cragfinder"

   ### forward to webserver(s) with load balancing and
    forward to <bt_hosts> port 4000 # buckytownsend.me - blog
    forward to <cf_hosts> port 3000 # cragfinder.buckytownsend.me - capstone project
}
{% endhighlight %}

As you can see I killed some stuff, and added some extra http protocols to point to the correct subdomains but this makes some easy sense, and allows me to point to whatever I want. I also found a useful command for relayd, which allows you to reload the config file on the fly.
{% highlight shell %}
# relayctl reload
{% endhighlight %}

Now I can pretty easily update my relayd config and have it work with little downtime.

And now another interesting update... If you haven't noticed, I now have a bunch of random links as the top of my pages. I recently installed and added the [jekyll-archives](https://github.com/jekyll/jekyll-archives) gem to my site so that I can have a nice way to organize my posts as they proliferate. It is super easy to install, and makes archiving/creating type-specific indecies super easy. My main reason for adding it was to have an easy way to access/share blog posts pertaining to my capstone with my committee, classmates, and anyone else interested, while still having it be a part of my overall blog (who wants to manage separate blogs for separate things?). This place is the catch-all for all of my stuff, so I thought it might be a good idea to let it be sorted as well. I will probably make a category index that lists all categories, and add links to category-specific indecies from the page that relates to them. That's all I have for today!
