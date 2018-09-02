---
layout: post
title:  "Work Logger: My First Shot at a Custom ksh Function Set and Manual Used in Tmux"
date:   2016-11-15 10:23:00 -0700
permalink: /blog/:year-:month-:day-:slug/
categories: ksh scripting tmux make makefile man manpage mandoc
---
This is my first shot at really working in shell scripting to do something useful. I'll start with a little background first.

I am a self-employed/freelance IT jack of all trades. Most of my work is in web development. While I work in the wonderful world of HTML, CSS, JS, and a plethora of server side frameworks, languages and servers, I have developed a somewhat efficient way to track billable hours so that I can accurately and efficiently track my time. I have always been a pen-and-paper kind of guy, but that was getting exceetingly hard to track as I filled up composition notebooks with problems, issues, solutions, and timestamps. I would also often forget to mark my start or finish time, and have to go back later and guestimate what it was when putting together time sheets or invoices. It's not fun, and not really fair to myself nor my clients.

At this point you are probably yelling, "Bucky, you dingus, there are tons of tools out there to do this for you, and better than you can probably imagine." I totally agree. The thing is I spend about 85% of my time in terminal windows. I am not a huge fan of using my mouse unless I absolutely have to, so I decided to undertake the task of developing a set of ksh functions to do what I need for work. I don't really need a big crazy system, just something simple and functional. And Work Logger was born.

I've been using this tool for a few months now, but I kept running into one small issue... Occasionally I would forget the flags for a specific functionality if I hadn't used it for a while. Over the past couple days, I have learned and implemented mandoc man page formatting to build a simple manual page for my scripts. Today I took it a step further and created a makefile so that you can install the script and manual file into their appropriate locations to be used by anyone on the system. I had never created a makefile, so this was another small learning exercise. It turned out fruitful though. I now have a git repo on github where people can download and install my script and its dependencies and use it (not fully yet though). It is available at [github.com/desnudopenguino/wl](https://github.com/desnudopenguino/wl) if you want to take a look.

This was a nice little exercise to learn process automation using make, learing how to write a manual page for \*nix, and finding a simple way to solve a problem. I thought I would share it with you all in the hope that maybe someone else won't have to reinvent this wheel for themselves.

Cheers,
Bucky




