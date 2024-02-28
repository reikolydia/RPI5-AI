# RPI5-AI
## Run an externally accessible AI chatbot on your Raspberry Pi 5!

---

## To-do
> Not in order yet

- [x] Set up Pi 5
- [x] Make it boot from nvme
- [x] Blinkt! setup & internetcheck service
- [x] Zerotier
- [ ] Portainer / docker
- [x] Clone SD to NVME SSD
- [ ] The AI parts
- [ ] 

---

## Item list

> Your list of items may vary

1. Raspberry Pi 5
   > ```8 GB``` of RAM is recommended
2. A NVMe Base / HAT
3. A case to house the Pi and the Base / HAT
   > Used here in this example that includes both the case and the NVME base is the:
   > ```Argon NEO 5 M.2 NVME PCIE Case for Raspberry Pi 5```
4. A Pi 5 capable power supply
5. 32 GB MicroSD card
6. 1 TB M.2 NVME SSD
7. Blinkt! RGB LED
8. 

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

### 3. Initial boot into it, setting up other basic Pi stuff

```shell
sudo raspi-config
```

### 4. Set up SSH keys

> On the Pi

```shell
ssh-keygen -t rsa -b 4096 -t ecdsa -b 521
```

<br>

> From Windows

```shell
ssh-keygen.exe -t rsa -b 4096 -t ecdsa -b 521
type $env:USERPROFILE\.ssh\id_rsa.pub | ssh pi@<PI ip address> "cat >> .ssh/authorized_keys"
type $env:USERPROFILE\.ssh\id_ecdsa.pub | ssh pi@<PI ip address> "cat >> .ssh/authorized_keys"
```

<br>

> From Linux

```shell
ssh-keygen -t rsa -b 4096 -t ecdsa -b 521
ssh-copy-id -i ~/.ssh/id_rsa.pub pi@<PI ip address>
ssh-copy-id -i ~/.ssh/id_ecdsa.pub pi@<PI ip address>
```

### 5. Update the pi

```shell
sudo apt update
sudo apt full-upgrade -y
sudo apt dist-upgrade -y
sudo apt autoremove -y
sudo apt clean -y
```

### 6. Edit the EEPROM

```shell
sudo rpi-eeprom-config --edit
```

> Change the ```BOOT_ORDER``` line to the following (the ```6``` at the back is important!)

```shell
BOOT_ORDER=0xf416
```

### 7. Other tweaks

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

<br>

> The following is specific to the ```Argon NEO 5 NVME``` case

```shell
curl https://download.argon40.com/argon-eeprom.sh | bash
curl https://download.argon40.com/argonneo5.sh | bash
```

<br>

> Set up ```Zerotier```

```shell
curl -s https://install.zerotier.com/ | sudo bash
sudo zerotier-cli join <network ID>
```

<br>

> Shell In A Box
> Accessible in a browser at: https://`<Pi 5 IP>`:4200

```shell
apt install shellinabox
```

### 8. Reboot the Pi

```shell
sudo reboot now
```

### 9. Check that the NVME drive is seen

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

### 10. Set up Blinkt! & internetcheck service

> This step uses Python 3.11.2, skip this if you do not use Blinkt!'s LEDs to monitor whether the Pi is connected to the internet

  1. Allow the ```EXTERNALLY-MANAGED``` error

```shell
sudo mv /usr/lib/python3.11/EXTERNALLY-MANAGED /usr/lib/python3.11/EXTERNALLY-MANAGED.old
```

  2. Run the Blinkt! install

```shell
curl https://get.pimoroni.com/blinkt | bash
sudo apt remove python3-rpi.gpio
sudo pip install rpi-lgpio
```

  3. Download the ```leds``` folder into your home directory
  4. Create the internetcheck service

### 11. Clone the SD card to the NVME SSD

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

5. Reboot the Pi
6. Check that the booting is from the NVME SSD

```shell
lsblk
```

> It should look like this:

```bash
NAME        MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
mmcblk0     179:0    0  29.7G  0 disk
├─mmcblk0p1 179:1    0   512M  0 part
└─mmcblk0p2 179:2    0  29.2G  0 part
nvme0n1     259:0    0 931.5G  0 disk
├─nvme0n1p1 259:1    0   512M  0 part /boot/firmware
└─nvme0n1p2 259:2    0   931G  0 part /
```

7. You can remove the SD card from here onwards

### 12. Docker

1. Install Docker

```shell
curl -sSL https://get.docker.com | sh
```

2. Set up the Docker user group

```shell
sudo usermod -aG docker $USER
```

3. Logout or reboot the Pi

### 13. Portainer

> If you already have a Portainer server installed elsewhere, install the Portainer Agent instead

#### Install Portainer

> Taken from https://github.com/pi-hosted/pi-hosted

1. Pull Portainer Docker container

> Portainer Business Edition

```shell
sudo docker pull portainer/portainer-ee:latest
```

> Portainer Community Edition

```shell
sudo docker pull portainer/portainer-ce:latest
```

2. Run Portainer

> Portainer Business Edition

```shell
sudo docker run -d -p 9000:9000 -p 9443:9443 --name=portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ee:latest --logo "https://pi-hosted.com/pi-hosted-logo.png" --log-level=DEBUG
```

> Portainer Community Edition

```shell
sudo docker run -d -p 9000:9000 -p 9443:9443 --name=portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce:latest --logo "https://pi-hosted.com/pi-hosted-logo.png"
```

#### Install Portainer Agent

> If you have already installed Portainer Server, you do not need this step

1. Within Portainer Server, add a new ```environment```
2. Using the ```Environment Wizard```, choose the appropriate environment type, ```Docker Standalone``` is used for this example
3. Copy the given command to the Pi

```shell
docker run -d \
  -p 9001:9001 \
  --name portainer_agent \
  --restart=always \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v /var/lib/docker/volumes:/var/lib/docker/volumes \
  portainer/agent:2.19.4
```

4. Add the ```Name``` and the ```Environment address``` with the right port number
5. Click ```Connect```


