# RaspberryPiSetup
Raspberry Pi 3 Quick Start Guide

### Transfer image to microSD card
**Mac:** Unmount card's partition first, e.g. /dev/rdisk4s1 by launching Disk Utilities, then click `boot` partition then click `Unmount`.

`sudo dd bs=1m if=2020-02-13-raspbian-buster-full.img of=/dev/rdisk4 conv=sync`

### Fix laggy mouse pointer (Microsoft keyboard with touchpad)
Add `usbhid.mousepoll=0` to /boot/cmdline.txt

`sudo nano /boot/cmdline.txt` to open

`CTRL`+`O` to wrie-out changes, `Enter` to confirm

`CTRL`+`X` to exit Nano editor.

### Disable screensaver
`sudo apt-get install xscreensaver` and disable using xscreensaver's UI

### Flip image vertically
In `/boot/config.txt`, add `lcd_rotate=2`

### Adjust 7" Touchscreen Brightness
Open `/sys/class/backlight/rpi_backlight/brightness` and modify integer value, 0-255

### Update Raspbian
First, update your system's package list by entering the following command:

`sudo apt-get update`

Next, upgrade all your installed packages to their latest versions with the command:

`sudo apt-get dist-upgrade`

Clear downloaded package files (.deb files) in `/var/cache/apt/archives`:

`sudo apt-get clean`

### Make terminal alias
In home directory:
`nano .bashrc`

Add:
`alias ll='ls -lah'`

### Edit 7" touchscreen backlight 'file' permission
Edit the backlight rules file:
`sudo nano /etc/udev/rules.d/backlight-permissions.rules`

Insert line:
`SUBSYSTEM=="backlight",RUN+="/bin/chmod 666 /sys/class/backlight/%k/brightness /sys/class/backlight/%k/bl_power"`

### Automatic backlight brightness change by Cds sensor
Code:
```
from gpiozero import LightSensor
from time import sleep
offset = 15
max = 255
min = 15
prev = 0
delta = 0
f = open('/sys/class/backlight/rpi_backlight/brightness', 'w')
sensor = LightSensor(18, charge_time_limit=0.2, threshold=0.1)
while True:
    print("Sensor value: ", sensor.value)
    lightValue = round(255 * sensor.value + offset)

    if lightValue > max:
        lightValue = max
    if lightValue < min:
        lightValue = min
    print("Light value: ", lightValue)
    delta = abs(lightValue - prev)
    if delta > 1:
        print("Delta: ", abs(lightValue - prev))
        prev = lightValue
        f.seek(0)
        f.write(str(lightValue))
        f.truncate()
    sleep(1)
```

## Enable autorun of autobrightness.py on boot, by making it a systemd service


In `/etc/systemd/system` Make `autobrightness.service` file.

Put in following:
```
[Unit]
Description=Get auto brightness service running at boot
After=mosquitto.service mysql.service

[Service]
ExecStart=/usr/bin/python3 /home/pi/autobrightness/autobrightness.py
Restart=always
StandardOutput=syslog
StandardError=syslog
SyslogIdentifier=autobrightness
User=pi
Group=pi

[Install]
WantedBy=multi-user.target
```

Enable service:

`sudo systemctl enable autobrightness.service`

Start clock for current boot only:

`sudo systemctl start autobrightness.service`

Check if it's running:

`systemctl status autobrightness.service`


GPIO Diagnostics Routine - change pin number to desired pin, and either source or sink an LED with 10K resistor. LED should blink:

```
import RPi.GPIO as GPIO
import time

pin = 18

GPIO.setmode(GPIO.BCM)
GPIO.setwarnings(False)
GPIO.setup(pin,GPIO.OUT)
while True:
        print(pin,"HIGH")
        GPIO.output(pin,GPIO.HIGH)
        time.sleep(0.5)
        print(pin,"LOW")
        GPIO.output(pin,GPIO.LOW)
        time.sleep(0.5)
```
