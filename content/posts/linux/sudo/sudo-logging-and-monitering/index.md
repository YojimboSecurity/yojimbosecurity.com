---
title: "Sudo Logging and Monitoring"
date: 2017-07-26T20:33:58-05:00
draft: false
# feature_image: "images/siteLogo.png"
---

For Debian based systems

Basic Logging methods Log what sudo did (/var/log/auth.log)

Debugging (/var/log/sudo_debug)

Full session capture (/var/log/sudo-io)

With these three you can customize the level of logging needed for your
environment.

VISUDO You should always use visudo to edit the /etc/sudoers file. It
will make sure your changes are correct. It is possible to lock your
self out of the system. Visudo helps but if your changes are
syntactically and logically correct you can still get locked out. So use
caution and also use visudo to help catch mistakes.

Set the EDITOR environment variable to your preferred editor and visudo
will use it instead of the default.

Example

export EDITOR=/usr/bin/vim Configure Sudo Logging Configure logging in

/etc/sudo.conf

Sudo.conf logging is broken up into four parts.

Debug Program Log file location Subsystem and level Logging messages are
split into: Levels: measure of severity or priority

Subsystems: log activity from each subsystem

Levels/Priority

Debug trace info dialog notice warn err crit Subsystems

For Sudo:

args conv edit exec main pcomm plugin selinux utmp For Sudoers:

alias audit auth defaults env ldap logging match nss parser perms plugin
rbtree Both sudo and sudoers:

all netif pty util Example

Debug sudo /var/log/sudo_debug all\@notice Debug sudo applies to both
sudo and sudoers. There can only be one Debug statement per program or
plugin.

Sudoreplay Sudo can log the input and output, give it a timestamp and
display it exactly as is happened.

Default logging to:

/var/log/sudo-io

Logging options: log~output~ Enables output logging.

Warning do not log output from sudoreplay or reboot. To disable logging
for sudoreplay Defaults! /usr/bin/sudoreplay !log_output. To disable
logging for reboot Defaults! /sbin/reboot !log_output.

log_input Enables input logging.

Warning For those trying to protect your data you may not want to use
this. It may contain passwords and other sensitive information.

You can also log input/output per command with these flags.

LOG_INPUT LOG_OUTPUT NOLOG_INPUT NOLOG_OUTPUT Example

Add something like this to /etc/sudoers to log the output but not
sudoreplay or reboot.

Defaults log_output

Defaults! /usr/bin/sudoreplay !log_output

Defaults! /sbin/reboot !log_output With logging enabled you can now
replay what someone did as sudo. Run a command as sudo. For example sudo
ls. Then try sudoreplay -l as root or with sudo. You should see
something like this.

Oct 26 15:56:57 2017 : vagrant : TTY=/dev/pts/0 ; CWD=/var/log ;
USER=root ; TSID=000001 ; COMMAND=/bin/ls To replay in realtime
sudoreplay 000001

If a user was to use sudo su to switch to root, do something, then exit.
You could replay their entire session and see every command they
entered. Give it a try :)

References <https://www.sudo.ws/man/1.8.12/sudo.conf.man.html>

<https://www.sudo.ws/man/1.8.17/sudoers.man.html>
