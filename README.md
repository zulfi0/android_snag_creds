# Disclaimer
This is just for educational and authorized testing scenarios. The author of this modified script expressly disclaim all liabilities and responsibilities for any unauthorized or illicit use of the tool and not responsible for any consequent damages, losses, or repercussions stemming from the misuse or misapplication of this modified script.

# Android Snag Creds
Capture windows NTLMv2 hashes just using an android device. This is just another implementation of bash bunny QuickCreds from Hak5.

## What is this ?
This is essentially just a script from Offsec's Nethunter that has been modified (by me) to allow capturing NetNTLMv2 Windows hashes even when the target device is locked. This script was inspired by the Bash Bunny tool from Hak5.

## Why Android?
There's no specific reason. I just feel that Android is the most comprehensive IoT device with very capable features.

## How it works ?
Works the same way as QuickCreds from Bash Bunny:
```
QuickCreds on the Bash Bunny works by exploiting network
protocols like LLMNR and NetBIOS to capture credential hashes.
When the Bash Bunny is plugged into the target computer,
it pretends to be an Ethernet device and configures itself as the default gateway and DNS server.
It then runs the Responder tool to listen for network authentication requests.
When the target system tries to connect to this 'network,'
QuickCreds automatically captures and stores the NTLMv2 hashes sent by the target system,
which can later be cracked or used in further attacks.
```
but this is on android.

## Prerequisite
1. Rooted Android device (this script was tested using redmi note 8 (A10) and poco x3 NFC (A13)).
2. Install linux chroot in android [here](https://github.com/zulfi0/install_rootfs_android) (tested on ubuntu 22.04 arm64 rootfs)
3. usb cable

## Setup
Inside android su shell:
1. Enable RNDIS interface using `usbarsenal` from offsec's [kali nethunter](https://gitlab.com/kalilinux/nethunter/build-scripts/kali-nethunter-project)
```bash
./usbarsenal -t win -f adb,rndis -v '0x04B3' -p '0x4010'
```
Note: in order the target recognize our device as ethernet adapter we need to set vendor id `0x04B3` and product id `0x4010` (IBM USB Remote NDIS Network Device usb and vendor ID)

Inside the linux chroot:
1. install python2, iproute2, iptables, isc-dhcp-common and isc-dhpc-server.
2. install the `responder-bunny.deb` from this repo (downloaded from [Hak5's forum](https://forums.hak5.org/topic/40971-info-tools/))
3. Run the modified script:
```bash
./usbtethering_linux -o rmnet_data1 -i rndis0 -A 172.16.64.10 -B 172.16.64.10 -C 172.16.64.1 -D 255.255.255.0
```
Note: you can use `wlan*` interface if you are connected to WiFi, or if you use simcard you can use `rmnet_data*`.

---
Now you are ready to plug in the android to the target computer.

The modified script will automatically detect if NTLM has been captured inside `/tools/responder/logs/` directory. To indicate the NTLM has been captured the modified script will turn on your flashlight automatically.

Here's another trick:

You can launch HID attack through SSH by enabling HID interface using the `usbarsenal` and setup SSH to the chroot with VPN. I'd like to call it sshid (pronounced: esessid)

## Questions ?

**Q:** Does the Android need to be plugged into the target computer first?  
**A:** No. You can setup the script first.

**Q:** The responder is too old. Have you tried the latest version?  
**A:** Yes! I did some tests using the latest responder, but I encountered many errors.

**Q:** Does the target need to be unlocked?  
**A:** No! The target computer doesn't need to be unlocked as long as the target computer has been logged in once. This means the script won't work if the target computer has just been powered on.

**Q:** I have some ideas to improve the script. Can I modify the script?  
**A:** Yes, changes and improvements are welcome.

---
### Credits
Thanks to Offsec for creating the great [Kali Nethunter](https://www.kali.org/docs/nethunter/).

Thanks to [Hak5](https://shop.hak5.org/) for creating many cool devices for pentest. 

---
### References
https://www.doyler.net/security-not-included/bash-bunny-quickcreds

https://room362.com/posts/2016/snagging-creds-from-locked-machines/

