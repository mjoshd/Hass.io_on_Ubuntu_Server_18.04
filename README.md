# Installing Hass.io on Ubuntu Server 18.04

## Prerequisites

Create 2-3 snapshots of your current Hass.io installation.

## Install [Ubuntu Server 18.04.1 LTS](https://www.ubuntu.com/download/server)

* For instructions on how to install Ubuntu Server, [see Canonicals documentation here.](https://tutorials.ubuntu.com/tutorial/tutorial-install-ubuntu-server#0)
* For instructions on how to make a installable USB key, see:
  * [Windows](https://tutorials.ubuntu.com/tutorial/tutorial-create-a-usb-stick-on-windows#0)
  * [macOS](https://tutorials.ubuntu.com/tutorial/tutorial-create-a-usb-stick-on-macos#0)
  * [Ubuntu](https://tutorials.ubuntu.com/tutorial/tutorial-create-a-usb-stick-on-ubuntu#0)

## Set the system timezone

```bash
# Get a root shell.
sudo -s

# Display timezone list and find your timezone in the list.
timedatectl list-timezones

# Apply timezone settings, replacing YOUR_TIMEZONE with one from the list.
timedatectl set-timezone YOUR_TIMEZONE

# Verify timezone settings.
timedatectl

```

## Set up a static IP

* It is preferable to set a static IP duirng the OS installation. Use the information below if you need to create it afterward.

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
# Apply the config. Press ENTER again to accept the changes.
sudo netplan try

# Reboot the host.
sudo reboot now
```

## [Installing Hass.io](https://www.home-assistant.io/hassio/installation/#alternative-install-on-generic-linux-server)

Once logged in to the machine, open a terminal and run the following commands.

```bash
# Get a root shell.
sudo -i

# Add the universe repository.
add-apt-repository universe

# Update package lists and upgrade existing packages.
apt-get update && apt-get upgrade -y

# Install required software.
apt-get install -y apparmor-utils apt-transport-https avahi-daemon ca-certificates curl dbus jq network-manager socat software-properties-common

# Create DNS configuration file for Docker where GATEWAY_IP is your default gateway.
mkdir -p /etc/docker
echo '{"dns": ["GATEWAY_IP", "1.1.1.1", "9.9.9.9", "8.8.8.8"]}' > /etc/docker/daemon.json

# Install Docker.
curl -sSL https://get.docker.com | sh

# Install Hass.io
curl -sL "https://raw.githubusercontent.com/home-assistant/hassio-build/master/install/hassio_install" | bash -s
```

## Finishing Up

* Install either the Samba addon or the community IDE addon.

* Configure, start, and open the chosen addon.

* Use the addon to upload the largest snapshot to the "backup" directory.

* Open the side-menu then click Hass.io > Snapshots.

* Click the Reload icon in the upper-right corner of the pane.

* Click the desired snapshot, ensure all boxes are selected, then click 'WIPE & RESTORE'.

* After it is complete, log in and verify everything is working.

## Important Note

If you are going to use either of the DNS Ad-blocking addons (AdGuard Home/Pi-hole) follow these steps in order:

* Install the desired ad-blocking addon and note that after starting it there are errors related to 'address/port is already in use'. This is because Ubuntu has it's own DNS service running on port 53.

* Run the following commands in the Ubuntu host's terminal to disable it so the addon will be able to start successfully.

```bash
# Get a root shell.
sudo -s

# Disable systemd-resolved.service from auto-starting.
systemctl disable systemd-resolved.service

# Reboot the host.
reboot now
```