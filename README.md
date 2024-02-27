# RPI5-AI
## Run an externally accessible AI chatbot on your rPI 5!

todo: (not in order yet)

Set up Pi 5
Make it boot from nvme
Blinkt! setup
internetcheck.py
Zerotier

set up Portainer / docker

---

# Set up a PI 5

###1. Using Raspberry Pi Imager, flash a 64 bit raspbian OS on a SD card (i use headless for this example)
###2. Before unplugging the SD card, add to bottom of ```/boot/config.txt```

```shell
dtparam=pciex1_gen=3
```

> If this does not work (ie, the nvme drive does not show up later with ``` lsblk ``` ), use this instead: ```dtparam=nvme```

###3. Initial boot into it, setting up SSH, hostnames, other basic stuff

```shell
sudo raspi-config
```

###4. Update the pi

```shell
sudo apt update
sudo apt full-upgrade -y
sudo apt dist-upgrade -y
sudo apt autoremove -y
sudo apt clean -y
```

###5. Edit the EEPROM

```shell
sudo rpi-eeprom-config --edit
```

> Change the ```BOOT_ORDER``` line to the following (the ```6``` at the back is important)

```shell
BOOT_ORDER=0xf416
```

###6. Reboot the Pi

###7. Check that the NVME drive is seen

```shell
lsblk
```

> You should see the NVME drive listed as: ```nvme0n1```

> If you do not see the drive, check step 2

####If you still do not see the drive, the drive should at least be in this supported list

> Taken from: https://shop.pimoroni.com/products/nvme-base

- AData Legend 700
- AData Legend 800
- AData XPG SX8200 Pro
- Axe Memory Generic Drive
- Crucial P2 M.2
- Crucial P3 M.2
- Crucial P3 Plus M.2
- Inland PCIe NVMe SSD
- Kingston KC3000
- Kioxia Exceria NVMe SSD
- Kioxia Exceria G2 NVMe SSD
- Lexar NM620
- Lexar NM710
- Netac NV2000 NVMe SSD
- Netac NV3000 NVMe SSD
- Origin Inception TLC830 Pro NVMe
- Patriot P310
- PNY CS1030
- Sabrent Rocket 4.0
- Sabrent Rocket Nano
- Samsung 980
- Samsung 980 Pro (500GB/1TB)
- Team MP33
- Western Digital Black SN750 SE (Phison Controller)

