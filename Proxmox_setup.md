<ins>Table of contents</ins>
- [Proxmox setup instructions](#proxmox-setup)
- [NAS Info](#nfs-share-setup)
- [Proxmox Error](#Proxmox-issues-with-Permission-denied-invalid-PVE-ticket-401)

# Proxmox setup

### step 1

Boot to USB thumb drive

### step 2

Use ZFS share during installation

### step 3

Join host into a cluster

### step 4

Add NFS share to the cluster storage

### step 5

Download the ISO and container templates

### step 6

On each node:

```
nano /etc/apt/sources.list
```

add:

```
deb [http://download.proxmox.com/debian](http://download.proxmox.com/debian) stretch pve-no-subscription
```

### step 7

```
nano /etc/apt/sources.list.d/pve-enterprise.list
```

comment out

### step 8

```
apt-get update && apt-get dist-upgrade -y
```

Reboot

### step 9

If you want to add a USB drive as storage that is connected to the host for the VM/containers to use. (Optional)

Adding USB to each Node

```
list disk
```

```
fdisk -l
```

#step 10

```
mkdir /usb-drive
```

Mount Directory
```
mount /dev/sdb1 /usb-drive
```


add the USB drive to boot
```
nano /etc/fstab
```

```
/dev/sdb1       /usb-drive           vfat    defaults        0       0
```

> This has broken the boot for Node 3 more than once

### step 11

In datacenter menu > storage > add directory

Use the mount path for each USB (mount path must be unique for each node)

Add restriction to only use on that node (otherwise size is not correctly showing on other nodes)

Remove nodes:

Login to a node you do NOT want to remove.

Connect to Shell

```
pvecm delnode [NODE_NAME]
```

### step 12

In the online GUI select the Node PC name. Then go to Updates and Repositories. 

Disable the pve-enterprise

Click Add and select No-subscription

### step 13

To disable the pop up on login about no subscription:

First connect to the server using SSH, as this is easier because you can copy and paste

Now switch to the folder where the file is stored

```
cd /usr/share/javascript/proxmox-widget-toolkit
```

Next back up the file in case we mess things up

```
cp proxmoxlib.js proxmoxlib.js.bak
```

And then edit the file as we need to make a change

```
nano proxmoxlib.js
```

Use Ctrl-W to find and type in

```
if (res ==
```

Hit return and this should take you to a line like this
if (res === null || res === undefined || !res || res
                        .data.status.toLowerCase() !== 'active') {

Replace the line with this
if (false) {

Now save and exit using Ctrl-X

Basically we've changed this statement check to always return false so that it never triggers

Restart the PVE service for the changes to take effect

```
systemctl restart pveproxy
```

Now repeat this for all nodes

# upgrade PVE version

https://pve.proxmox.com/wiki/FAQ#faq-support-table

<make sure all VMs or Containers are turned off before starting. OR migrate them to another host>

use PVE Upgrade checklist

```
pve7to8
```

Check for updates 

```
apt update
apt dist-upgrade
pveversion
```

update all Repositories

```
sed -i 's/bullseye/bookworm/g' /etc/apt/sources.list
```

Edit the Repositories sources list:

```
nano /etc/apt/sources.list
```

delete everything in that file and replace with:
reference: https://pve.proxmox.com/wiki/Package_Repositories#_repositories_in_proxmox_ve

```
deb http://ftp.debian.org/debian bookworm main contrib
deb http://ftp.debian.org/debian bookworm-updates main contrib

# Proxmox VE pve-no-subscription repository provided by proxmox.com,
# NOT recommended for production use
deb http://download.proxmox.com/debian/pve bookworm pve-no-subscription

# security updates
deb http://security.debian.org/debian-security bookworm-security main contrib
```

run update again for applying the new changes:

```
apt update
apt dist-upgrade
```

Answer any prompts to complete the upgrade

Then repeat 1 host at a time.

# NFS Share setup
Permission allowed to the Micro cluster IPs ONLY. Full access Read/Write

Mount Path
```
/mnt/md0/NFS-Share
```
# Proxmox issues with Permission denied invalid PVE ticket 401

1. Delete These files:
- /etc/pve/pve-root-ca.pem
- /etc/pve/priv/pve-root-ca.key
- /etc/pve/nodes/NODE/pve-ssl.pem
- /etc/pve/nodes/NODE/pve-ssl.key
- /etc/pve/authkey.pub
- /etc/pve/priv/authkey.key
- /etc/pve/priv/authorized_keys
> replace NODE with the hostname

1. Run command
```
pvecm updatecerts -f
```

3. Run command
```
systemctl restart pvedaemon pveproxy
```

> tls_process_server_certificate: certificate verify failed (596)

```
cd /etc/pve
```

4. Add to corosync.confg:
```
token: 10000
```
