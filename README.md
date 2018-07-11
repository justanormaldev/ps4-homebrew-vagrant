# ps4-homebrew-vagrant

A vagrant box for PS4 homebrew execution and development

## Currently only for 5.05

## What this vagrantfile is intended to do

- setup dependencies and build from source code on provision (minimum effort to get started)
- serve as executable instructions on how to build the different projects
- add utils to simplify usage

## To get it running

- install vagrant (more info: https://www.vagrantup.com/intro/getting-started/install.html)
- get the latest `Vagrantfile` and any potentially needed files (e.g. by cloning this repo, more info: https://help.github.com/articles/cloning-a-repository/)
- run `vagrant up` and then `vagrant ssh` (more info: https://www.vagrantup.com/intro/getting-started/up.html)

## To update to latest

Run `vagrant destroy -f`to remove everything from the previous version (more info: https://www.vagrantup.com/intro/getting-started/teardown.html) other than what you placed on the `/vagrant` path (more info: https://www.vagrantup.com/intro/getting-started/synced_folders.html).

Then you can follow the same steps as in the [To get it running](#to-get-it-running) section.

## What you can do with this

After you've got it running and are inside an ssh session to it, you will be able to see the following directories in the home path:

```
vagrant@ubuntu-xenial:~$ ls
exploit  hen  jkpatch  liborbis  mira  ps4dev  ps4link  ps4-payload-sdk  ps4sdk
```

### liborbis

Contains orbisdev's libraries and samples.

#### to run modplayer sample

- install and start [the orbislink pkg](https://github.com/justanormaldev/ps4-homebrew-vagrant/raw/148ce861059ea235d4215ef7ffe56b5368b55d8a/orbislink.pkg) in your PS4.
- inside a ssh session to the vagrant box, run:
```
vagrant@ubuntu-xenial:~$ cd ~/liborbis/samples/modplayer/bin/
vagrant@ubuntu-xenial:~/liborbis/samples/modplayer/bin$ ls
homebrew.elf  zweifeld.mod
vagrant@ubuntu-xenial:~/liborbis/samples/modplayer/bin$ ps4sh
ps4sh version 1.0
/home/vagrant/.ps4shrc: No such file or directory

log: [HOST][INFO]: [PS4SH] Ready
ps4sh> connect
log: [HOST][INFO]: [PS4SH] Connecting to fio ps4link ip 192.168.1.107
log: [HOST][INFO]: [PS4SH] PlayStation is listening at 192.168.1.107
log: [HOST][DEBUG]: [PS4SH] Opening homebrew.elf flags 0
log: [HOST][DEBUG]: [PS4SH] Open return 7
log: [HOST][DEBUG]: [PS4SH] 659552 result of lseek 0 offset 2 whence
log: [HOST][DEBUG]: [PS4SH] 0 result of lseek 0 offset 0 whence
log: [HOST][DEBUG]: [PS4SH] Opening zweifeld.mod flags 0
log: [HOST][DEBUG]: [PS4SH] Open return 7
log: [HOST][DEBUG]: [PS4SH] 34148 result of lseek 0 offset 2 whence
log: [HOST][DEBUG]: [PS4SH] 0 result of lseek 0 offset 0 whence
log: [HOST][DEBUG]: [PS4SH] read 34148 bytes of file descritor 7
ps4sh>
```

If you wish to experiment with the source:
- edit `~/liborbis/samples/modplayer/source/main.c`
- rebuild by running `cd ~/liborbis/samples/modplayer; make`

### exploit

Contains Cryptogenic's browser exploit.

The vagrant box has been configured to use the hen and mira from the original source when running this exploit.

#### start server

You can start an HTTP server for it by running the `serve.sh` script inside this directory. The vagrant box is configured to use port 8080 on the same IP as the host machine
(e.g: if the computer you're using has the IP `192.168.0.2` and the PS4 is on the same network, you can type in the PS4 browser: `http://192.168.0.2:8080` to access it).

```
vagrant@ubuntu-xenial:~$ exploit/serve.sh
~/exploit ~
Serving HTTP on 0.0.0.0 port 8080 ...
10.0.2.2 - - [05/Jul/2018 22:07:30] "GET / HTTP/1.1" 200 -
10.0.2.2 - - [05/Jul/2018 22:07:30] "GET /rop.js HTTP/1.1" 200 -
10.0.2.2 - - [05/Jul/2018 22:07:30] "GET /syscalls.js HTTP/1.1" 200 -
10.0.2.2 - - [05/Jul/2018 22:07:30] "GET /expl.js HTTP/1.1" 200 -
10.0.2.2 - - [05/Jul/2018 22:07:30] "GET /userland.js HTTP/1.1" 200 -
10.0.2.2 - - [05/Jul/2018 22:07:30] "GET /homebrew.js HTTP/1.1" 200 -
10.0.2.2 - - [05/Jul/2018 22:07:30] "GET /mira.js HTTP/1.1" 200 -
```

### hen

Contains xvortex's homebrew enabler (aka hen).

#### start server

You can start an HTTP server for it by running the `serve.sh` script inside this directory. The vagrant box is configured to use port 8080 on the same IP as the host machine
(e.g: if the computer you're using has the IP `192.168.0.2` and the PS4 is on the same network, you can type in the PS4 browser: `http://192.168.0.2:8080` to access it).

```
vagrant@ubuntu-xenial:~$ hen/serve.sh
~/hen/exploit ~
Serving HTTP on 0.0.0.0 port 8080 ...
10.0.2.2 - - [05/Jul/2018 22:17:10] "GET / HTTP/1.1" 200 -
```

#### send payload

If you're running the exploit and is `Awaiting Payload`, you can send it by running the `send.sh` script inside this directory.
You can specify the PS4 IP using the environment variable `PS4IP`.

```
vagrant@ubuntu-xenial:~$ PS4IP=192.168.1.107 hen/send.sh
```

### jkpatch

Contains withmetta's fork of the xemio's jkpatch (it is the one that seems to work properly for 5.05).

#### send payload

If you're running the exploit and is `Awaiting Payload`, you can send it by running the `send.sh` script inside this directory.
You can specify the PS4 IP using the environment variable `PS4IP`.

```
vagrant@ubuntu-xenial:~$ PS4IP=192.168.1.107 jkpatch/send.sh
```

### mira

Contains The Mira Project from OpenOrbis.

#### send payload

If you're running the exploit and is `Awaiting Payload`, you can send it by running the `send.sh` script inside this directory.
You can specify the PS4 IP using the environment variable `PS4IP`.

```
vagrant@ubuntu-xenial:~$ PS4IP=192.168.1.107 mira/send.sh
```
