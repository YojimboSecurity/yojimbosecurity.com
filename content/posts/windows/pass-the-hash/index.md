---
author:
- David Johnson
index: 'Windows!Servers!Pass the Hash'
setupfile: '\"../org-html-themes/org/theme-readtheorg-local-1.setup\"'
title: Pass The Hash
---

Detecting Pass-The-Ticket and Pass-The-Hash Attack Using Simple WMI Commands
============================================================================

Reading this I wanted to follow along but there was some things left
out. I like reproducability and wanted to reproduce the resaults.

This artical starts out realy well. The first few paragraphs go over the
issue I am having. PTH is really hard to detect how can we do a better
job to filter out the false positives? This guy seams to have figured
something out so I read on.

In the section titled **Technical PPT** there is a numbered list or
steps, things to do. It goes as follows...

Just kidding read the artical.

here is whats missing

the following is done in powershell

    gwmi Win32_LoggedOnUser

get a lot of this



    __GENUS          : 2
    __CLASS          : Win32_LoggedOnUser
    __SUPERCLASS     : CIM_Dependency
    __DYNASTY        : CIM_Dependency
    __RELPATH        : Win32_LoggedOnUser.Antecedent="\\\\.\\root\\cimv2:Win32_Account.Domain=\"WIN-2VCENS6CSCE\",Name=\"SY
                       STEM\"",Dependent="\\\\.\\root\\cimv2:Win32_LogonSession.LogonId=\"999\""

    gwmi Win32_LoggedOnUser| Select Antecedent

you get

    Antecedent
    ----------
    \\.\root\cimv2:Win32_Account.Domain="WIN-2VCENS6CSCE",Name="SYSTEM"
    \\.\root\cimv2:Win32_Account.Domain="WIN-2VCENS6CSCE",Name="SYSTEM"
    \\.\root\cimv2:Win32_Account.Domain="WIN-2VCENS6CSCE",Name="SYSTEM"
    \\.\root\cimv2:Win32_Account.Domain="WIN-2VCENS6CSCE",Name="SYSTEM"
    \\.\root\cimv2:Win32_Account.Domain="WIN-2VCENS6CSCE",Name="SYSTEM"
    \\.\root\cimv2:Win32_Account.Domain="WIN-2VCENS6CSCE",Name="SYSTEM"
    \\.\root\cimv2:Win32_Account.Domain="WIN-2VCENS6CSCE",Name="SYSTEM"
    \\.\root\cimv2:Win32_Account.Domain="WIN-2VCENS6CSCE",Name="SYSTEM"
    \\.\root\cimv2:Win32_Account.Domain="WIN-2VCENS6CSCE",Name="LOCAL SERVICE"
    \\.\root\cimv2:Win32_Account.Domain="WIN-2VCENS6CSCE",Name="NETWORK SERVICE"
    \\.\root\cimv2:Win32_Account.Domain="WINDOWS2012R2",Name="vagrant"
    \\.\root\cimv2:Win32_Account.Domain="WINDOWS2012R2",Name="vagrant"
    \\.\root\cimv2:Win32_Account.Domain="WINDOWS2012R2",Name="WIN2012$"
    \\.\root\cimv2:Win32_Account.Domain="WINDOWS2012R2",Name="Administrator"
    \\.\root\cimv2:Win32_Account.Domain="WINDOWS2012R2",Name="Administrator"
    \\.\root\cimv2:Win32_Account.Domain="WIN-2VCENS6CSCE",Name="ANONYMOUS LOGON"
    \\.\root\cimv2:Win32_Account.Domain="WIN-2VCENS6CSCE",Name="DWM-1"
    \\.\root\cimv2:Win32_Account.Domain="WIN-2VCENS6CSCE",Name="DWM-1"

    gwmi Win32_LoggedOnUser| Select Dependent

    Dependent
    ---------
    \\.\root\cimv2:Win32_LogonSession.LogonId="999"
    \\.\root\cimv2:Win32_LogonSession.LogonId="475876"
    \\.\root\cimv2:Win32_LogonSession.LogonId="232078"
    \\.\root\cimv2:Win32_LogonSession.LogonId="148982"
    \\.\root\cimv2:Win32_LogonSession.LogonId="232371"
    \\.\root\cimv2:Win32_LogonSession.LogonId="232309"
    \\.\root\cimv2:Win32_LogonSession.LogonId="232151"
    \\.\root\cimv2:Win32_LogonSession.LogonId="135911"
    \\.\root\cimv2:Win32_LogonSession.LogonId="997"
    \\.\root\cimv2:Win32_LogonSession.LogonId="996"
    \\.\root\cimv2:Win32_LogonSession.LogonId="572172"
    \\.\root\cimv2:Win32_LogonSession.LogonId="473323"
    \\.\root\cimv2:Win32_LogonSession.LogonId="537212"
    \\.\root\cimv2:Win32_LogonSession.LogonId="192345"
    \\.\root\cimv2:Win32_LogonSession.LogonId="523931"
    \\.\root\cimv2:Win32_LogonSession.LogonId="146862"
    \\.\root\cimv2:Win32_LogonSession.LogonId="69791"
    \\.\root\cimv2:Win32_LogonSession.LogonId="69722"

    gwmi Win32_LogonSession

a lot of

    AuthenticationPackage : Negotiate
    LogonId               : 69722
    LogonType             : 2
    Name                  :
    StartTime             : 20180718201202.422084-240
    Status                :

    gwmi Win32_LoggedOnUser| Select Dependent

you get

    Dependent
    ---------
    \\.\root\cimv2:Win32_LogonSession.LogonId="999"
    \\.\root\cimv2:Win32_LogonSession.LogonId="475876"
    \\.\root\cimv2:Win32_LogonSession.LogonId="232078"
    \\.\root\cimv2:Win32_LogonSession.LogonId="148982"
    \\.\root\cimv2:Win32_LogonSession.LogonId="232371"
    \\.\root\cimv2:Win32_LogonSession.LogonId="232309"
    \\.\root\cimv2:Win32_LogonSession.LogonId="232151"
    \\.\root\cimv2:Win32_LogonSession.LogonId="135911"
    \\.\root\cimv2:Win32_LogonSession.LogonId="997"
    \\.\root\cimv2:Win32_LogonSession.LogonId="996"
    \\.\root\cimv2:Win32_LogonSession.LogonId="572172"
    \\.\root\cimv2:Win32_LogonSession.LogonId="473323"
    \\.\root\cimv2:Win32_LogonSession.LogonId="537212"
    \\.\root\cimv2:Win32_LogonSession.LogonId="192345"
    \\.\root\cimv2:Win32_LogonSession.LogonId="523931"
    \\.\root\cimv2:Win32_LogonSession.LogonId="146862"
    \\.\root\cimv2:Win32_LogonSession.LogonId="69791"
    \\.\root\cimv2:Win32_LogonSession.LogonId="69722"

for PTH the author recommends \"detect any "Negotiation" logon sessions
that contains the Logon Type '9'\"

    gwmi Win32_LogonSession | Select AuthenticationPackage

    AuthenticationPackage
    ---------------------
    Negotiate
    Kerberos
    Kerberos
    Kerberos
    Kerberos
    Kerberos
    Kerberos
    Kerberos
    Negotiate
    Negotiate
    Kerberos
    NTLM
    Negotiate
    Negotiate

    gwmi Win32_LogonSession | Select LogonType

    LogonType
    ---------
            0
            3
            3
            3
            3
            3
            3
            3
            5
            5
            2
            3
            2
            2

``` {.example}
The- Hash attack will be executed via this method, and the only time that 
you’ll see Logon Type ‘9’ in “Negotiation” session will be if someone is 
using runas command with the “netonly” parameter, which is not something 
you see every day in normal environments, so the false positive rate in 
this method is very low.
```

simple enough

    iex (New-Object Net.WebClient).Downloadstring('https://raw.githubusercontent.com/JavelinNetworks/IR-Tools/master/Get-SessionAnomaly.ps1')

Footnote
--------
