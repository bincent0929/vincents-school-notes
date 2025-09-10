
Firstly, you want to install the `network-manager` package for Ubuntu.

`sudo apt install network-manager`

Now that we have NetworkManager installed, you'll want to stop `systemd-networkd`. Do that by typing:
```
sudo systemctl disable systemd-networkd
sudo systemctl stop systemd-networkd
```

Then you want to create `/etc/netplan/01-network-manager-all.yaml`

To do that, type: `sudo nano /etc/netplan/01-network-manager-all.yaml`

Then put this in it:
```
# lets NetworkManager...manage all the interfaces
network:
  version: 2
  renderer: NetworkManager
```

After, go into the NetworkManager config: `/etc/NetworkManager/NetworkManager.conf`

Then put this in:
```
[main]
plugins=ifupdown,keyfile
dns=systemd-resolved

[ifupdown]
managed=

[device]
wifi.scan-rand-mac-address=no
```

By having `dns=systemd-resolved` we still use the standard system service for DNS queries.

Now you want to disable and then stop the `systemd-networkd` service:
```
sudo systemctl disable systemd-networkd
sudo systemctl stop systemd-networkd
```

Then enable the NetworkManager service and restart your device:
`sudo systemctl enable NetworkManager`

Once your device turns back on, you can use `nmcli d` to list devices. It'll look a little like this:
```
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

You can go ahead and type `nmcli dev wifi list`, and you'll see all the WiFi available in your area!