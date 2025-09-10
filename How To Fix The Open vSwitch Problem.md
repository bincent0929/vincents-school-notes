# How To Fix The Open vSwitch Problem On Ubuntu 22.04.5 LTS On The Raspberry Pi 4
All credit from here: https://stackoverflow.com/questions/77352932/ovsdb-server-service-from-nowhere#77365896

To fix it you just need to run:
```bash
sudo apt-get install linux-modules-extra-raspi
```
Then
```bash
sudo apt-get install openvswitch-switch-dpdk
```

Now it won't complain to you.
## P.S.
If you are still having problem, it may be related to indentation in `/etc/netplan/50-cloud-init.yaml`. It's very picky.

Here's a working example config:
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