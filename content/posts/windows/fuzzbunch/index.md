---
title: "Fuzzbunch"
date: 2019-03-12T20:33:58-05:00
draft: false
feature_image: "/images/baby.jpg"
---

Fuzzbunch is a framework like Metasploit however it was written for
Windows XP and who wants to use that. This tutorial will cover how to
get it up and running on a Debian based distro.

Prerequisites
=============

-   Debian based distro

-   Wine

-   fuzzbunch-debian

Step 1:
=======

The first thing we need to do is install Wine and clone
fuzzbunch-debian.

As always we begin with an update.

```bash
apt update
```

Install Wine and other Wine tools.

```bash
apt install wine winbind winetricks
```

Add x32 architecture, update, and install wine 23

```bash
dpkg --add-architecture i386 && apt-get update && apt-get install wine32
```

Add variable.

```bash
WINEPREFIX="$HOME/.wine"
```

Set Wine Architecture.

```bash
WINEARCH=win32 wine wineboot
```

Add Path to bashrc

```bash
echo "export WINEPREFIX=$HOME/.wine-fuzzbunch" >> ~/.bashrc
```

Change into the Wine directory.

```bash
cd $HOME/.wine-fuzzbunch/drive_c
```

Clone Fuzzbunch-Debian.

```bash
git clone https://github.com/mdiazcl/fuzzbunch-debian.git
```

Install Python26

```bash
winetricks python26
```

Step 2:
=======

With Wine and fuzzbunch-debian installed its time to fire it up.

Change directories into fuzzbunch-debian/windows where fb.py is located.

```bash
cd $HOME/.wine-fuzzbunch/drive_c/fuzzbunch-debian/windows
```

Open up a Windows command prompt.

```bash
wine cmd.exe
```

We need to add Python26 and fuzzbunch-debian to Windows path.

```bash
path C:\Python26;C:\fuzzbunch-debian\windows\fuzzbunch;C:\Windows; C:\Windows\system32;C:\Windows\system32\webm
```

Start up fuzzbunch.

```bash
python fb.py
```

To use Eternalblue and Douplepulsar.

```bash
use EternalBlue
```

```bash
use DoublePulsar
```

References
==========

<https://github.com/mdiazcl/fuzzbunch-debian>

<https://www.exploit-db.com/exploits/41891/>


<https://github.com/ElevenPaths/Eternalblue-Doublepulsar-Metasploit>

<!--
https://www.youtube.com/watch?v=mJWMqLF00fM
https://www.youtube.com/watch?v=wDAkiXxm1gE
-->
