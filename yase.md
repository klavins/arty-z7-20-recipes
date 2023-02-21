

Build tools
---

Install build essentials with

```
sudo dnf check-update
sudo dnf upgrade
sudo dnf install autoconf automake
sudo dnf install binutils bison flex
sudo dnf install gcc gcc-c++ gdb
sudo dnf install gcc
sudo dnf install g++
sudo dnf install glibc-devel
sudo dnf install libtool
sudo dnf install make
```

Yase
---

Install yase via git with

```
sudo dng install git
git config --global http.sslVerify false
git clone https://github.com/klavins/yase.git
```