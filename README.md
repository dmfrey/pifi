# pifi
PiFi Hotel Hotspot based on Raspberry Pi

When complete, connect to:
* SSID: pifi
* Password: set in `etc/hostapd/hostapd.conf`, modify before setting up

## Hardware
For this project the following hardware is required:

1. Raspberry Pi 3
2. External WiFi USB dongle (such as Edimax EW-7811Un 150Mbps 11n Wi-Fi USB Adapter)
3. Travel Keyboard (such as FAVI FE01 2.4GHz Wireless USB Mini Keyboard w Mouse Touchpad)
4. HDMI Cable
5. Micro USB for power
6. SD Card for Operation System
7. Power button (optional)

Here is an Amazon Wishlist with all all the hardware you would require: https://amzn.com/w/CWSDWEDT9PY0

## Operating System
Install the latest Raspbian Jessie, available from https://www.raspberrypi.org/downloads/raspbian/.

Prepare the SD Card for the OS following the installation instructions on the download page. Run all updates.

## Software
The software installed will turn the internal WiFi, wlan0, into an internal network. All external interfaces will be used to access the internet. Optionally, it can be configured to use an OpenVPN connection for all traffic.

1. hostapd
2. dnsmasq
3. openvpn (optional)

`$ sudo apt-get install hostapd dnsmasq openvpn`

## Setup the packages
Copy or symlink all the files under `etc` in their respective directories.

Now enable the services to start on boot
```
$ sudo update-rc.d hostapd enable
$ sudo update-rc.d dnsmasq enable
```

Optional, setup OpenVPN

```
$ sudo update-rd.d openvpn enable
```

OpenVPN in the current setup is configured to connect to a ProXPN premium account. It is recommended to create a local branch and modify `etc/openvpn/client.conf` appropriate to your VPN connection.  `etc/openvpn/auth.txt` will also need to be updated with the correct credentials.

The iptables rules in `etc/iptables.ipv4.nat` are configured to send all traffic through `tun0`, the virtual connection created when OpenVPN is connected. If OpenVPN is not configured, edit `etc/iptables.ipv4.nat` and change `tun0` to `eth0` or `wlan1`, depending on whichever interface is connected to the internet.

There are now iptables rules for passing all traffic through `tun0`, `eth0` or `wlan1`. Simply modify `etc/rc.local` and comment out the default `iptables-restore` rule and uncomment the appropriate one. The default is pass all traffic through `tun0`.

## Notes

### Connection to the Hotel
1. When in a hotel, connect the Raspberry Pi to the TV.
2. Open the default browser and click through the hotel's paywall for WiFi.
3. (Optional) If OpenVPN is configured, the service will have to be restarted after a connection has been made `$ sudo service openvpn restart`


### Add a phyical power button

Do you feel like trying to add a physical power button to turn the pifi on and off. There are scripts now in the repository.  There is a script that will listen for shutdown on the GPIO pins 5 and 6. Also included is a systemd startup script to listen to the short on the GPIO. When enabled, it will start listening at startup, cleanly shutdown and restart if it should crash.

I'll leave it up to you for how to solder in the power button.

Check out [this article](https://howchoo.com/g/mwnlytk3zmm/how-to-add-a-power-button-to-your-raspberry-pi) for some help.


### Further Enhancements
1. VNC Server - this is to configure the Hotel's paywall without having to connect the Raspberry Pi to the TV first, just open a VNC Connection from a laptop and configure the Hotel's wifi.
2. Ad Blocking - configure dnsmasq to block all ad networks

