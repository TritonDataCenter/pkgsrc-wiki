## Contents

1. <a href="#introduction">Introduction</a>
1. <a href="#navigating-pkgsrc">Navigating pkgsrc</a>
1. <a href="#configuration-files">Configuration Files</a>
1. <a href="#package-options">Package Options</a>
1. <a href="#building-a-package">Building A Package</a>
1. <a href="#installing-a-package">Installing A Package</a>
1. <a href="#cleanup">Cleanup</a>
1. <a href="#summary">Summary</a>

<a name="introduction"/>

## Introduction

Our goal at [Joyent](http://www.joyent.com/) is that our binary packages
fulfill all of our users' needs.  This isn't always possible, however - users
may want packages we do not yet provide, or build with different options.

To satisfy those demands, it should instead be reasonably straight-forward for
users to build their own packages, and this guide hopefully provides all the
information for them to do just that.

This guide assumed that you have followed the
[setup](/joyent/pkgsrc/wiki/pkgdev:setup) document and are logged into an
appropriate `run-sandbox` session.

<a name="navigating-pkgsrc"/>

## Navigating pkgsrc

pkgsrc is organised into categories, with all packages following the
`pkgsrc/<category>/<package>` layout, and everything is driven with `bmake`,
the BSD implementation of `make(1)`.  We will choose `nmap` as an example, as
it will show a couple of things that need to be covered.

First, finding it.  Often the easiest way is with a simple glob:

```console
$ cd /data/pkgsrc
$ ls -d */*nmap*
net/nmap
```

If you want a more featured search, you can do:

```console
$ bmake search key=nmap
```

though the first time you run this it creates the `INDEX` file it requires, and
that can take a long time.  Another option is to use the
[pkgsrc.se](http://pkgsrc.se/) web interface.

Once you have selected a package, `cd` into its directory in order to perform
any further actions.

```console
$ cd net/nmap
```

<a name="configuration-files"/>

## Configuration Files

There are three main configuration files to be aware of when building packages.

The primary configuration file for building packages is `mk.conf`.  This file
is stored relative to `PKG_SYSCONFDIR` which differs depending on the OS and
package set, so to find the correct location for the sandbox you have created
you can run (assuming you are in a package directory):

```console
$ echo $(bmake show-var VARNAME=PKG_SYSCONFDIR)/mk.conf
```

Changes made to this file will be lost upon exiting the sandbox, so it is
useful for temporary changes which you do not wish to retain.

This primary `mk.conf` then includes a secondary pkgbuild-specific `mk.conf`
configuration file which contains the bulk of the configuration for the chosen
pkgbuild.  This is stored in `/data/pkgbuild/conf/<pkgbuild>/mk.conf.  As this
file is maintained in the `pkgbuild.git` repository it's best not to make
changes to it.

Finally, the pkgbuild `mk.conf` includes an optional `mk.conf.local` file
located in the same directory, so if you wish to make permanent changes then
they should go in this file.

So, for example, with a 2016Q4-x86_64 package set you would have:

```
/opt/local/etc/mk.conf
/data/pkgbuild/conf/2016Q4-x86_64/mk.conf
/data/pkgbuild/conf/2016Q4-x86_64/mk.conf.local
```

The reason for having separate files is because the primary `mk.conf` is part
of the bootstrap kit, and so cannot be easily edited.  By having per-pkgbuild
configuration files we can make changes to the build easily via git without
having to re-generate and distribute new bootstrap kits.

<a name="package-options"/>

## Package Options

Next, let's look at any options the package supports.

```console
$ bmake show-options
```

If the package supports build options, as `net/nmap` does, you'll see
something like:

```
Any of the following general options may be selected:
        inet6    Enable support for IPv6.
        lua      Enable Lua support.
        ndiff    Enable tool to compare Nmap scans.
        zenmap   Enable nmap GUI frontend.

These options are enabled by default:
        inet6

These options are currently enabled:
        inet6

You can select which build options to use by setting PKG_DEFAULT_OPTIONS
or PKG_OPTIONS.nmap.
```

These options are configured in `mk.conf`, so either add it to the primary
`mk.conf` if you wish to just test them temporarily, or add them to
`mk.conf.local` as described above for a more permanent change:

```make
PKG_OPTIONS.nmap+=	ndiff
```

## Building A Package

Now finally, we can go ahead and build the package.  The output from this will
be long, so you may want to `tee` it to a file for reviewing:

```console
$ bmake 2>&1 | tee /var/tmp/nmap.log
```

Assuming this completes ok, you should note the main stages that make up a
package build:

* __`bootstrap-depends`__ comes first, and installs all the dependencies
  required for pkgsrc to get started.  For example, `pkgtools/digest` is
  installed to calculate the `SHA1`, `RMD160`, and `SHA512` checksums of the
  source tarball and any package patches.

```
=> Bootstrap dependency digest>=20010302: NOT found
=> Verifying bin-install for ../../pkgtools/digest
===> Binary install for digest>=20010302
=> Installing digest>=20010302 from /data/packages/SmartOS/2016Q4/x86_64/All;http://0.0.0.0:8080/packages/SmartOS/2016Q4/x86_64//All
digest-20160304 successfully installed.
...
```

* __`fetch`__ and __`checksum`__ then run to download the source tarball for
  this particular package, and then verify the checksum matches that stored by
  pkgsrc, to ensure it was downloaded from a good source:

```
=> Fetching nmap-7.40.tar.bz2
=> Total size: 9043221 bytes
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 8831k  100 8831k    0     0  1634k      0  0:00:05  0:00:05 --:--:-- 1814k
=> Checksum SHA1 OK for nmap-7.40.tar.bz2
=> Checksum RMD160 OK for nmap-7.40.tar.bz2
=> Checksum SHA512 OK for nmap-7.40.tar.bz2
```

* __`depends`__ then installs all packages required for both build and runtime
  for the package in question:

```
=> Tool dependency libtool-base>=2.4.2nb9: NOT found
=> Verifying bin-install for ../../devel/libtool-base
===> Binary install for libtool-base>=2.4.2nb9
=> Installing libtool-base>=2.4.2nb9 from /data/packages/SmartOS/2016Q4/x86_64/All;http://0.0.0.0:8080/packages/SmartOS/2016Q4/x86_64//All
libtool-base-2.4.2nb13 successfully installed.
```

* __`extract`__ and __`patch`__ then unpack the source and apply any pkgsrc
  patches to the package.  The patches are located in the `patches/`
  sub-directory of each package:

```
===> Extracting for nmap-7.40
===> Patching for nmap-7.40
=> Applying pkgsrc patches for nmap-7.40
=> Verifying /data/pkgsrc/net/nmap/patches/patch-configure
=> Applying pkgsrc patch /data/pkgsrc/net/nmap/patches/patch-configure
Hmm...  Looks like a unified diff to me...
The text leading up to this was:
--------------------------
|$NetBSD: patch-configure,v 1.1 2015/11/20 15:37:40 adam Exp $
|
|External liblinear must be configured later.
|
|--- configure.orig     2015-11-10 04:26:26.000000000 +0000
|+++ configure
--------------------------
Patching file configure using Plan A...
Hunk #1 succeeded at 6242 (offset 50 lines).
Hunk #2 succeeded at 6897 (offset 2 lines).
done
...
```

* The bulk of the build is performed by __`configure`__ and __`build`__ which
  for most software will consist of `./configure && make`.

```
===> Configuring for nmap-7.40
...
Configured with: ndiff nping openssl ncat
Configured without: localdirs zenmap lua nmap-update
Type make (or gmake on some *BSD machines) to compile.
===> Building for nmap-7.40
...
gmake[1]: Leaving directory '/home/pbulk/build/net/nmap/work/nmap-7.40/nping'
```

<a name="installing-a-package"/>

## Installing A Package

Assuming that the build completed successfully, you can now call the `install`
target.  This installs the software to a temporary `DESTDIR` directory, and
then creates a binary package from that.  The binary package is then installed
into the real `PREFIX` using `pkg_add`:

```
$ bmake install
===> Installing for nmap-7.40
...
=> Automatic manual page handling
=> Generating post-install file lists
=> Checking file-check results for nmap-7.40
=> Checking for non-existent script interpreters in nmap-7.40
=> Checking file permissions in nmap-7.40
=> Checking for missing run-time search paths in nmap-7.40
=> Checking for work-directory references in nmap-7.40
=> Creating binary package /home/pbulk/build/net/nmap/work/.packages/nmap-7.40.tgz
===> Installing binary package of nmap-7.40
```

You can now verify it is installed, and test it:

```console
$ type nmap
nmap is /opt/local/bin/nmap

$ nmap -v
Starting Nmap 7.40 ( https://nmap.org ) at 2017-05-30 12:22 UTC
Read data files from: /opt/local/share/nmap
WARNING: No targets were specified, so 0 hosts scanned.
Nmap done: 0 IP addresses (0 hosts up) scanned in 0.15 seconds
           Raw packets sent: 0 (0B) | Rcvd: 0 (0B)
```

Note that the binary package was created under `/home/pbulk`.  This is a
temporary directory which is destroyed when you exit the sandbox.  In order to
save the package to a permanent location you need to call the `package` target.
Note though that this will overwrite any existing package that may already be
stored there.

```console
$ bmake package
=> Bootstrap dependency digest>=20010302: found digest-20160304
===> Building binary package for nmap-7.40
=> Creating binary package /data/packages/SmartOS/2016Q4/x86_64/All/nmap-7.40.tgz
```

You can now install the package outside of the sandbox using:

```console
$ pkg_add /data/packages/SmartOS/2016Q4/x86_64/All/nmap-7.40.tgz
```

<a name="cleanup"/>

## Cleanup

The quickest way to clean up is to simply exit the sandbox.  This will destroy
any non-shared directories and remove the sandbox completely.

If you prefer to just clean up the build artefacts, for example if you are
using the sandbox to build more packages but do not have a lot of space, you
can use the `clean` and `clean-depends` targets.

```console
$ bmake clean clean-depends
```

Sometimes though it's easier (and faster) to just wipe out the build area
completely.  This is configured by the `WRKOBJDIR` variable, so:

```console
$ bmake show-var VARNAME=WRKOBJDIR
/home/pbulk/build
$ rm -rf /home/pbulk/build/*
```

<a name="summary"/>

## Summary

This should hopefully be enough to get you started, but is only a small example
of what pkgsrc can do and how it is configured.  For more in-depth information
please refer to [the pkgsrc guide](https://www.netbsd.org/docs/pkgsrc/).
