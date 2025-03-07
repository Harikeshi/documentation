Важно выставить `uefi`, `single disk`

После установки дистрибутива
установка прав
```sh
su -
nano /etc/sudoers
```

установка **ssh**
```sh
sudo apt install openssh-client /old
```
или прописать в config rsa ключи скопировать .ssh

установка mirror2
```sh
sudo nano /etc/apt/sources.list.d/source_new.list
```
```sh
deb http://mirror2.01050-1.ru/proxmox bookworm pve-no-subscription
deb http://mirror2.01050-1.ru/proxmox-pbs bookworm pve-no-subscription
deb http://mirror2.01050-1.ru/debian bookworm main contrib non-free non-free-firmware
deb-src http://mirror2.01050-1.ru/debian bookworm main contrib
deb http://mirror2.01050-1.ru/debian bookworm-updates main contrib non-free non-free-firmware
deb-src http://mirror2.01050-1.ru/debian bookworm-updates main contrib 
deb http://mirror2.01050-1.ru/debian-security bookworm-security main contrib non-free non-free-firmware
```
### Old
```sh
deb http://mirror.01050-1.ru/debian bullseye main contrib non-free
```
### Debian 12 (bookworm)
```sh
deb http://mirror2.01050-1.ru/debian bookworm main contrib non-free non-free-firmware
#deb-src http://mirror2.01050-1.ru/debian bookworm main contrib
deb http://mirror2.01050-1.ru/debian bookworm-updates main contrib non-free non-free-firmware
#deb-src http://mirror2.01050-1.ru/debian bookworm-updates main contrib
 
deb http://mirror2.01050-1.ru/debian-security bookworm-security main contrib non-free non-free-firmware
```
### Установить пакеты:
```sh
g++
qtbase5-dev
libboost1.74-dev
libspdlog-dev
libgtest-dev
```
```sh
open-vm-tools
open-vm-tools-desktop
libboost1.81-dev
cmake
git
gcc
g++
gdb /old
dolphin
kate
```
### Настройка gnome:
```sh
yaru-theme
gnome-tweak -> gnome-shell-exs..-dashtodock dash-to-panel
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
### Настройка общих папок:
```sh
/etc/fs -> .host: /mnt/hgfs fuse.vmhgfs-fuse defaults,allow_other uid=1000 0 0
//192.168.50.3/01051-1 /mnt/x cifs user,uid=harikeshi,gid=harikeshi,rw,suid,username=shchegolikhinsn,pass= 0 0
//192.168.50.63/storage /mnt/work cifs user,uid=harikeshi,gid=harikeshi,rw,suid,username=storage,pass= 0 0
```

### Doxygen
*graphviz* - для отображения графов

### Настройка принтера:
```sh
Установить samba, smbclient 
sudo apt install system-config-printer //dc/
```
В новых версиях qtcreator -> gtest
удалить папку GTest -> `/usr/lib/x86_64-linux-gnu/cmake/GTest`

CRLF -> git config --global core.autocrlf false|true|input
	git config --global --edit 





