# RaspberryPiSetup
Raspberry Pi 3 Quick Start Guide

### Transfer image to microSD card
`sudo dd bs=1m if=2018-04-18-raspbian-stretch.img of=/dev/rdisk2 conv=sync`

### Fix laggy mouse pointer (Microsoft keyboard with touchpad)
Add `usbhid.mousepoll=0` to /boot/cmdline.txt
