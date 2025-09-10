# How To Fix The Open vSwitch Problem On Ubuntu 22.04.5 LTS On The Raspberry Pi 4

You might've gone into `/etc/netplan/50-cloud-init.yaml`, updated it, and then did a `sudo netplan apply` to then find yourself encountering something like, `WARNING:root:Cannot call Open vSwitch: ovsdb-server.service is not running.`. From what I've found, it seems like this is due to Ubuntu Server on the Raspberry Pi.

Here's how to fix it.

All credit from here: https://web.archive.org/web/20250910171255/https://stackoverflow.com/questions/77352932/ovsdb-server-service-from-nowhere#77365896

Yes, you do need to download a package. You'll want to either trying plugging in ethernet, USB tether from your phone, or wiping your device to get to the point where you can install packages again if you can't download them.

To fix it you just need to run:
```bash
sudo apt-get install linux-modules-extra-raspi
```
Then:
```bash
sudo apt-get install openvswitch-switch-dpdk
```

Restart your device and now it won't complain to you when you do `sudo netplan apply`!
## P.S.
If you are still having problems actually connecting to WiFi, it may be related to indentation in `/etc/netplan/50-cloud-init.yaml`. It's very picky.

Here's a properly indented, working example config:
```
network:
    version: 2
    wifis:
       wlan0:
          optional: true
          access-points:
                 "wifi-name":
                      password: "password0"
          dhcp4: true
```

Just replace `wifi-name` and `password0`. Make sure to leave the quotations around them.

Now try a `sudo netplan apply` and see if it's working. Do `sudo netplan try` to further diagnose if not.