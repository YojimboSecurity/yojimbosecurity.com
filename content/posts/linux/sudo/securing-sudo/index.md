---
title: "Securing Sudo"
date: 2018-11-28T20:33:58-05:00
draft: false
# feature_image: "images/siteLogo.png"
---


In this tutorial we will secure Sudo by enabling logging. Giving
us the ability to replay a users session.

Prerequisites
=============

I will be using Ubuntu however this should work on most systems
with sudo. Keep in mind the paths and files may differ across
different systems.

Enable SUDO Logging
===================

There are several ways to log sudo activity but the coolest way
is with `sudoreplay`.

To enable sudo logging we will be editing the sudoers file.
Before that I highly recommend using visudo to do so. For one
thing you don't have to remember the fie path just type visudo.
Also if there are errors in you configuration it will notify
you. Visudo will be what I using in the examples.

Add the following to your sudoers file.

```
Defaults    log_output  
Defaults!   /sbin/reboot !log_output  
Defaults!   /usr/bin/sudoreplay !log_output
```

The above enables output logging except for sudoreplay and
reboot.

Verify
======

Run

```bash
sudo ls 
```

Then

```bash
sudoreplay -l
```

You should see something like the following.

```bash
Oct 26 15:56:57 2017 : vagrant : TTY=/dev/pts/0 ; CWD=/var/log ; USER=root ; TSID=000001 ; COMMAND=/bin/ls  
To replay sudoreplay 000001.
```

If a user were to sudo su the whole session could be replayed
in real time, speed up, or slowed down.

Summary
=======

The purpose of this was to enable sudo logging and with that
accountability for privileged commands has been established.

This is just one step in securing a system. However a bunch of
little steps add up and makes a big difference.

