Важно выставить `uefi`, `single disk`

После установки дистрибутива
установка прав
```sh
su -
nano /etc/sudoers
```
установка **mirror2**
```sh
sudo wget -O /etc/apt/trusted.gpg.d/mirror2.asc http://mirror2.01050-1.ru/mirror2.asc
sudo nano /etc/apt/sources.list.d/source_new.list
```
```sh
### astralinux 1.7.6
# EXTENDED
deb http://mirror2.01050-1.ru/astra/1.7/extended stable astra-ce backports contrib gitlab main non-free #experimental
# BASE
deb http://mirror2.01050-1.ru/astra/1.7/base stable main contrib non-free
```
установка **ssh**
```sh
sudo apt install openssh-client
```
настройка **ssh-client** для новых версий
в `/etc/ssh/ssh_config` прописать:
```
PubkeyAcceptedAlgorithms +ssh-rsa
HostkeyAlgorithms +ssh-rsa
```
настроить `ssh/astra -> Bitbucket` и `win -> ssh/astra`
### Установка утилит
```sh
sudo apt install open-vm-tools open-vm-tools-desktop ninja-build cmake git gcc g++ gdb doxygen dolphin kate
```
### Установка **qt5**
```sh
sudo apt install qtbase5-dev
sudo apt install qtbase5-examples
sudo apt install qtbase5-doc
sudo apt install qtbase5-doc-dev
sudo apt install qtbase5-doc-html
sudo apt install qtcreator
```
### Установка библиотек:
```sh
sudo apt install clang-format
sudo apt install libboost1.74-dev
sudo apt install libgtest-dev # по версии gcc
sudo apt install libgmock-dev # по версии gcc
sudo apt install googletest # по версии gcc
sudo apt install libspdlog-dev # logger
```
### Настройка общих папок:
```sh
/etc/fs -> .host: /mnt/hgfs fuse.vmhgfs-fuse defaults,allow_other uid=1000 0 0
//192.168.50.3/01051-1 /mnt/x cifs user,uid=harikeshi,gid=harikeshi,rw,suid,username=shchegolikhinsn,pass= 0 0
//192.168.50.63/storage /mnt/work cifs user,uid=harikeshi,gid=harikeshi,rw,suid,username=storage,pass= 0 0
```
### Doxygen
**graphviz** - для отображения графов

### Настройка принтера:
```sh
Установить samba, smbclient 
sudo apt install system-config-printer //dc/
```
В новых версиях qtcreator -> gtest
удалить папку GTest -> `/usr/lib/x86_64-linux-gnu/cmake/GTest`

CRLF -> git config --global core.autocrlf false|true|input
	git config --global --edit 





