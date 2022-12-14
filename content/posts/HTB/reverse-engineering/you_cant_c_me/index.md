---
title: "You Can't C Me"
date: 2022-01-06T20:33:58-05:00
draft: false
feature_image: "images/see.jpg"
---

![see](images/see.jpg)

# Hack the Box Reverse Engineering: You Can&rsquo;t C Me

I start by looking at the file.

```bash
file auth | tr "," "\n"
```

I then opened it up in ghidra and looked at the functions. The functions where
obfuscated however, I was able to find what appears to be the main function. Note
that I did rename the function to main and I also rename a variable to userIn for
reasons to be discussed later.

```bash
undefined4 main(void)
    
{
    int iVar1;
    undefined8 local_48;
    undefined8 local_40;
    undefined4 local_38;
    char *userIn;
    undefined8 local_28;
    undefined8 local_20;
    undefined4 local_18;
    undefined local_14;
    int local_10;
    undefined4 local_c;
        
    local_c = 0;
    local_10 = 0;
    printf("Welcome!\n");
    local_28 = 0x5f73695f73696874;
    local_20 = 0x737361705f656874;
    local_18 = 0x64726f77;
    local_14 = 0;
    userIn = (char *)malloc(0x15);
    local_48 = 0x5517696626265e6d;
    local_40 = 0x555a275a556b266f;
    local_38 = 0x29635559;
    for (local_10 = 0; local_10 < 0x14; local_10 = local_10 + 1) {
        *(char *)((long)&local_28 + (long)local_10) = *(char *)((long)&local_48 + (long)local_10) + '\n'
        ;
    }
    fgets(userIn,0x15,stdin);
    iVar1 = strcmp((char *)&local_28,userIn);
    if (iVar1 == 0) {
        printf("HTB{%s}\n",userIn);
    }
    else {
        printf("I said, you can\'t c me!\n");
    }
    return local_c;
}
```

Here we can see that if iVar1 is equal to 0, then it prints out the flag, but
what is iVar1? Well, like in baby-re there is a string comparison, that checks
if the user input is equal to local<sub>28</sub>. If it is, then it prints out the flag.
if it is not it prints &ldquo;I said, you can&rsquo;t c me!&rdquo;. Honestly, I had a lot of trouble
with this one, I eventually googled 0x5f73695f73696874 which led me to this blog
post <https://nxtdaemon.xyz/htb-you-cant-c-me-reversing/> spoiler alert, the answer
is there. However, I wasn&rsquo;t looking for the answer I was looking for a clue and
I did find it in ltrace. The man page for ltrace has a lot however one thing stood
out to me

```bash
-S     Display system calls as well as library calls
```

I gave this a try.

```bash
$ ltrace -S ./auth
SYS_brk(0)                                                                                                                                         = 0x2127000
SYS_access("/etc/ld.so.preload", 04)                                                                                                               = -2
SYS_openat(0xffffff9c, 0x7f43a2469208, 0x80000, 0)                                                                                                 = 3
SYS_newfstatat(3, 0x7f43a24689f5, 0x7ffcafafee20, 4096)                                                                                            = 0
SYS_mmap(0, 0x1198f, 1, 2)                                                                                                                         = 0x7f43a2430000
SYS_close(3)                                                                                                                                       = 0
SYS_openat(0xffffff9c, 0x7f43a2474f40, 0x80000, 0)                                                                                                 = 3
SYS_read(3, "\177ELF\002\001\001\003", 832)                                                                                                        = 832
SYS_pread(3, 0x7ffcafafeb80, 784, 64)                                                                                                              = 784
SYS_pread(3, 0x7ffcafafeb50, 32, 848)                                                                                                              = 32
SYS_pread(3, 0x7ffcafafeb00, 68, 880)                                                                                                              = 68
SYS_newfstatat(3, 0x7f43a24689f5, 0x7ffcafafee20, 4096)                                                                                            = 0
SYS_mmap(0, 8192, 3, 34)                                                                                                                           = 0x7f43a242e000
SYS_pread(3, 0x7ffcafafea70, 784, 64)                                                                                                              = 784
SYS_mmap(0, 0x1c8378, 1, 2050)                                                                                                                     = 0x7f43a2265000
SYS_mprotect(0x7f43a228b000, 1654784, 0)                                                                                                           = 0
SYS_mmap(0x7f43a228b000, 0x148000, 5, 2066)                                                                                                        = 0x7f43a228b000
SYS_mmap(0x7f43a23d3000, 0x4b000, 1, 2066)                                                                                                         = 0x7f43a23d3000
SYS_mmap(0x7f43a241f000, 0x6000, 3, 2066)                                                                                                          = 0x7f43a241f000
SYS_mmap(0x7f43a2425000, 0x8378, 3, 50)                                                                                                            = 0x7f43a2425000
SYS_close(3)                                                                                                                                       = 0
SYS_mmap(0, 8192, 3, 34)                                                                                                                           = 0x7f43a2263000
SYS_arch_prctl(4098, 0x7f43a242f580, 0x7f43a2265000, 34)                                                                                           = 0
SYS_mprotect(0x7f43a241f000, 12288, 1)                                                                                                             = 0
SYS_mprotect(0x403000, 4096, 1)                                                                                                                    = 0
SYS_mprotect(0x7f43a2471000, 8192, 1)                                                                                                              = 0
SYS_munmap(0x7f43a2430000, 72079)                                                                                                                  = 0
printf("Welcome!\n" <unfinished ...>
SYS_newfstatat(1, 0x7f43a23ed75a, 0x7ffcafaff490, 4096)                                                                                            = 0
SYS_brk(0)                                                                                                                                         = 0x2127000
SYS_brk(0x2148000)                                                                                                                                 = 0x2148000
SYS_write(1, "Welcome!\n", 9Welcome!
)                                                                                                                      = 9
<... printf resumed> )                                                                                                                             = 9
malloc(21)                                                                                                                                         = 0x21276b0
fgets( <unfinished ...>
SYS_newfstatat(0, 0x7f43a23ed75a, 0x7ffcafaffa20, 4096)                                                                                            = 0
SYS_read(0
```

Here the executable is waiting for some input. I just hit enter.

```bash
, "\n", 1024)                                                                                                                            = 1
<... fgets resumed> "\n", 21, 0x7fa84d3039a0)                                                                                                      = 0x1dd96b0
strcmp("wh00ps!_y0u_d1d_c_m3", "\n")                                                                                                               = 109
printf("I said, you can't c me!\n" <unfinished ...>
SYS_write(1, "I said, you can't c me!\n", 24I said, you can't c me!
)                                                                                                      = 24
<... printf resumed> )                                                                                                                             = 24
SYS_exit_group(0 <no return ...>
+++ exited (status 0) +++
```

This is really cool and we can see the function and system calls. One that we 
want check out is the strcmp function. With ltrace we see that it compairs
&ldquo;wh00ps!<sub>y0u</sub><sub>d1d</sub><sub>c</sub><sub>m3</sub>&rdquo; to the input that I entered &ldquo;\n&rdquo;.  Naturaly this does not
match, but it does show what it wants to match &ldquo;wh00ps!<sub>y0u</sub><sub>d1d</sub><sub>c</sub><sub>m3</sub>&rdquo;. If we 
run this again but this time we enter &ldquo;wh00ps!<sub>y0u</sub><sub>d1d</sub><sub>c</sub><sub>m3</sub>&rdquo; we get the following

```bash
$ ltrace -S ./auth
SYS_brk(0)                                                                                                                                         = 0x2127000
SYS_access("/etc/ld.so.preload", 04)                                                                                                               = -2
SYS_openat(0xffffff9c, 0x7f43a2469208, 0x80000, 0)                                                                                                 = 3
SYS_newfstatat(3, 0x7f43a24689f5, 0x7ffcafafee20, 4096)                                                                                            = 0
SYS_mmap(0, 0x1198f, 1, 2)                                                                                                                         = 0x7f43a2430000
SYS_close(3)                                                                                                                                       = 0
SYS_openat(0xffffff9c, 0x7f43a2474f40, 0x80000, 0)                                                                                                 = 3
SYS_read(3, "\177ELF\002\001\001\003", 832)                                                                                                        = 832
SYS_pread(3, 0x7ffcafafeb80, 784, 64)                                                                                                              = 784
SYS_pread(3, 0x7ffcafafeb50, 32, 848)                                                                                                              = 32
SYS_pread(3, 0x7ffcafafeb00, 68, 880)                                                                                                              = 68
SYS_newfstatat(3, 0x7f43a24689f5, 0x7ffcafafee20, 4096)                                                                                            = 0
SYS_mmap(0, 8192, 3, 34)                                                                                                                           = 0x7f43a242e000
SYS_pread(3, 0x7ffcafafea70, 784, 64)                                                                                                              = 784
SYS_mmap(0, 0x1c8378, 1, 2050)                                                                                                                     = 0x7f43a2265000
SYS_mprotect(0x7f43a228b000, 1654784, 0)                                                                                                           = 0
SYS_mmap(0x7f43a228b000, 0x148000, 5, 2066)                                                                                                        = 0x7f43a228b000
SYS_mmap(0x7f43a23d3000, 0x4b000, 1, 2066)                                                                                                         = 0x7f43a23d3000
SYS_mmap(0x7f43a241f000, 0x6000, 3, 2066)                                                                                                          = 0x7f43a241f000
SYS_mmap(0x7f43a2425000, 0x8378, 3, 50)                                                                                                            = 0x7f43a2425000
SYS_close(3)                                                                                                                                       = 0
SYS_mmap(0, 8192, 3, 34)                                                                                                                           = 0x7f43a2263000
SYS_arch_prctl(4098, 0x7f43a242f580, 0x7f43a2265000, 34)                                                                                           = 0
SYS_mprotect(0x7f43a241f000, 12288, 1)                                                                                                             = 0
SYS_mprotect(0x403000, 4096, 1)                                                                                                                    = 0
SYS_mprotect(0x7f43a2471000, 8192, 1)                                                                                                              = 0
SYS_munmap(0x7f43a2430000, 72079)                                                                                                                  = 0
printf("Welcome!\n" <unfinished ...>
SYS_newfstatat(1, 0x7f43a23ed75a, 0x7ffcafaff490, 4096)                                                                                            = 0
SYS_brk(0)                                                                                                                                         = 0x2127000
SYS_brk(0x2148000)                                                                                                                                 = 0x2148000
SYS_write(1, "Welcome!\n", 9Welcome!
)                                                                                                                      = 9
<... printf resumed> )                                                                                                                             = 9
malloc(21)                                                                                                                                         = 0x21276b0
fgets( <unfinished ...>
SYS_newfstatat(0, 0x7f43a23ed75a, 0x7ffcafaffa20, 4096)                                                                                            = 0
SYS_read(0wh00ps!_y0u_d1d_c_m3
, "wh00ps!_y0u_d1d_c_m3\n", 1024)                                                                                                        = 21
<... fgets resumed> "wh00ps!_y0u_d1d_c_m3", 21, 0x7f43a24229a0)                                                                                    = 0x21276b0
strcmp("wh00ps!_y0u_d1d_c_m3", "wh00ps!_y0u_d1d_c_m3")                                                                                             = 0
printf("HTB{%s}\n", "wh00ps!_y0u_d1d_c_m3" <unfinished ...>
SYS_write(1, "HTB{wh00ps!_y0u_d1d_c_m3}\n", 26HTB{wh00ps!_y0u_d1d_c_m3}
)                                                                                                    = 26
<... printf resumed> )                                                                                                                             = 26
SYS_lseek(0, -1, 1)                                                                                                                                = -29
SYS_exit_group(0 <no return ...>
+++ exited (status 0) +++
```


Here we see that strcmp(&ldquo;wh00ps!<sub>y0u</sub><sub>d1d</sub><sub>c</sub><sub>m3</sub>&rdquo;, &ldquo;wh00ps!<sub>y0u</sub><sub>d1d</sub><sub>c</sub><sub>m3</sub>&rdquo;) is 0, 
and we then see the syscall to write the flag to stdout SYS<sub>write</sub>(1, &ldquo;HTB{wh00ps!<sub>y0u</sub><sub>d1d</sub><sub>c</sub><sub>m3</sub>}\n&rdquo;, 26HTB{wh00ps!<sub>y0u</sub><sub>d1d</sub><sub>c</sub><sub>m3</sub>}).
There is our flag!

Try it yourself by downloading [auth](auth) here.
