# AlpineLinuxForGNS3
Custom Alpine Appliance for GNS3
Along the lines of https://wiki.alpinelinux.org/wiki/Install_Alpine_in_QEMU

- check latest version: https://alpinelinux.org/downloads/
- Download Alpine Image: ```wget https://dl-cdn.alpinelinux.org/alpine/v3.18/releases/x86_64/alpine-virt-3.18.4-x86_64.iso -P /tmp```
- go to GNS3 QUEMU guest image dir: ```cd ~/GNS3/images/QEMU/```
- create qemu disk image: ```qemu-img create -f qcow2 alpine_custom.qcow2 1G```
- Boot iso to image: ```qemu-system-x86_64 -boot d -cdrom /tmp/alpine-virt-3.18.4-x86_64.iso -hda ~/GNS3/images/QEMU/alpine_custom.qcow2 -enable-kvm -m 1G -serial telnet:localhost:4321,server,nowait```
- Will give us a nice login shell: ![grafik](https://github.com/DanielBarie/AlpineLinuxForGNS3/assets/73287620/9977366c-47ab-4a3a-b32f-56d45e735c0e)
- login with username root
- start ```alpine-setup```
  - keyboard: de
  - variant: de
  - hostname: default
  - interfaces: default (eth0)
  - configure per dhcp
  - password: ```admin```
  - timezeon: ```Europe/Berlin```
  - leave default mirror
  - no additional user
  - allow root ssh login: ```yes```
  - use ```sda1```
  - use sda1 as ```sys```
  - erase disk ```yes```, will take a while to set up...
  - ```halt```
- boot: ```qemu-system-x86_64 -boot c -hda ~/GNS3/images/QEMU/alpine_custom.qcow2 -enable-kvm -m 1G -serial telnet:localhost:4321,server,nowait -object rng-random,filename=/dev/urandom,id=rng0 -device virtio-rng-pci,rng=rng0 &```
![grafik](https://github.com/DanielBarie/AlpineLinuxForGNS3/assets/73287620/96a124bf-f500-4e6d-a0e9-1ef3715c7968)

- change welcome message (motd): ```echo "Hallo. Das ist Alpine Linux..." > /etc/motd```
- change available repos: ```nano /etc/apk/repositories```, add / uncomment lines:
  ```
  http://dl-cdn.alpinelinux.org/alpine/v3.18/main
  http://dl-cdn.alpinelinux.org/alpine/v3.18/community
  http://dl-cdn.alpinelinux.org/alpine/edge/testing
  ``` 
- add packages: ```apk add nano mc bonding iperf3 agetty```
- add apk-autoupdate (need to add from edge repo):
  - ```apk add apk-autoupdate --update-cache --repository https://dl-cdn.alpinelinux.org/alpine/edge/testing/ --allow-untrusted```
  - make it run one a day:
    - ```cd /etc/periodic/daily```
    - ```ln -s /usr/sbin/apk-autoupdate ./```
- add bmon (bandwidth monitor):
  - ```apk add apk-autoupdate --update-cache --repository https://dl-cdn.alpinelinux.org/alpine/edge/community/ --allow-untrusted```
- add ifupdown-nw (for gns3): (should work from repos added above, if not, install from edge/community)
  - ```apk add ifupdown-ng```
- add another interface, edit ```/etc/network/interfaces```:
  - ```nano /etc/network/interfaces```
  ```
  auto lo
  iface lo inet loopback
  
  auto eth0
  iface eth0 inet dhcp
  
  auto eth1
  iface eth1 inet dhcp

  ```
- remove root password (for autologin, will run as network appliance...): ```nano /etc/passwd```, remove ```x``` in user root's line:
  ```root:x:0:0:root:/root:/bin/ash```
  becomes
  ```root::0:0:root:/root:/bin/ash```
- Passwordless auto-login: Change highlighted line:
![grafik](https://github.com/DanielBarie/AlpineLinuxForGNS3/assets/73287620/8b953681-41bd-4724-bf7c-2e93081c8cce)  
  to become
  ```
  ttyS0::respawn:/sbin/agetty -a root --noissue -L ttyS0 115200 vt100
  ```
- Lower boot wait: ```nano /boot/extlinux.conf``` change highlighted line as shown:
  ![grafik](https://github.com/DanielBarie/AlpineLinuxForGNS3/assets/73287620/99b5fb6a-fe87-47b1-b788-0058f04035fe)

- 
Nice, we now have a 155MB custom Alpine Linux Image for our network lab.  
![grafik](https://github.com/DanielBarie/AlpineLinuxForGNS3/assets/73287620/9a815000-8a69-4b81-a696-8c47a55424d1)
