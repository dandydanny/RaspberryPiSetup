# RaspberryPiSetup
Raspberry Pi 3 Quick Start Guide

### Transfer image to microSD card
`sudo dd bs=1m if=2018-04-18-raspbian-stretch.img of=/dev/rdisk2 conv=sync`

### Fix laggy mouse pointer (Microsoft keyboard with touchpad)
Add `usbhid.mousepoll=0` to /boot/cmdline.txt

### Disable screensaver
`sudo apt-get install xscreensaver` and disable using xscreensaver's UI

### Adjust 7" Touchscreen Brightness
Open `/sys/class/backlight/rpi_backlight/brightness` and modify integer value, 0-255

### Update Raspbian
First, update your system's package list by entering the following command:
`sudo apt-get update`

Next, upgrade all your installed packages to their latest versions with the command:
`sudo apt-get dist-upgrade`

Clear downloaded package files (.deb files) in `/var/cache/apt/archives`. 
`sudo apt-get clean.`
