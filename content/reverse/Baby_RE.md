---
title: "Baby_RE"
date: 2020-08-13T20:40:21+05:30
draft: false
---

## Baby_RE

**We are going to do reverse engineering to get a Flag from the given Executable**

Download the file from the challege tab in Challenge > Reversing

Download link : [Baby_RE](https://www.hackthebox.eu/home/challenges/download/92 "title text!").

Password: `hackthebox`

Lets check what kind of file it is first.

```toml
root@natzsec:~/Desktop/htb/challenges/reverse# file baby 
baby: ELF 64-bit LSB shared object, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, BuildID[sha1]=25adc53b89f781335a27bf1b81f5c4cb74581022, for GNU/Linux 3.2.0, not stripped
```

{{< figure src="/images/babyre1.png" caption="Baby_RE, \"Details of file using file Utility\" by natzsec" >}}

It says it is a `ELF 64` bit application file.

Now we know what kind of file type is, so we can now check if there are any readable strings in this executable.

we can use the `strings` utility against the file.

If we take a look at the strings there are intresting things in it.

```toml
root@natzsec:~/Desktop/htb/challenges/reverse# strings baby 
/lib64/ld-linux-x86-64.so.2
mgUa
libc.so.6
puts
stdin
fgets
__cxa_finalize
strcmp
__libc_start_main
GLIBC_2.2.5
_ITM_deregisterTMCloneTable
__gmon_start__
_ITM_registerTMCloneTable
u/UH
HTB{B4BYH
_R3V_TH4H
TS_Ef
[]A\A]A^A_
Dont run `strings` on this challenge, that is not the way!!!!
Insert key: 
abcde122313
Try again later.
;*3$"
GCC: (Debian 9.2.1-8) 9.2.1 20190909
crtstuff.c
deregister_tm_clones
__do_global_dtors_aux
completed.7444
__do_global_dtors_aux_fini_array_entry
frame_dummy
__frame_dummy_init_array_entry
baby.c
```

{{< figure src="/images/babyre2.png" caption="Baby_RE, \"Listing strings using Strings Utility\" by natzsec" >}}

We can notice that the it shows the flag too with `HTB{HTB{B4BYH
_R3V_TH4H`

But since it does not have the ending curly brackets not sure if thats the end of it or not, so moving forward we can see it is mentioned "Dont run `strings` on this challenge, that is not the way!!!!"

Just below it there is a string saying `Insert Key`, and there is also a string which is given right below it `abcde122313`

Let's try to run the program and see if we are getting the `Insert Key:` string.

When we executed, it asked for a Insert Key if enter any wrong string it says `Try again later`

If we compare the `Try again later` string in our strings output we can see its having a string above it which the program might be comparing to.

If we enter the string `abcde122313` it spits out the flag as we see in the below output.

```toml
root@natzsec:~/Desktop/htb/challenges/reverse# ./baby 
Insert key: 
abcde122313
HTB{B4BY_R3V_TH4TS_EZ}
root@natzsec:~/Desktop/htb/challenges/reverse# 
```

{{< figure src="/images/babyreflag.png" caption="Baby_RE, \"Flag using Strings\" by natzsec" >}}

As in this case Strings utility has played a major role in getting our flag. But this is not going to happen every time since many executable will be obsfucated to make it harder for the analysis.

Let's take another way to get the flag using IDA PRO.

Load the executable file into your IDA Pro.

{{< figure src="/images/Ida.png" caption="Baby_RE, \"Loading Baby_RE in IDA\" by natzsec" >}}

Now just do not get confused, just click on `OK` with the default options.

{{< figure src="/images/mainfunc.png" caption="Baby_RE, \"main function in IDA\" by natzsec" >}}

So in the main function window, which is showing in the graph view.

You can see that the `ASCII` characters of the input/output of the program, such as `Insert Key`

{{< figure src="/images/ascii.png" caption="Baby_RE, \"main function in IDA\" by natzsec" >}}

We can see that there is `_strcmp` call which is possibily comparing the string which we are entering with the `abcde122313`

We can also see that the `JNZ` to a location which is showing the string `Try Again Later` This makes sense as if we enter the input as incorrect we will be getting `Try Again Later`.

We can also view the hex view in the IDA for the same main function, it will show the flag details also.

{{< figure src="/images/hex.png" caption="Baby_RE, \"main function Hex in IDA\" by natzsec" >}}

So here, we saw that we can use two different ways to get the flag.

I hope you have enjoyed the Reversing this executable as much as I did writing my first blog.
