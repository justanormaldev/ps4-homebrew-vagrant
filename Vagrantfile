# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/xenial64"

  config.vm.network "forwarded_port", guest: 8080, host: 8080

  config.vm.provision "shell", inline: <<-SHELL
export PS4IP=192.168.1.107
echo "PS4IP=${PS4IP}" >> /etc/environment

apt-get update

apt-get install -y language-pack-en
echo "LC_ALL=en_US.UTF-8" >> /etc/default/locale
echo "LANG=en_US.UTF-8" >> /etc/default/locale

apt-get install -y git

wget -O - https://apt.llvm.org/llvm-snapshot.gpg.key | sudo apt-key add -
apt-add-repository "deb https://apt.llvm.org/xenial/ llvm-toolchain-xenial-3.9 main"
apt -o Acquire::AllowInsecureRepositories=true update
apt-get install -y clang-3.9 lldb-3.9 binutils make gcc

git clone https://github.com/xvortex/ps4-payload-sdk.git /home/vagrant/ps4-payload-sdk
export PS4SDK=/home/vagrant/ps4-payload-sdk
echo "PS4SDK=${PS4SDK}" >> /etc/environment
cd /home/vagrant/ps4-payload-sdk
git reset --hard e6f009de39f7d7f70280ce7f9c3eafdbab568cb5
cd /home/vagrant/ps4-payload-sdk/libPS4
make
cd /home/vagrant/ps4-payload-sdk/libusbfatfs
make

apt-get install -y socat

git clone https://github.com/xvortex/ps4-hen-vtx.git /home/vagrant/hen
cd /home/vagrant/hen
git reset --hard 665793735c9b320ef6f3e3b5072e7c1c61a0b243
echo '#!/bin/bash\nBASEDIR=$(dirname "$0")\nsocat -u FILE:${BASEDIR}/ps4-hen-vtx.bin TCP:${PS4IP}:9020' > send.sh
chmod +x send.sh
echo -e '#!/bin/bash\nBASEDIR=$(dirname "$0")\npushd ${BASEDIR}/exploit\npython -m SimpleHTTPServer 8080\npopd' > serve.sh
chmod +x serve.sh
./build.sh
cp ps4-hen-vtx.bin /vagrant/hen.bin

apt-get install -y nasm

# https://github.com/xemio/jkpatch.git
# https://github.com/ChendoChap/jkpatch505.git
git clone https://github.com/withmetta/jkpatch505.git /home/vagrant/jkpatch
git reset --hard 4df3163a0610904207f87e36893b3d6982500792
cd /home/vagrant/jkpatch
echo -e '#!/bin/bash\nBASEDIR=$(dirname "$0")\nsocat -u FILE:${BASEDIR}/payload.bin TCP:${PS4IP}:9020\nsleep 0.25\nsocat -u FILE:${BASEDIR}/kpayload.elf TCP:${PS4IP}:9023' > send.sh
chmod +x send.sh
make
cp payload.bin /vagrant/jkpatch.bin
cp kpayload.elf /vagrant/jkpatch.elf
cd librpc/rpcasm
make

git clone https://github.com/Cryptogenic/PS4-5.05-Kernel-Exploit.git /home/vagrant/exploit
cd /home/vagrant/exploit
git reset --hard a5875664ff7c6128ecd237cea7de94c49b0ba619
echo -e '#!/bin/bash\nBASEDIR=$(dirname "$0")\npushd ${BASEDIR}\npython -m SimpleHTTPServer 8080\npopd' > serve.sh
chmod +x serve.sh
./js_shellcode.py  ../hen/ps4-hen-vtx.bin -o homebrew.js

chown -R vagrant:vagrant /home/vagrant
  SHELL
end
