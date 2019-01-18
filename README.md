# Installing Hass.io on Ubuntu Server 18.04

## Prerequisites

Create a snapshot of your current Hass.io installation.

Install [Ubuntu Server 18.04.1 LTS](https://www.ubuntu.com/download/server)

* For instructions on how to install Ubuntu Server, [see Canonicals documentation here.](https://tutorials.ubuntu.com/tutorial/tutorial-install-ubuntu-server#0)
* For instructions on how to make a installable USB key, see:
  * [Windows](https://tutorials.ubuntu.com/tutorial/tutorial-create-a-usb-stick-on-windows#0)
  * [macOS](https://tutorials.ubuntu.com/tutorial/tutorial-create-a-usb-stick-on-macos#0)
  * [Ubuntu](https://tutorials.ubuntu.com/tutorial/tutorial-create-a-usb-stick-on-ubuntu#0)


## Setting up a static IP

Use this information if you did not set up a static IP during the inital Ubuntu Server installation. 

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
    ethernets:
        INTERFACE:
            addresses:
            - IP_ADDRESS/CIDR_MASK
            dhcp4: false
            gateway4: GATEWAY_IP
            nameservers:
                addresses:
                - GATEWAY_IP
                - 1.1.1.1
                - 9.9.9.9
                - 8.8.8.8
    version: 2

```

Apply the configuration and reboot the server.

```bash
# Apply the config
sudo netplan try

# Press ENTER to accept the changes.

# Reboot the host.
sudo reboot now
```

## [Installing Hass.io](https://www.home-assistant.io/hassio/installation/#alternative-install-on-generic-linux-server)

Once logged in to the machine, open a terminal and run the following commands.

```bash
# Get a root shell.
sudo -i

# Add the universe repository and update.
add-apt-repository universe && apt-get update

# Install required packages.
#   -y switch used to auto-accept, can be ommited if desired.
apt-get install -y apparmor-utils apt-transport-https avahi-daemon ca-certificates curl dbus jq network-manager socat software-properties-common

# Install official Docker-CE
curl -fsSL get.docker.com | sh

# Update from repository again and upgrade existing packages.
#   -y switch used to auto-accept, can be ommited if desired.
apt-get update && apt-get upgrade -y

# Download hassio_install script from github, and automatically execute it in a bash shell.
# You can navigate to this url in a web browser if you'd like to examine the script before running.
curl -sL "https://raw.githubusercontent.com/home-assistant/hassio-build/master/install/hassio_install" | bash -s
```

## Important Note

If you are going to install either of the DNS Ad-blocking addons (AdGuard Home/Pi-hole) you will need to run the following commands in the Ubuntu host's terminal or you will get errors related to 'address/port is already in use' when trying to start it:

```bash
# Get a root shell.
sudo -i

# Disable systemd-resolved.service from auto-starting.
systemctl disable systemd-resolved.service

# Reboot the host.
reboot now
```

## Finishing Up

* Install either the Samba addon or the community IDE addon.

* Configure, start, and open the chosen addon.

* Use the addon to upload the snapshot to the "backup" directory.

* Open the side-menu then click Hass.io > Snapshots.

* Click the Reload icon in the upper-right corner of the pane.

* Click the desired snapshot, de-select Home Assistant (leave all other items selected), then click 'RESTORE SELECTED'.

* After it is complete, log in and verify everything is working.
