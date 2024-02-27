# RPI5-AI
## Run an externally accessible AI chatbot on your rPI 5!

---

todo: (not in order yet)

Set up Pi 5
Make it boot from nvme
Blinkt! setup & internetcheck service
Zerotier
Clone SD to NVME SSD

set up Portainer / docker

---

# Set up the PI 5

### 1. Using Raspberry Pi Imager, flash a 64 bit Raspbian OS on a SD card

> A headless/lite OS is used for this example

> Raspberry Pi Imager is available at: https://www.raspberrypi.com/software

1. Using version 1.8.5, select ```RASPBERRY PI 5``` as the Raspberry Pi Device

![](https://github.com/reikolydia/RPI5-AI/blob/main/images/rpi_imager_1.8.5.png)

2. Click ```NEXT``` then ```EDIT SETTINGS```

![](https://github.com/reikolydia/RPI5-AI/blob/main/images/rpi_imager_next.png)

> Set up a user here, we will use the default raspbian user: ```pi```

3. In ```GENERAL```: Set the hostname, username and password
4. In ```SERVICES```: Tick ```Enable SSH```
5. Click ```SAVE```
6. ```Would you like to apply OS customisation settings?```: ```YES```

### 2. Before unplugging the SD card from your computer, add to bottom of ```/boot/config.txt```

```shell
dtparam=pciex1_gen=3
```

> If this does not work (ie, the nvme drive does not show up later with ``` lsblk ```), use this instead: ```dtparam=nvme```

### 3. Initial boot into it, setting up SSH, hostnames, other basic stuff

```shell
sudo raspi-config
```

### 4. Update the pi

```shell
sudo apt update
sudo apt full-upgrade -y
sudo apt dist-upgrade -y
sudo apt autoremove -y
sudo apt clean -y
```

### 5. Edit the EEPROM

```shell
sudo rpi-eeprom-config --edit
```

> Change the ```BOOT_ORDER``` line to the following (the ```6``` at the back is important!)

```shell
BOOT_ORDER=0xf416
```

### 6. Other tweaks

> Add to .bash_aliases

```shell
alias apt='sudo apt'
alias update='apt update ; apt full-upgrade -y ; apt dist-upgrade -y ; apt autoremove -y ; apt clean -y'

cd() {
    if [ -n "$1" ]; then
        builtin cd "$@" && ls --group-directories-first
    else
        builtin cd ~ && ls --group-directories-first
    fi
}
```

<br>

> Add to bottom of ```/etc/inputrc```

```shell
set completion-ignore-case On
```

### 7. Reboot the Pi

### 8. Check that the NVME drive is seen

```shell
lsblk
```

You should see the NVME drive listed as: ```nvme0n1```

> If you do not see the drive, check step 2

```bash
NAME        MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
mmcblk0     179:0    0  29.7G  0 disk
├─mmcblk0p1 179:1    0   512M  0 part /boot/firmware
└─mmcblk0p2 179:2    0  29.2G  0 part /
nvme0n1     259:0    0 931.5G  0 disk
```

#### If you still do not see the drive, the drive should at least be in this supported list

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

### 9. Set up Blinkt! & internetcheck service

> This step uses Python 3.11.2, skip this if you do not use Blinkt!'s LEDs to monitor whether the Pi is connected to the internet

  1. Allow the ```EXTERNALLY-MANAGED``` error

```shell
sudo mv /usr/lib/python3.11/EXTERNALLY-MANAGED /usr/lib/python3.11/EXTERNALLY-MANAGED.old
```

  2. Run the Blinkt! install

```shell
curl https://get.pimoroni.com/blinkt | bash
```

  3. Download the ```leds``` folder into your home directory
  4. Create the internetcheck service

### 10. Clone the SD card to the NVME SSD

> Taken from: https://github.com/geerlingguy/rpi-clone

  1. Install rpi-clone
   
```shell
curl https://raw.githubusercontent.com/geerlingguy/rpi-clone/master/install | sudo bash
```

  2. Set up a hostname (if you have not already)
   
```shell
sudo rpi-clone-setup -t testhostname
```

  3. Wipe and clean the NVME SSD

```shell
sudo umount /dev/nvme0n1
sudo wipefs --all --force /dev/nvme0n1
sudo dd if=/dev/zero of=/dev/nvme0n1 bs=1024 count=1
```

  4. Run the clone

```shell
sudo rpi-clone nvme0n1
```

