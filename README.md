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
exploit  hen  jkpatch  mira  ps4-payload-sdk
```

### exploit

Contains Cryptogenic's browser exploit (https://github.com/justanormaldev/ps4-homebrew-vagrant/blob/master/Vagrantfile#L70).

The vagrant box has been configured to use the hen and mira from the original source when running this exploit (https://github.com/justanormaldev/ps4-homebrew-vagrant/blob/master/Vagrantfile#L72, https://github.com/justanormaldev/ps4-homebrew-vagrant/blob/master/Vagrantfile#L73)

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

Contains xvortex's homebrew enabler (aka hen) (https://github.com/justanormaldev/ps4-homebrew-vagrant/blob/master/Vagrantfile#L39).

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

Contains withmetta's fork of the xemio's jkpatch (it is the one that seems to work properly for 5.05) (https://github.com/justanormaldev/ps4-homebrew-vagrant/blob/master/Vagrantfile#L61).

#### send payload

If you're running the exploit and is `Awaiting Payload`, you can send it by running the `send.sh` script inside this directory.
You can specify the PS4 IP using the environment variable `PS4IP`.

```
vagrant@ubuntu-xenial:~$ PS4IP=192.168.1.107 jkpatch/send.sh
```

### mira

Contains The Mira Project from OpenOrbis (https://github.com/justanormaldev/ps4-homebrew-vagrant/blob/master/Vagrantfile#L47).

#### send payload

If you're running the exploit and is `Awaiting Payload`, you can send it by running the `send.sh` script inside this directory.
You can specify the PS4 IP using the environment variable `PS4IP`.

```
vagrant@ubuntu-xenial:~$ PS4IP=192.168.1.107 mira/send.sh
```
