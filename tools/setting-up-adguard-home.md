# Setting up AdGuard Home

AdGuard Home is alternative to Pi-Hole for network-wide Ad blocking but it has more capabilities for parental control and some other additional features. I have been using Pi-Hole on my home server, Raspberry Pi, virtual machine and even Docker container at home network to stop all annoying ads pop-ups on my mobile devices and computers for some time. However, recently AdGuard Home has got my attention since I found out that OpenWRT can run AdGuard Home natively without requring any additional server. It has been a year now using AdGuard Home instead of Pi-Hole on my home network.

## Prerequisites

* Dedicated bare-metal Linux server (Raspberry Pi 3, 4 or even Pi Zero 2 w) or Linux virtual machine running on some kind of type 1 hypervisor
* Docker if you like to use the docker container for AdGuard Home (I use a dedicated VM to deploy AdGuard Home)
* Basic understanding of Linux commands and YAML for backend configuration change

## Installation

For automated install on LInux, run the following command.

```
curl -s -S -L https://raw.githubusercontent.com/AdguardTeam/AdGuardHome/master/scripts/install.sh | sh -s -- -v
```

For other methods of installation, go to AdGuardHome Github page [https://github.com/AdguardTeam/AdGuardHome#getting-started](https://github.com/AdguardTeam/AdGuardHome#getting-started)

## Configuration

After the AdGuard Home installation, the following will be displayed on the screen to initiate the setup on the server.

