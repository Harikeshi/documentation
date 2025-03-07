```sh
sudo fdisk /dev/sda
sudo fdisk -l
sudo mkfs.ext4 /dev/sda
sudo blkid
sudo mkdir /work
```
```sh
fstab -> UUID=360ca23a-300t-asdd-8c8e-a3xdf46dlpf1 /work ext4 errors=remount-ro 0 1
```
```sh
sudo mount -a
```
```sh
apt-get install -y samba samba-client
sudo chmod -R 0755 /work
groupadd smbgrp
useradd astra
usermod -aG smbgrp astra
sudo chgrp smbgrp /work
sudo smbpasswd -a astra

sudo nano /etc/samba/smb.conf
[astra]
path = /work               
valid users = @smbgrp
guest ok = no
browsable = yes
writable = yes
```
```sh
sudo systemctl restart smbd
sudo systemctl restart nmbd
sudo iptables -A INPUT -p tcp -m tcp --dport 445 -s 10.0.0.0/24 -j ACCEPT
sudo iptables -A INPUT -p tcp -m tcp --dport 139 -s 10.0.0.0/24 -j ACCEPT
sudo iptables -A INPUT -p udp -m udp --dport 137 -s 10.0.0.0/24 -j ACCEPT
sudo iptables -A INPUT -p udp -m udp --dport 138 -s 10.0.0.0/24 -j ACCEPT
sudo apt-get install iptables-persistent
sudo iptables -L
```