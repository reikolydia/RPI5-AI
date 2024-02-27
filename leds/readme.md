
# Blinkt! LED internet check

---

## Start the Internet Check Python script service

```shell
sudo systemctl start internetcheck.service
```

## To restart

```shell
sudo systemctl restart internetcheck.service
```

## To stop:

```shell
sudo systemctl stop internetcheck.service
```

---

## 1. Create service

> In: ```/etc/systemd/system/internetcheck.service```

```bash
[Unit]
Description=BLINKT Internet Checks
After=multi-user.target

[Service]
Type=simple
Restart=always
ExecStart=/usr/bin/python3 /home/pi/leds/internet_check.py

[Install]
WantedBy=multi-user.target
```

### 2. Reload systemd daemon

```shell
sudo systemctl daemon-reload
```

### 3. Enable the service

```shell
sudo systemctl enable
```

### 4. Start the service

```shell
sudo systemctl start internetcheck.service
```

### 5. Check the service

```shell
sudo systemctl status internetcheck.service
```

> Should return an output as follows

```bash
● internetcheck.service - BLINKT Internet Checks
     Loaded: loaded (/etc/systemd/system/internetcheck.service; enabled; vendor>
     Active: active (running) since Fri 2024-02-16 22:47:31 +08; 1 weeks 3 days>
   Main PID: 13687 (python3)
      Tasks: 1 (limit: 8754)
     Memory: 2.4M
     CGroup: /system.slice/internetcheck.service
             └─13687 /usr/bin/python3 /home/pi/leds/internet_check.py

Feb 16 22:47:31 argon-eon systemd[1]: Started BLINKT Internet Checks.
Feb 16 22:47:32 argon-eon python3[13772]: TERM environment variable not set.
```
