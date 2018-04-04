Title: conjure-up | User manual
TODO: Needs a considerable overhaul
      Document should be renamed
table_of_contents: True

# User Manual

**conjure-up** is a thin layer spanning a few different underlying technologies
- [Juju][juju], [MAAS][maas] and [LXD][lxd].

**conjure-up** provides you with a streamlined, turnkey solution. In order to
provide that streamlined approach, **conjure-up** makes use of processing
scripts.

Processing scripts give you the flexibility to alter LXD profiles in order to
expose additional network interfaces to Neutron services, import images into
Glance once the service is available, or notifying the Deployment status screen
that your solution is ready and can be viewed at a specific URL.

With these powerful concepts you can package up the solution that can then be
provided to coworkers who can easily deploy your solutions in any Public
Cloud, MAAS, or LXD.

## Getting started

## Hardware requirements

For **Public Cloud** deployments hardware requirements (*constraints*) are
handled by the Spell authors and will automatically be allocated during deploy.

For **localhost** deployments the following setup is recommended:

- 2 cores
- 16G RAM
- 32G Swap
- 250G SSD

## Update your system

It is always recommended to have the latest packages installed prior to running `conjure-up`:

```
sudo apt update
sudo apt upgrade
```

## Installing conjure-up

`conjure-up` is available on Ubuntu Xenial 16.04 LTS and macOS

### Ubuntu
```bash
sudo snap install conjure-up --classic
```
!!! Note:
    If above command fails you’ll want to make sure **snapd** is installed with
    `sudo apt install snapd`

### macOS
```
brew install conjure-up
```

## Beta and development versions

If you want to preview of the next release, the latest beta version can be
installed with the following command:

```bash
sudo snap install conjure-up --classic --beta
```

For the most recent changes, install the `edge` release:

```bash
sudo snap install conjure-up --classic --edge
```

If you have **conjure-up** already installed, you can update to a different
snap channel with:

```bash
sudo snap refresh conjure-up --classic --edge
```
or

```
sudo snap refresh conjure-up --classic --beta
```

## Users of LXD

**conjure-up** requires that the minimum version of LXD be **3.0.0**. Additionally,
LXD should be configured prior to running.

### Install LXD

To install LXD run the following:

```
sudo snap install lxd
/snap/bin/lxd init  #  Notice there is NO __sudo__ prepended here.
```

[Snaps][snappy] are the recommended installation method. In upcoming Ubuntu
releases the snap version of LXD will be the only recommended way of installing
and using LXD. For the best experience, it is recommended to migrate from the **deb**
LXD packaging:


```
/snap/bin/lxd.migrate
```

This will move all container specific data to the snap version and clean up the
unused debian packages.

**Ubuntu 16.04 for Desktops**

In order to access the LXD service your **$USER** will need to be apart of the **lxd** group. To add your **$USER** to lxd group perform the following:


```
sudo usermod -a -G lxd $USER
newgrp lxd
```

!!! Note:
    This only allows the current shell to have access to the **lxd**
    group. The recommended way is to completely logout of your system so that the
    **lxd** group can be properly applied.

**Ubuntu 16.04 for Servers**

By default, Ubuntu Server has the **lxd** group associated with your default **$USER**. To verify, run the following:

```
id
uid=1000(ubuntu) gid=1000(ubuntu) groups=1000(ubuntu),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),113(lpadmin),128(sambashare),129(lxd)
```

If not, simply re-run the `usermod` and `newgrp` commands:

```
sudo usermod -a -G lxd $USER
newgrp lxd
```

### LXD Storage

At least one storage pool must be created and the default profile be set to use that storage device:

```
/snap/bin/lxc storage list
```

```
+---------+-------------+--------+------------------------------------------------+---------+
|  NAME   | DESCRIPTION | DRIVER |                     SOURCE                     | USED BY |
+---------+-------------+--------+------------------------------------------------+---------+
| default |             | dir    | /var/snap/lxd/common/lxd/storage-pools/default | 1       |
+---------+-------------+--------+------------------------------------------------+---------+
```

```
/snap/bin/lxc storage show default
```

```
config:
  source: /var/snap/lxd/common/lxd/storage-pools/default
description: ""
name: default
driver: dir
used_by:
- /1.0/profiles/default
```

### LXD Networking

For localhost deployments, LXD must have a network bridge defined:

```
/snap/bin/lxc network create lxdbr0 ipv4.address=auto ipv4.nat=true ipv6.address=none ipv6.nat=false
```

!!! Note:
    conjure-up does not support IPv6 at this time.

To verify that a bridge is configured properly you will need to inspect the config:

```
/snap/bin/lxc network show lxdbr0
```

```
config:
  ipv4.address: 10.101.64.1/24
  ipv4.nat: "true"
  ipv6.address: none
  ipv6.nat: "false"
description: ""
name: lxdbr0
type: bridge
used_by: []
managed: true
```

You will also want to make sure that the LXD default profile is set to use **lxdbr0** as its bridge:

```
/snap/bin/lxc profile show default
```

```
config: {}
description: Default LXD profile
devices:
  eth0:
    nictype: bridged
    parent: lxdbr0
    type: nic
  root:
    path: /
    pool: default
    type: disk
name: default
used_by: []
```

!!! Note:
    If this is a brand new LXD install and your profile does not look like the
    one above, run `/snap/bin/lxc profile edit default` and make the necessary
    adjustments.

### Verify container creation and network accessibility

```
lxc launch ubuntu:16.04 u1
lxc exec u1 ping ubuntu.com
```

Once satisfied your container can reach out to the internet, you can stop and remove that container:

```
lxc stop u1
lxc delete u1
```


## Summon a Spell

To deploy solutions such as Kubernetes you will summon a spell:

```bash
conjure-up kubernetes
```

To see a list of all available spells run:

```bash
conjure-up
```

!!! Note:
    Several remote locations are supported - please see [Advanced Spell
    Summoning][advancedspells] for further details

## Uninstalling

To remove deployments:

```bash
conjure-down
```

To uninstall **conjure-up** itself:

```bash
sudo snap remove conjure-up
```

<!-- LINKS -->
[juju]: https://jujucharms.com
[maas]: https://maas.io/
[lxd]: https://linuxcontainers.org/lxd/
[trusty]: http://releases.ubuntu.com/14.04/
[xenial]: http://releases.ubuntu.com/16.04/
[snappy]: https://snapcraft.io/
[applist]: ./index.md#application-list
[advancedspells]: ./usage.md

<!-- IMAGES -->
