# How To Use NetworkManager In The Cli

With NetworkManger, if you want to connect to WiFi, instead of going to `/etc/netplan/50-cloud-init.yaml` and dealing with this mess:

```
network:
    version: 2
    wifis:
       wlan0:
          optional: true
          access-points:
                 "wifi-name":
                      password: "password"
          dhcp4: true
```

(If you don't already have it set up, go [here](./ubuntu-systemd-networkd-to-networkmanager) to do it)

You can go ahead and type:
```bash
nmcli dev wifi list
```

With that, you'll now see all the WiFi access points available in your area and connect to them!

From the list of access points you'll want to pick one and replace `network-ssid` with its name and `actual-password` with its password in the line below and then run it in your terminal:
```bash
sudo nmcli dev wifi connect network-ssid password actual-password
```

If there's not password just type:
```bash
sudo nmcli dev wifi connect network-ssid
```

Now you should be connected!

Run `nmcli d` again to double check. It should say that you're connected.
## P.S.
### Connecting To A New Network After Using NetworkManager To Connect To A Different Network

For some reason, when you have already used NetworkManager to connect to a WiFi network, it clears the WiFi access points that it has scanned. So when you try to run a `sudo nmcli dev wifi connect` with all the right info put in, it acts like it doesn't know what you're talking about.

To get around this you just need to have it rescan for access points again. You can do that by inputting this:
```bash
sudo nmcli device wifi rescan
```

Now you can go ahead and do `sudo nmcli dev wifi connect` with your access point of choice if you already know the networks around you or you can do `nmcli dev wifi list` if you want to connect to one you don't already know off the top of your head.