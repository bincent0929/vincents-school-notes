# Ubuntu Server Network Service Swap From `systemd-networkd` To NetworkManager For Easier WiFi

You may be tired of going into that darn `/etc/netplan/50-cloud-init.yaml` file. If you are, then setting up `nmcli` will be worth your time. It'll find WiFi networks for you and you can use it to connect to them. All from the `cli`! No file editing needed!

Firstly, make sure all of your packages and repositories are up to date:
```bash
sudo apt update
sudo apt upgrade
sudo reboot
```

If you are disconnected from the internet because of a faulty configuration file and/or the [Open vSwitch service problem](./open-vswitch-problem-ubuntu-22.04.5LTS-rpi4), then you will need to connect your device to the internet through an interface that is working, or transfer the package from another device you have.

Once you get your device updated and reboot you'll now want to install NetworkManager:
```bash
sudo apt install network-manager
```

But before we start using NetworkManager, we'll want to configure it a little bit.

First, make a copy of the `/etc/netplan/50-cloud-init.yaml` file (you could name it `/etc/netplan/50-cloud-init.yaml.copy`) and then delete it. If you don't, it can cause problems with NetworkManager handling your interfaces.

After you do that, go ahead and create `/etc/netplan/01-network-manager-all.yaml`. This will let the NetworkManager actually manage your computer's interfaces:
```bash
sudo nano /etc/netplan/01-network-manager-all.yaml
```

In the file, input this:
```
# lets NetworkManager...manage all the interfaces
network:
  version: 2
  renderer: NetworkManager
```

This basically tells your operating system to use NetworkManager to manage all of its interfaces.

Now that we're configured out operating system, we'll go ahead and configure network manager itself through editing its config file:
```bash
sudo nano /etc/NetworkManager/NetworkManager.conf
```

In the file you'll want it to look like this:
```
[main]
plugins=ifupdown,keyfile
dns=systemd-resolved

[ifupdown]
managed=false

[device]
wifi.scan-rand-mac-address=no
```

By adding `dns=systemd-resolved`, it allows use to avoid complicating the install with swapping around DNS resolvers.

Ok, so now your system should be ready for you to start to swap your services.

First you'll want to do this:
```bash
systemctl mask systemd-networkd-wait-online.service
```

This will stop your device from waiting for you network to be connected before starting up. It also stops `systemd-networkd` from getting started by it.

Now you'll want to disable and then stop the `systemd-networkd` service:
```bash
sudo systemctl disable systemd-networkd
sudo systemctl stop systemd-networkd
```

Then enable the NetworkManager service and restart your device:
```bash
sudo systemctl enable NetworkManager
```

Once your device turns back on, run `nmcli d` to list your interfaces. It'll look like this:
```bash
user@device:~$ nmcli d
DEVICE         TYPE      STATE                   CONNECTION
eth0           ethernet  connected               Wired connection 1
tailscale0     tun       connected (externally)  tailscale0
wlan0          wifi      connected               netplan-wlan0-bill-wi the science-fi 5g
p2p-dev-wlan0  wifi-p2p  disconnected            --
lo             loopback  unmanaged               --
```

If when you run `nmcli d` it shows that most of your interfaces are unmanaged, then there's a problem. Something is causing NetworkManager to not manage your interfaces. To be honest, that may mean there's a problem with the guide. Please create an issue in the repo and let me know if it's not working.

Now NetworkManager is working!

Head over [here](./using-network-manager-in-the-cli) if you want to get an idea of how to use NetworkManager.