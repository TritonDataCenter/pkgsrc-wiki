## Contents

1. <a href="#introduction">Introduction</a>
1. <a href="#os-setup">OS setup</a>
    1. <a href="#os-setup-smartos">SmartOS</a>
    1. <a href="#os-setup-osx">Mac OS X</a>
    1. <a href="#os-setup-ubuntu-linux">Ubuntu Linux</a>
    1. <a href="#os-setup-redhat-linux">RedHat Linux</a>
1. <a href="#fetch-repositories">Fetch Repositories</a>
1. <a href="#create-sandbox">Create Sandbox</a>

<a name="introduction"/>

## Introduction

In order to get started with package development it is recommended that you use
the [pkgbuild](https://github.com/joyent/pkgbuild) infrastructure.  This
provides scripts and configuration files used by Joyent to produce the official
binary packages, and helps to ensure your environment is similar.

<a name="os-setup"/>

## OS setup

Follow the instructions below for your target OS.

<a name="os-setup-smartos"/>

### SmartOS

For SmartOS users we provide a `pkgbuild` image.  This contains everything you
need to get going quickly.

Use the image version which matches the package set you want to build.  For
example, to build packages for 16.4.x/2016Q4 images use the latest 16.4.x
pkgbuild image.  If you want to develop against pkgsrc trunk, then use
whichever the most recent release available is.

```console
$ imgadm update
$ imgadm avail | awk '/pkgbuild.*16.4/ {print}'
0d649af0-e6ed-11e6-8689-7fb3356bad96 pkgbuild 16.4.1 smartos zone-dataset 2017-01-30
$ imgadm import 0d649af0-e6ed-11e6-8689-7fb3356bad96
```

Create a zone using the chosen image uuid, giving it plenty of RAM and quota if
you are planning to build large packages.  For example:

```json
{
  "brand": "joyent",
  "image_uuid": "0d649af0-e6ed-11e6-8689-7fb3356bad96",
  "alias": "pkgbuild-16-4",
  "max_physical_memory": 8192,
  "quota": 16,
  "resolvers": [
    "8.8.8.8"
  ],
  "dns_domain": "local",
  "nics": [
    {
      "nic_tag": "admin",
      "ip": "dhcp"
    }
  ]
}
```

```console
$ vmadm create -f pkgbuild.json
```

<a name="os-setup-osx"/>

### Mac OS X

Create the pbulk user, used for unprivileged builds.  The easiest way to do
this is via the System Preferences dialog, but if you wish to use the command
line then something like might work (adjust UID for your system):

```console
$ user="pbulk"
$ uid=500
$ gid=20
$ comment="pbulk user"

$ dscl . -create /users/$user RecordName $user
$ dscl . -create /users/$user RecordType dsRecTypeNative:users
$ dscl . -create /users/$user UniqueID $uid
$ dscl . -create /users/$user PrimaryGroupID $gid
$ dscl . -create /users/$user NFSHomeDirectory "/Users/$user"
$ dscl . -create /users/$user UserShell "/bin/bash"
$ dscl . -create /users/$user Comment "$comment"
$ dscl . -delete /users/$user AuthenticationAuthority
$ dscl . -create /users/$user Password '*'
$ dscl . -create /users/$user IsHidden 1

$ mkdir /Users/pbulk
$ chown pbulk:staff /Users/pbulk
```

Mac OS X can run into issues with case-insensitive file systems, so it is often
worth creating a separate file system image on which to store pkgsrc.

```console
$ hdiutil create -fs JHFS+X -size 10g -type SPARSE -volname "data" data
$ sudo hdiutil attach -mountpoint /data data.sparseimage
```

This directory will need to be NFS exported for the sandboxes to access it, as
Mac OS X does not ship with any adequate null/bind mount options.

```console
$ cat >/etc/exports <<EOF
/data				-alldirs -maproot=root 127.0.0.1
/private/var/spool/postfix	-maproot=root 127.0.0.1
EOF
```

Check that the directories are exported.  The NFS daemon detects changes to
`/etc/exports` and will update mounts automatically.

```console
$ showmount -e
Exports list on localhost:
/private/var/spool/postfix		127.0.0.1
/data					127.0.0.1
```

If you are running macOS Sierra 10.12.4 or newer you will unfortunately need to
[disable System Integrity
Protection](https://www.howtogeek.com/230424/how-to-disable-system-integrity-protection-on-a-mac-and-why-you-shouldnt/)
as Apple has restricted the ability to update the mDNS service which is
required for DNS resolution inside build chroots.

<a name="os-setup-ubuntu-linux"/>

### Ubuntu Linux

Perform a full upgrade.

```console
$ apt-get update
$ apt-get dist-upgrade
$ apt-get autoremove
```

Install required packages.  `git` is part of `build-essential` on 16.04 or later.

```console
$ apt-get install build-essential git mailutils postfix
```

If using the Joyent Ubuntu image rename the `/mnt` mount to provide `/data`.

```console
: Switch /mnt to /data if required
$ sed -ie 's,/mnt,/data,' /etc/fstab
```

Create the pbulk user, used for unprivileged builds.

```console
$ useradd -U -m -s /bin/bash -c "pbulk user" pbulk
```

Set hostname if required

```console
$ vi /etc/hostname
pkgsrc-pbulk.local

$ reboot
```

<a name="os-setup-redhat-linux"/>

### RedHat Linux

Perform a full upgrade.

```console
$ yum update
```

Install required packages.

```console
$ yum install bzip2 ed gcc gcc-c++ git psmisc screen
```

Create the pbulk user, used for unprivileged builds.

```console
$ useradd -U -m -s /bin/bash -c "pbulk user" pbulk
```

Set hostname if required.

```console
: EL 6.x
$ vi /etc/sysconfig/network
HOSTNAME=pkgsrc-pbulk.local

: EL 7.x
$ hostnamectl set-hostname pkgsrc-pbulk.local

$ reboot
```

<a name="fetch-repositories"/>

## Fetch repositories

Fetch both the [pkgsrc](https://github.com/joyent/pkgsrc) and
[pkgbuild](https://github.com/joyent/pkgbuild) repositories.  If you are using
the SmartOS pkgbuild image these repositories will have already been cloned, so
you may just want to do a `git pull` instead.

The `/data` prefix is somewhat hardcoded at this point, though with some work
you can use a different prefix if necessary.  This is however currently
undocumented).

```console
: For OSX/Linux
$ mkdir -p /data
$ cd /data
$ git clone https://github.com/joyent/pkgsrc.git
$ git clone https://github.com/joyent/pkgbuild.git

: For SmartOS
$ (cd /data/pkgbuild; git pull)
$ (cd /data/pkgsrc; git pull)
```

Add the `pkgbuild/scripts` directory to your path.

```console
: For OSX/Linux.  SmartOS 'pkgbuild' already has this by default.
$ PATH=$PATH:/data/pkgbuild/scripts
```

For OS X you will want to track the `joyent/osx/trunk` branch, i.e.:

```console
$ cd /data/pkgsrc
$ git checkout joyent/osx/trunk
$ git submodule init
$ git submodule update
```

While for Linux you don't need to do anything and can use the default `trunk`.

## Fetch bootstrap and packages

Darwin-specific for now.

```console
$ mkdir -p /data/packages/Darwin/bootstrap-pbulk
$ cd /data/packages/Darwin/bootstrap-pbulk
$ for bs in i386 pbulk32 pbulk64 x86_64; do
>   curl -O https://pkgsrc.joyent.com/packages/Darwin/bootstrap-pbulk/bootstrap-trunk-${bs}.tar.gz
> done
```

<a name="create-sandbox"/>

## Create Sandbox

The pkgbuild repository aims to recreate the same environment that produces the
official Joyent binaries.  The `run-sandbox` script prepares a chroot
environment with everything set up ready to build packages.  It takes a single
argument which is the pkgbuild configuration to use, based on one of the
directories under `/data/pkgbuild/conf`.

Some examples:

```console
run-sandbox 2016Q4-x86_64       # Create a 64-bit SmartOS sandbox for 16.4.x
run-sandbox 2016Q4-i386         # Create a 32-bit SmartOS sandbox for 16.4.x
run-sandbox 2017Q1-tools        # Create a GZ "tools" SmartOS sandbox for 17.1.x
run-sandbox osx-trunk-x86_64    # Create a 64-bit OS X sandbox
run-sandbox linux-trunk-i386    # Create a 32-bit Linux sandbox
```

Once you have successfully created a sandbox you can move onto any of the other
package development documents.
