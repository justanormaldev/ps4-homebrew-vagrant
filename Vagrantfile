# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/xenial64"

  config.vm.network "forwarded_port", guest: 8080, host: 8080

  config.vm.provision "shell", inline: <<-SHELL
echo "PS4IP=192.168.1.107" >> /etc/environment

apt-get update

apt-get install -y language-pack-en
echo "LC_ALL=en_US.UTF-8" >> /etc/default/locale
echo "LANG=en_US.UTF-8" >> /etc/default/locale

apt-get install -y git

wget -O - https://apt.llvm.org/llvm-snapshot.gpg.key | sudo apt-key add -
apt-add-repository "deb https://apt.llvm.org/xenial/ llvm-toolchain-xenial-3.9 main"
apt -o Acquire::AllowInsecureRepositories=true update
apt-get install -y clang-3.9 lldb-3.9 binutils make gcc
ln -s /usr/bin/clang-3.9 /usr/bin/clang

export HOME=/home/vagrant

export PS4SDK=${HOME}/ps4sdk
echo "PS4SDK=${PS4SDK}" >> /etc/environment
git clone https://github.com/psxdev/ps4sdk.git ${PS4SDK}; cd ${PS4SDK}; git reset --hard df03b9fd274ec5dabf105a07e6cb9f0bf6685801
cd ${PS4SDK}
make

export LIBORBIS=${HOME}/liborbis
# https://github.com/orbisdev/liborbis.git; cd ${LIBORBIS}; git reset --hard 8c678f60ec8f04177ccc575abec7a7521f297abe
git clone https://github.com/justanormaldev/liborbis.git ${LIBORBIS}; cd ${LIBORBIS}
cd libdebugnet
make
make install
cd ..
cd libelfloader
make
make install
cd ..
cd libps4link
make
make install
cd ..
cd liborbisPad
make
make install
cd ..
cd liborbisAudio
make
make install
cd ..
cd libmod
make
make install
cd ..
cd portlibs
cd libz
make
make install
cd ..
cd libpng
make
make install
cd ..
cd ..
cd liborbis2d
make
make install
cd ..
cd liborbisFileBrowser
make
make install
cd ..
cd liborbisXbmFont
make
make install
cd ..
cd liborbisKeyboard
make
make install

export PS4DEV=${HOME}/ps4dev
echo "PS4DEV=${PS4DEV}" >> /etc/environment
mkdir -p ${PS4DEV}
cd ${PS4SDK}
chmod +x install.sh; ./install.sh

export PS4PSDK=${HOME}/ps4-payload-sdk
echo "PS4PSDK=${PS4PSDK}" >> /etc/environment
# https://github.com/stooged/ps4-payload-sdk
git clone https://github.com/xvortex/ps4-payload-sdk.git ${PS4PSDK}; cd ${PS4PSDK}; git reset --hard e6f009de39f7d7f70280ce7f9c3eafdbab568cb5
find . -type f -exec sed -i 's/PS4SDK/PS4PSDK/g' {} +
cd ${PS4PSDK}/libPS4
make
cd ${PS4PSDK}/libusbfatfs
make

apt-get install -y socat

export HEN=${HOME}/hen
export HEN_BIN=${HEN}/ps4-hen-vtx.bin
git clone https://github.com/xvortex/ps4-hen-vtx.git ${HEN}; cd ${HEN}; git reset --hard 665793735c9b320ef6f3e3b5072e7c1c61a0b243
find . -type f -exec sed -i 's/PS4SDK/PS4PSDK/g' {} +
echo -e "#\x21/bin/bash\nsocat -u FILE:${HEN_BIN} TCP:\\${PS4IP}:9020" > send.sh; chmod +x send.sh
echo -e "#\x21/bin/bash\npushd ${HEN}/exploit\npython -m SimpleHTTPServer 8080\npopd" > serve.sh; chmod +x serve.sh
./build.sh
cp ${HEN_BIN} /vagrant/hen.bin

export MIRA=${HOME}/mira
export MIRA_BIN=${MIRA}/Firmware/MiraFW/MiraFW_Orbis.bin
git clone --recurse-submodules https://github.com/OpenOrbis/mira-project.git ${MIRA}; cd ${MIRA}; git reset --hard 71edaa1263ecade71ac093ee509ac5caf97a5ca3
find . -type f -exec sed -i 's/PS4SDK/PS4PSDK/g' {} +
echo -e "#\x21/bin/bash\nsocat -u FILE:${MIRA_BIN} TCP:\\${PS4IP}:9020" > send.sh; chmod +x send.sh
cd ${MIRA}/Scripts
chmod +x init_development_environment.sh; echo "1" | ./init_development_environment.sh
cp ${MIRA_BIN} /vagrant/mira.bin

apt-get install -y nasm

export JKPATCH=${HOME}/jkpatch
export JKPATCH_BIN=${JKPATCH}/payload.bin
export JKPATCH_ELF=${JKPATCH}/kpayload.elf
# https://github.com/xemio/jkpatch.git
# https://github.com/ChendoChap/jkpatch505.git
git clone https://github.com/withmetta/jkpatch505.git ${JKPATCH}; cd ${JKPATCH}; git reset --hard 4df3163a0610904207f87e36893b3d6982500792
find . -type f -exec sed -i 's/PS4SDK/PS4PSDK/g' {} +
echo -e "#\x21/bin/bash\nsocat -u FILE:${JKPATCH_BIN} TCP:\\${PS4IP}:9020\nsleep 0.25\nsocat -u FILE:${JKPATCH_ELF} TCP:\\${PS4IP}:9023" > send.sh; chmod +x send.sh
make
cp ${JKPATCH_BIN} /vagrant/jkpatch.bin
cp ${JKPATCH_ELF} /vagrant/jkpatch.elf
cd ${JKPATCH}/librpc/rpcasm
make

export EXPLOIT=${HOME}/exploit
git clone https://github.com/Cryptogenic/PS4-5.05-Kernel-Exploit.git ${EXPLOIT}; cd ${EXPLOIT}; git reset --hard a5875664ff7c6128ecd237cea7de94c49b0ba619
echo -e "#\x21/bin/bash\npushd ${EXPLOIT}\npython -m SimpleHTTPServer 8080\npopd" > serve.sh; chmod +x serve.sh
./js_shellcode.py ${HEN_BIN} -o homebrew.js
./js_shellcode.py ${MIRA_BIN} -o mira.js; sed -i 's/writeHomebrewEN/writeMira/g' mira.js

chown -R vagrant:vagrant ${HOME}
  SHELL
end
