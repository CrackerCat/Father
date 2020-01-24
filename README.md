# father

## Overview

***father*** is a short LD_PRELOAD rootkit for Linux. It's designed to be used in a competition environment, and has various standard features:</br>

* Network hiding
* File hiding
* Process hiding
* Local privilege escalation
* Remote accept() hook backdoor
* Time/logic bomb component
* GnuPG signature interception
* Anti-detection

## Installation

To install father, download the source code and change the configuration options to reflect your desired values. You can set the INSTALL_LOCATION to a file with the STRING prefix to hide the kit on disk.

To compile the kit you'll need to download libgcrypt on your computer. The dynamic linker will resolve all libgcrypt calls (like from GnuPG) to our dynamic library.


## Operation

### Priv-Esc 

To escalate privileges, just run a setuid program like *sudo* or *gpasswd* from the command prompt with your specified environment variable set. While in the shell you'll possess your magic GID and rootkit functions will be disabled, giving you unrestricted access to the system. Any processes spawned will be hidden from utilities like ps. This should work for most binaries.

```bash
$ father=a gpasswd

Enjoy the shell!

root@sectorv:~# 
```

### accept() backdoor

To use the accept backdoor, connect to a listening TCP socket on the system from the defined source port. If everything is working you'll be prompted to authenticate with your password and on complete will be presented with a bind shell.  It will inherit the permissions of the running process, and if possible hide itself from the process list. This behavior can be changed to a reverse shell over the hidden port by uncommenting the relevant code block in the source.

```bash
root@kali:~# ncat $IP 22 -p $SOURCEPORT 


AUTHENTICATE: father

```

### GnuPG Signature Tampering

This is very easy to implement, but meant moreso as a proof of concept. Since GnuPG is a dynamically linked program, we can intercept the calls it makes to its own library libgcrypt and change the return values. If you load the kit and then run any libgcrypt signature verification you'll receive a succcessful result, regardless of file or signature content. In theory this can be expanded to backdoor other operations like key reading and generation, or encryption/decryption.


### remove_preload.asm

remove_preload.asm is a short assembly program that unlinks /etc/ld.so.preload. The kit can be removed from the backdoor shell, but this provides a smaller and more easily scripted way to do so. It can be run in a loop by a blue team to prevent installation of most LD_PRELOAD based malware.

### IOCs

* ssdeep: 192:RGXLaAazXX84f+4VrDS5hsj0e6kltuMjVewpRHOi//IS1Y7Eawnn5HRh:ARazXVf+YrnHjewpcm/z1YRwT

* SHA256: 5c59bf25b35473517e345f7827c68a46230f6b86bee9cb51aec39167e306f1db

* SHA1: 5c59bf25b35473517e345f7827c68a46230f6b86bee9cb51aec39167e306f1db

* MD5: be932463c4028348a664106539ec907e
