# AlpineLinuxForGNS3
Custom Alpine Appliance for GNS3

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
