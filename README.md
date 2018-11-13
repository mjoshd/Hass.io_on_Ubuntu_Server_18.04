# Installing Hass.io on Ubuntu Server 18.04

## Prerequisites

Create a snapshot of your current Hass.io installation.

Install [Ubuntu Server 18.04.1 LTS](https://www.ubuntu.com/download/server)

* For instructions on how to install Ubuntu Server, [see Canonicals documentation here.](https://tutorials.ubuntu.com/tutorial/tutorial-install-ubuntu-server#0)
* For instructions on how to make a installable USB key, see:
  * [Windows](https://tutorials.ubuntu.com/tutorial/tutorial-create-a-usb-stick-on-windows#0)
  * [macOS](https://tutorials.ubuntu.com/tutorial/tutorial-create-a-usb-stick-on-macos#0)
  * [Ubuntu](https://tutorials.ubuntu.com/tutorial/tutorial-create-a-usb-stick-on-ubuntu#0)

## Installing Hass.io

Once logged in to the machine, open a terminal and run the following commands.

```bash
# Get a root shell.
sudo -s

# Add the universe repository and update.
add-apt-repository universe && apt-get update

# Install docker.io, avahi-daemon, and jq.
#   -y switch used to auto-accept, can be ommited if desired.
apt-get install docker.io avahi-daemon jq -y

# Update from repository and upgrade existing packages.
#   -y switch used to auto-accept, can be ommited if desired.
apt-get update && apt-get upgrade -y

# Download hassio_install script from github, and automatically execute it in a bash shell.
# You can navigate to this url in a web browser if you'd like to examine the script before running.
curl -sL https://raw.githubusercontent.com/home-assistant/hassio-build/master/install/hassio_install | bash
```

## Setting up a static IP

Determine the name of your network interface then reference the information below and alter the file to reflect similarly to the example provided.

```bash
# Display network interfaces.
ifconfig

# Open network configuration file for editing.
sudo nano /etc/netplan/50-cloud-init.yaml
```

Replace INTERFACE, IP_ADDRESS, CIDR_MASK and GATEWAY_IP with your details.

```yaml
# This file is generated from information provided by
# the datasource.  Changes to it will not persist across an instance.
# To disable cloud-init's network configuration capabilities, write a file
# /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg with the following:
# network: {config: disabled}
network:
  version: 2
  renderer: networkd
  ethernets:
    INTERFACE:
      dhcp4: false
      addresses: [IP_ADDRESS/CIDR_MASK]
      gateway4: GATEWAY_IP
      nameservers:
        addresses: [GATEWAY_IP,8.8.8.8,9.9.9.9]
```

Reboot the server to apply the configuration.

```bash
# Apply the config
sudo netplan try

# Press ENTER to accept the changes.

# Reboot the host.
sudo reboot now
```

## Finishing Up

* Install either the Samba addon or the community IDE addon.

* Configure, start, and open the chosen addon.

* Use the addon to upload the snapshot to the "backup" directory.

* Open the side-bar then click Hass.io > Snapshots.

* Click the Reload icon in the upper-right corner of the pane.

* Click the desired snapshot, ensure all the boxes are filled, then click 'Wipe & Restore'.

* After it is complete, log in and verify everything is working.

## Notes

* Not too long after issuing the curl command to install Hass.io there will be a notice in the terminal similar to 'trying again after 30 seconds'. Do not worry, this is normal and the process will continue on its own without intervention.

* If you install the Pi-hole addon and get errors related to 'address is already in use' when trying to start it, run the following commands in the Ubuntu host's terminal:

```bash
# Get a root shell.
sudo -s

# Open resoved.conf in nano.
nano /etc/systemd/resolved.conf

# Add the following line.
DNSStubListener=no

# Reboot the host.
reboot now
```
