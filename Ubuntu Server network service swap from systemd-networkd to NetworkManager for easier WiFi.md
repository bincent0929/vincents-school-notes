# Ubuntu Server Network Service Swap From systemd-networkd To NetworkManager For Easier WiFi

You may be tired of going into that darn `/etc/netplan/50-cloud-init.yaml` file. If you are, then setting up `nmcli` will be worth your time. It'll find WiFi networks for you and you can use it to connect to them. All from the `cli`! No file editing needed!

Firstly, you want to install the `network-manager` package for Ubuntu:
```bash
sudo apt install network-manager
```

Now we'll want to configure how the NetworkManager service will behave before we have it start up.

First, you will create `/etc/netplan/01-network-manager-all.yaml`. This will let the NetworkManager actually manage your computer's interfaces.

To do that, type: 
```bash
sudo nano /etc/netplan/01-network-manager-all.yaml
```

Then put this in it:
```
# lets NetworkManager...manage all the interfaces
network:
  version: 2
  renderer: NetworkManager
```

After, go into the NetworkManager config: 
```bash
sudo nano /etc/NetworkManager/NetworkManager.conf
```

Then, put this in:
```
[main]
plugins=ifupdown,keyfile
dns=systemd-resolved

[ifupdown]
managed=false

[device]
wifi.scan-rand-mac-address=no
```

By having `dns=systemd-resolved`, we still use the standard system service for DNS queries, avoiding any possible conflicts.

Now you want to disable and then stop the `systemd-networkd` service:
```bash
sudo systemctl disable systemd-networkd
sudo systemctl stop systemd-networkd
```

Then enable the NetworkManager service and restart your device:
```bash
sudo systemctl enable NetworkManager
```

Once your device turns back on, you can use `nmcli d` to list devices. It'll look a little like this:
```bash
user@device:~$ nmcli d
DEVICE         TYPE      STATE                   CONNECTION
eth0           ethernet  connected               Wired connection 1
tailscale0     tun       connected (externally)  tailscale0
wlan0          wifi      connected               netplan-wlan0-bill-wi the science-fi 5g
p2p-dev-wlan0  wifi-p2p  disconnected            --
lo             loopback  unmanaged               --
```

And if you want to connect to WiFi, instead of going to `/etc/netplan/50-cloud-init.yaml` and dealing with this mess:

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

You can go ahead and type:
```bash
nmcli dev wifi list
```

Now you'll see all the WiFi available in your area and connect to them!

With the SSID you pick from that list you'll want to type:
```bash
sudo nmcli dev wifi connect network-ssid
```

Replace `network-ssid` with the SSID of the network you want to connect to and it'll prompt you for a password for the network if it has one.

Now you should be connected!

Run `nmcli d` again to double check. It should say that you're connected!