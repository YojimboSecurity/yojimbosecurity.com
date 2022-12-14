---
title: "Securing SSH"
date: 2019-01-23T20:33:58-05:00
draft: false
# feature_image: "images/siteLogo.png"
---


In this tutorial we will secure SSH by disabling root logon and
logon with a password. 

Prerequisites
=============

I will be using two Ubuntu vagrant machines however this should
work on most systems with SSH. Keep in mind the paths and files
may differ across different systems.

Step 1: Create RSA Keys
=======================

The first step is to generate the RSA key pair.

```bash
ssh-keygen -t rsa
```

You will be asked where to keep the keys and for a passphrase.
I recommend leaving the path to the files the default path. As
for the passphrase it does add extra security as well as an
extra step.

Step 2: Give Remote Host Public RSA Key
=======================================

We want the remote host to be able to use our key so we need to
give it the public RSA key we just created. If you changed the
file path go there for your keys other wise they are located at
**\~/.ssh/id_rsa.pub**.

Copy the contents of this file and add it to the remote hosts
authorized_keys. Below is a simple shell command to do that for
you. Just remember to change the username and IP address of the
remote machine you are adding the key to.

Example

```bash
cat ~/.ssh/id_rsa.pub | ssh user@192.168.0.1 "mkdir -p ~/.ssh && cat >>  ~/.ssh/authorized_keys"
```

Remember to change `user` and `192.168.0.1` to the username and
IP address of your remove machine.

Step 3: Verify
==============

Make sure everything is working. Try to log on. It should
connect with out asking for a password.

Step 4: Disable Password logon
==============================

With SSH RSA keys working the next step is to disable password
logon. To do that we will edit /etc/ssh/sshd_config. Locate and
change `PasswordAuthentication` to no

For example:

From:

```
PasswordAuthentication yes 
```

To:

```
PasswordAuthentication no
```


I recommend disabling root logon as well. To do that locate and
change PermitRootLogin to no.

For example:

Form:

```
PrmitRootLogin yes
```

To:

```
PermitRootLogin no  
```


Summary
=======

The purpose of this was to secure a system by enabling SSH RSA
Keys and disable root login as well as password logon.

This is just one step in securing a system. However a bunch of
little steps add up and makes a big difference.

Next I recommend [securing sudo](securing-sudo).
