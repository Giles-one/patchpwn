# patchpwn - A shell script to replace libc of pwn binary in CTF

## Abstract

This simple shell script is based [patchelf](https://github.com/NixOS/patchelf) and [glibc-all-in-one](https://github.com/matrix1001/glibc-all-in-one) . So , using patchpwn , you need to ensure that you have install them and they are in normal use . 

## Usage

Before specifying the libc version , you should download the libc via glibc-all-in-one, then edit this shell script and replace the LIBC_DIR variable  to adapt your local environment.After patching the binary ,it will generate a new binary file with the `_patched` suffix and no change to the original binary file.

```Powershell
LIBC_DIR="/home/giles/tools/glibc-all-in-one/"
# LIBC_DIR="/dir/to/your/glibc-all-in-one/"
```

After gitiing this project , add this to $PATH should be better.
```Powershell
giles@ubuntu:~/topatchelf $ chmod +x ./patchpwn 
giles@ubuntu:~/topatchelf $ sudo cp ./patchpwn /bin/
```
Currently,there are two patterns to specify the libc version.

### Specify the os


```Powershell
giles@ubuntu:~ $ patchpwn ubuntu18 ./pwn
giles@ubuntu:~ $ ldd ./pwn
	linux-vdso.so.1 (0x00007ffee81fb000)
	libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007fbd2347c000)
	/lib64/ld-linux-x86-64.so.2 (0x00007fbd23688000)
giles@ubuntu:~ $ ldd ./pwn_patched 
	linux-vdso.so.1 (0x00007ffe429d0000)
	/home/giles/tools/glibc-all-in-one/libs/2.27-3ubuntu1.2_amd64/libc.so.6 (0x00007fc4f07ad000)
	/home/giles/tools/glibc-all-in-one/libs/2.27-3ubuntu1.2_amd64/ld-linux-x86-64.so.2 => /lib64/ld-linux-x86-64.so.2 (0x00007fc4f0ba7000)
```

As you can see, the default patch aims at x86 libc. And you can add the suffix `32` to specify i386 version.

```Powershell
giles@ubuntu:~ $ patchpwn ubuntu18 ./pwn32 32
giles@ubuntu:~ $ ls
pwn  pwn32  pwn32_patched  pwn_patched  test.c
```
Unfortunately, only ubuntu16,ubuntu18,ubuntu20 are currently supported.

And also you change the default version of every supportive os in this shell script.

```Powershell
# the default is x86 glibc
DEFAULT_ubuntu16_04="2.23-0ubuntu11.3_amd64"
DEFAULT_ubuntu18_04="2.27-3ubuntu1.2_amd64"
DEFAULT_ubuntu20_04="2.31-0ubuntu9.2_amd64" 

DEFAULT_ubuntu16_32="2.23-0ubuntu11.3_i386"
DEFAULT_ubuntu18_32="2.27-3ubuntu1.2_i386"
DEFAULT_ubuntu20_32="2.31-0ubuntu9.2_i386"
```
### Specify the specific version

```Powershell
giles@ubuntu:~ $ patchpwn --help

...
Your Libc:
[+] 2.23-0ubuntu11.3_amd64
[+] 2.23-0ubuntu11.3_i386
[+] 2.23-0ubuntu3_amd64
[+] 2.23-0ubuntu3_i386
[+] 2.24-3ubuntu2.2_amd64
[+] 2.27-3ubuntu1.2_amd64
[+] 2.27-3ubuntu1.2_i386
[+] 2.27-3ubuntu1_amd64
[+] 2.27-3ubuntu1_i386
[+] 2.31-0ubuntu9.2_amd64
[+] 2.31-0ubuntu9.2_i386
giles@ubuntu:~ $ patchpwn 2.27-3ubuntu1_amd64 ./pwn
giles@ubuntu:~ $ ldd pwn_patched 
	linux-vdso.so.1 (0x00007fffbd9fe000)
	/home/giles/tools/glibc-all-in-one/libs/2.27-3ubuntu1_amd64/libc.so.6 (0x00007f58a7c85000)
	/home/giles/tools/glibc-all-in-one/libs/2.27-3ubuntu1_amd64/ld-linux-x86-64.so.2 => /lib64/ld-linux-x86-64.so.2 (0x00007f58a807f000)

```

The parameter `--help` help you inspect the available libc in the directory `/dir/to/your/glibc-all-in-one/libs/`. Also you can download more libc via glibc-all-in-one

## Application scene

```Powershell
giles@ubuntu:~/Desktop/GreentownNote/attachment $ strings libc-2.27.so | grep "GNU"
GNU C Library (Ubuntu GLIBC 2.27-3ubuntu1) stable release version 2.27.
Compiled by GNU CC version 7.3.0.
giles@ubuntu:~/Desktop/GreentownNote/attachment $ patchpwn 
...
Your Libc:
[+] 2.23-0ubuntu11.3_amd64
[+] 2.23-0ubuntu11.3_i386
[+] 2.23-0ubuntu3_amd64
[+] 2.23-0ubuntu3_i386
[+] 2.27-3ubuntu1.2_amd64
[+] 2.27-3ubuntu1.2_i386
[+] 2.27-3ubuntu1_amd64
[+] 2.27-3ubuntu1_i386
giles@ubuntu:~/Desktop/GreentownNote/attachment $ patchpwn 2.27-3ubuntu1_amd64 ./GreentownNote 
giles@ubuntu:~/Desktop/GreentownNote/attachment $ python exp.py
```
It can help to attack exit_hook(hook in ld)，and crack remote libc.




