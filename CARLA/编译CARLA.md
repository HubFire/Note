## 系统依赖软件安装
```shell
sudo apt-get update
sudo apt-get install wget software-properties-common
sudo add-apt-repository ppa:ubuntu-toolchain-r/test
wget -O - https://apt.llvm.org/llvm-snapshot.gpg.key|sudo apt-key add -
sudo apt-add-repository "deb http://apt.llvm.org/xenial/ llvm-toolchain-xenial-7 main"
sudo apt-get update
sudo apt autoremove
sudo apt-get  install build-essential clang-7 lld-7 g++-7 cmake ninja-build libvulkan1  libtiff5-dev libjpeg-dev tzdata sed curl unzip autoconf libtool rsync
```


