# TP-Link TL-WR840N V5

## Router Configuration with Original Firmware

1. __Hardware version:__ TL-WR840N v5 00000005
2. __Firmware version:__ 0.9.1 3.16 v0001.0 Build 211109 Rel.72604n
3. __MAC Address:__ <!-- 70:4F:57:59:50:B4 -->
4. __Default SSID:__ TP-Link_50B4
5. __Default Password:__ <!-- 63407228 -->
6. __Default Username:__ admin
7. __Default Password:__ admin
8. __Default IP:__ 192.168.0.1

## Router Configuration with OpenWRT Firmware

1. __Default SSID:__ Wi-Fi is disabled by default.
2. __Default Username:__ root
3. __Default Password:__ No password is set by default.
4. __Default IP:__ 192.168.1.1

## Setup router as an Access Point (Original Firmware)

1. Reset the Router by holding the RESET button on the back for 10 seconds.
2. Connect the router to PC via ethernet cable.
3. Alternatively, join the Wi-Fi network.
4. Goto 192.168.0.1
5. Login as admin.
6. Click on Quick Setup.
7. Click Next.
8. Select Access Point and click Next.
9. Enable Wireless, Set Wireless Network Name, Security Type and Wireless Password.
10. Check More Advanced Wireless Settings. Set Mode to 11bgn mixed, Channel Width and Channel to Auto.
11. Click Next.
12. Select Lan Type as Static IP. Set a Static IP to access the Web Interface (Eg. 192.168.1.100) and Subnet Mask (255.255.255.0). Reserve the same Static IP in the DHCP Reservation list on the Main Router.
13. Disable DHCP server and click Next.
14. Review all the settings and click Finish.
15. Now connect the LAN port of this router to the LAN port of the Main Router.
16. We should now have working internet through this Access Point. Connect with the configured Wi-Fi SSID and password.

## Switch Official Firmware with OpenWRT Firmware

1. Download the latest OpenWRT Firmware for TL-WR840N v5. Image is added in the Firmwares folder. (Last upgrade for v5 was in version 18.06.9)
2. Open the Web Interface and login as admin.
3. Click on System Tools and then Firmware Upgrade.
4. Click on Choose File and select the downloaded file.
5. Click on Upgrade and wait for the process to finish.
6. Once the Router Firmware installation finishes, (LED stops blinking), connect the router to PC and goto 192.168.1.1
7. OpenWRT Lucy Interface will open up. Click on Login.
8. OpenWRT is ready to be used.

## Restore from OpenWRT Firmware to Official Firmware

1. Download the original image for TL-WR840N v5. Image is added in the Firmwares folder.
2. Rename the image to `originalfw.bin`.
3. SSH into the router and copy the original firmware. `ssh root@192.168.1.1 "cat > /tmp/originalfw.bin" < /path/to/firmware/originalfw.bin`
4. Rebuild the image without the boot partition. `dd if=originalfw.bin of=originalfw_stripped.bin skip=257 bs=512`
5. Flash the stripped image using sysupgrade. `sysupgrade originalfw_stripped.bin`
6. Wait for the router to finish the flashing process. Indicated by the blinking LEDs.
7. Restoration should be completed and the router will be reset to its default IP address, Wireless SSID and Passwords.

## Fix broken Firmware Upgrade (Alternating red and green LED blinking on router / No Web Interface / No SSH / Bricked Router)

1. Setup TFTP on your PC (Refer _Setup TFTP on Windows/Mac/Linux_ link from the [References](#references) section.)
2. Adding TFTP setup for Linux here as I restored by Router on a Linux Machine.
2.1. Install atftpd. `sudo apt install atftpd`
2.2. Create a dir for saving the image to upload to router. `sudo mkdir /srv/tftp`
2.3. Download the tftp recovery image for TL-WR840N v5. Image is added in the Firmwares folder.
2.4. Rename the image to `tp_recovery.bin` and copy it to the dir created above. `sudo cp /path/to/firmware/tp_recovery.bin /srv/tftp`
2.5. Change the ownership of the folder and file inside. `chown nobody:nogroup -R /srv/tftp`
2.6. Start the tftp server. `atftpd --daemon --no-fork --logfile - /srv/tftp`
2.7. Check if the tftp server is listening. `netstat -lunp | grep 69`
3. Set computer ethernet static IP to `192.168.0.66` (Edit the `/etc/dhcpcd.conf` and `systemctl restart dhcpcd`)
4. Connect Router and PC via ethernet cable.
5. Turn off router. Press and hold the reset button and turn on router. (VERY IMPORTANT❗️)
6. Once the LED becomes green leave the reset button. The flashing process should start. This will be indicated by the flashing LEDs.
7. Wait for the flashing to complete. This should take about 4-5 minutes.
8. Now the SSH should be available.
9. SSH into the router. `ssh root@192.168.1.1`
10. Now from here if you want to go to original firmware, follow the steps in [Restore from OpenWRT Firmware to Official Firmware](#restore-from-openwrt-firmware-to-official-firmware) section.
11. If you want to roll back to the working OpenWRT firmware, follow the steps below.
11.1. SSH into the router. `ssh root@192.168.1.1`
11.2. Check for free memory. Memory should equal to or more than the size of firmware image. `free`
11.3. Download the working copy of OpenWRT Firmware for TL-WR840N v5. Image is added in the Firmwares folder.
11.4. Copy the image to the router. `ssh root@192.168.1.1 "cat > /tmp/openwrt-18.06.9-ramips-mt76x8-tl-wr840n-v5-squashfs-sysupgrade.bin" < /path/to/firmware/openwrt-18.06.9-ramips-mt76x8-tl-wr840n-v5-squashfs-sysupgrade.bin`
11.5. Flash the image using sysupgrade. `sysupgrade -v /tmp/openwrt-18.06.9-ramips-mt76x8-tl-wr840n-v5-squashfs-sysupgrade.bin`
12. Wait for the router to finish the flashing process. Indicated by the blinking LEDs.
13. Now the router should be restored to the OpenWRT defaults.

## References

1. [TL-WR840N Datasheet](https://static.tp-link.com/TL-WR840N(EU)_V5_Datasheet.pdf)
2. [TL-WR840N User Guide](https://static.tp-link.com/1910011966_TL-WR840N(EU)_V5_UG.pdf)
3. [TL-WR840N Quick Installation Guide](https://static.tp-link.com/7106507221_TL-WR840N(EU)_V5_Quick%20Installation%20Guide.pdf)
4. [WR840N Original Firmware](https://www.tp-link.com/in/support/download/tl-wr840n/v5/#Firmware)
5. [OpenWRT WR840N](https://openwrt.org/toh/tp-link/tl-wr840n_v5)
6. [OpenWRT WR840N Hardware Data](https://openwrt.org/toh/hwdata/tp-link/tp-link_tl-wr840n_v5)
7. [OpenWRT Firmware 18.06.4](https://downloads.openwrt.org/releases/18.06.4/targets/ramips/mt76x8/openwrt-18.06.4-ramips-mt76x8-tl-wr840n-v5-squashfs-sysupgrade.bin)
8. [OpenWRT Firmware 18.06.9](https://downloads.openwrt.org/releases/18.06.9/targets/ramips/mt76x8/openwrt-18.06.9-ramips-mt76x8-tl-wr840n-v5-squashfs-sysupgrade.bin)
9. [OpenWRT Downloads](https://downloads.openwrt.org/)
10. [OpenWRT Sysupgrade CLI](https://openwrt.org/docs/guide-user/installation/sysupgrade.cli)
11. [Revert To Stock Firmware](https://gist.github.com/tuyenld/fa216b146a560bca1be7569262240043)
12. [Recover Bricked WR840N](https://forum.openwrt.org/t/tp-link-wr840-bricked/88700)
13. [Recover Bricked WR840N 2](https://forum.openwrt.org/t/tl-wr840n-v5-bricked-solved/49054)
14. [Failed Upgrade Rescue](https://openwrt.org/docs/guide-user/troubleshooting/vendor_specific_rescue)
15. [TP-Link TFTP Recovery](https://community.tp-link.com/en/home/forum/topic/81462?page=1)
16. [TFTP Recovery Linux](https://mikepalmer.net/tp-link-archer-c7-ac1750-v2-tftp-recovery/)
17. [Setup TFTP on Windows/Mac/Linux](https://openwrt.org/docs/guide-user/troubleshooting/tftpserver)
