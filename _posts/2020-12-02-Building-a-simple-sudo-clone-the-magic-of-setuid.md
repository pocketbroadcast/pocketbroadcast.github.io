---
layout: post
title: "Building a simple sudo (demo) clone - the magic of setuid"
date: 2020-12-02
categories: [blog]
excerpt_separator: <!--more-->
---

This post shows how we can build a simple sudo demo clone. Our version has really limited features, no security concept and should of course not be used anywhere at all!

## Theory

[man sudo][sudoman] states:

> **sudo** allows a permitted user to execute a command as the superuser or another user, as specified by the security policy.

This is achieved using the ```setuid``` or ```setgid``` flags.
These flags can be set as part of the executables permission. Following an sample given at [stackoverflow][sudointernals]:

```
$ ls -la `which sudo`
---s--x--x 1 root root 149080 Sep 23 16:59 /usr/bin/sudo
```
can be interpreted as follows:
```
-|--s|--x|--x
-      - first dash denotes if a directory or a file ("d" = dir, "-" = file)  
--s    - only the setuid bit is enabled for user who owns file
--x    - only the group execute bit is enabled
--x    - only the other execute bit is enabled
```

Thus, on execution of ```sudo``` the effective user id is set to the user id of the file's owner and the process is executed as it had been started by the file owner itself (in our case ```root```)

Summing up: The real uid is the uid of the user on whoms behalf the program was started and the effective id represents the user whose rights are used.


## Building the sudo demo clone

Our demo clone prints the real and effective user and group ids (inspired by [this post][simple-c-program]) used before executing a (sub-)program specified by its command line arguments:

```
#include <stdio.h>
#include <unistd.h>

int main (int argc, char* const *argv) {
  if(argc < 2)
    return 1;

  int ruid = getuid();
  int euid = geteuid();
  int rgid = getgid();
  int egid = getegid();

  printf("execute %s with real uid/gid: %d/%d and effecive uid/gid: %d/%d\n", argv[1], ruid, rgid, euid, egid);
  return execv(argv[1], argv+1);
}
```

I am running the following tests as user ```lx``` with uid ```1000```
```
$ whoami
lx
$ echo $UID
1000
```
on a developer machine running ```Ubuntu 18.04``` and ```gcc 7.5.0```.


Building our clone creates an elf-binary with the following permissions it: 
```
$ gcc my_sudo.c -o my_sudo
$ ls -la my_sudo
-rwxr-xr-x 1 lx lx 8528 Dec  2 18:33 my_sudo
```

## Running the demo clone with ```setuid``` bit unset

Since the setuid bit is not set by now (```-rwxr-xr-x```) the output of the program states that the real uid as well as effective uid used during execution is ```1000``` (user ```lx```):
```
$ ./my_sudo /bin/sleep 10
execute /bin/sleep with real uid/gid: 1000/1000 and effecive uid/gid: 1000/1000
```
and also ```/bin/sleep``` is executed as ```lx```
```
$ ps -aux | grep sleep
lx        14630  0.0  0.0  14788  1148 pts/0    S+   18:37   0:00 grep --color=auto sleep
```

## Running the demo clone with ```setuid``` bit set

After changing the owner of the file and setting the ```setuid``` bit
```
$ sudo chown root my_sudo
$ sudo chmod 4111 my_sudo
$ ls -la my_sudo
---s--x--x 1 root lx 8528 Dec  2 18:33 my_sudo
```
execution of the program states that the real uid is still ```1000``` but the effective uid used during execution is ```0``` (user ```root``` which owns the file):

```
$ ./my_sudo /bin/sleep 10
execute /bin/sleep with real uid/gid: 1000/1000 and effecive uid/gid: 0/1000
```

and also ```/bin/sleep``` is now executed as ```root```
```
$ ps -au | grep sleep
root      15149  0.0  0.0   7828   828 pts/1    S+   19:06   0:00 /bin/sleep 10
```


[sudoman]: https://linux.die.net/man/8/sudo
[sudointernals]: https://unix.stackexchange.com/questions/80344/how-do-the-internals-of-sudo-work
[simple-c-program]: https://www.theurbanpenguin.com/using-a-simple-c-program-to-explain-the-suid-permission/