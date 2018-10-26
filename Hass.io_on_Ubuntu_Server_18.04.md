# Installing hass.io on Ubuntu Server 18.04

## Prerequisites

Install [Ubuntu Server 18.04.1 LTS](https://www.ubuntu.com/download/server)

* For instructions on how to install Ubuntu Server, [see Canonicals documentation here.](https://tutorials.ubuntu.com/tutorial/tutorial-install-ubuntu-server#0)
* For instructions on how to make a installable USB key, see:
  * [Windows](https://tutorials.ubuntu.com/tutorial/tutorial-create-a-usb-stick-on-windows#0)
  * [macOS](https://tutorials.ubuntu.com/tutorial/tutorial-create-a-usb-stick-on-macos#0)
  * [Ubuntu](https://tutorials.ubuntu.com/tutorial/tutorial-create-a-usb-stick-on-ubuntu#0)

## Installing hass.io

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

## Set up static IP

Follow the commands below, replacing the information in `<these>` with your own, removing the `< & >'s`.

```bash
sudo nano /etc/netplan/50-cloud-init.yaml
# alter the file to reflect the following

# Example: Replace <IP_ADDRESS>, <CIDR_MASK>, <GATEWAY_IP> with your info.
```

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
    enp0s25:
      dhcp4: false
      addresses: [<IP_ADDRESS>/<CIDR_MASK>]
      gateway4: <GATEWAY_IP>
      nameservers:
        addresses: [<GATEWAY_IP>,8.8.8.8,9.9.9.9]
```

Reboot the server to apply the configuration.

## Finishing Up

```text
Install the community IDE addon.

Configure, start, and open the IDE.

Use the IDE to upload the snapshot to the "backup" directory


Open Hass.io > Snapshots

Click refresh in upper-right

Click restore backup and tick all boxes (except hass version?)

After it is complete, log in and verify everything is working
```
