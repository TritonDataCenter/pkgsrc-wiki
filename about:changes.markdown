## Contents

1. <a href="#introduction">Introduction</a>
1. <a href="#pkgsrc-2017Q2">Changes in pkgsrc-2017Q2</a>
1. <a href="#pkgsrc-2017Q1">Changes in pkgsrc-2017Q1</a>
1. <a href="#pkgsrc-2016Q4">Changes in pkgsrc-2016Q4</a>
1. <a href="#pkgsrc-2016Q3">Changes in pkgsrc-2016Q3</a>
1. <a href="#pkgsrc-2016Q2">Changes in pkgsrc-2016Q2</a>
1. <a href="#pkgsrc-2016Q1">Changes in pkgsrc-2016Q1</a>
1. <a href="#pkgsrc-2015Q4">Changes in pkgsrc-2015Q4</a>
1. <a href="#pkgsrc-2015Q3">Changes in pkgsrc-2015Q3</a>
1. <a href="#pkgsrc-2015Q2">Changes in pkgsrc-2015Q2</a>
1. <a href="#pkgsrc-2015Q1">Changes in pkgsrc-2015Q1</a>
1. <a href="#pkgsrc-2014Q4">Changes in pkgsrc-2014Q4</a>
1. <a href="#pkgsrc-2014Q3">Changes in pkgsrc-2014Q3</a>
1. <a href="#pkgsrc-2014Q2">Changes in pkgsrc-2014Q2</a>
1. <a href="#pkgsrc-2014Q1">Changes in pkgsrc-2014Q1</a>

<a name="introduction"/>

## Introduction

With 18,000+ packages being constantly updated and changed, producing a full
ChangeLog would be an exercise in futility and not very helpful.  So instead we
try to summarise the most important changes in each quarterly release.

While this page is primarily for the illumos quarterly package sets, the
changes listed may be generic to pkgsrc and may not be available on illumos or
enabled in our binary packages.

<a name="pkgsrc-2017Q2"/>

## Changes in pkgsrc-2017Q2

- Number of binary packages available:
  + x86\_64: 16,500+
  + i386: 16,500+
  + multiarch: 14,500+

- New packages introduced (not all necessarily available as binary packages) include:
  + Firefox 54
  + GCC 7.1
  + MATE 1.18
  + Ruby 2.4
  + Ruby on Rails 4.2
  + TeX Live 2017
  + Thunderbird 52.1
  + Xen 4.8

- Package removals include:
  + Ruby 1.8
  + Ruby 2.1

- Infrastructure changes:
  + Implement optional new pkgtasks and init infrastructure for
    pkginstall.
  + Various enhancements and fixes for building with ccache.
  + Add support to USE\_LANGUAGES for newer C++ standards.
  + Enhanced support for security features: SSP, FORTIFY, and RELRO.
  + The official GitHub mirror of pkgsrc CVS has been migrated to
    https://github.com/NetBSD/pkgsrc, as a result the Joyent fork
    at https://github.com/joyent/pkgsrc has had to be reinitialised.

- Commit stats since 2017Q1:
  + 210 packages added
  + 43 packages removed
  + 1,780 package updates
  + 4,198 commits from 74 contributors

<a name="pkgsrc-2017Q1"/>

## Changes in pkgsrc-2017Q1

- Number of binary packages available:
  + x86\_64: 16,000+
  + i386: 16,000+
  + multiarch: 14,000+

- New packages introduced (not all necessarily available as binary packages) include:
  + Python 3.6
  + Nextcloud 11
  + Firefox 45.8.0 and 52.0.1
  + Gradle 3.4
  + pkg\_comp 2.0
  + qmail 1.03nb24 binary packages work, supporting common use cases
  + Many additional Python, Perl and Ruby modules
  + Many additional TeX Live 2016 packages

- Package removals include:
  + GCC 4.5, 4.6, and 4.7
  + Xen 3.1, 3.3, and 4.1

- Infrastructure changes:
  + Default version of Apache changed from 2.2 from 2.4 (this was already the
    case with our illumos packages).
  + Improvements to the curses.mk infrastructure with many more packages
    updated to support native curses/terminfo if available.

- Commit stats since 2016Q4:
  + 192 packages added
  + 25 packages removed
  + 1,458 package updates
  + 3,901 commits from 68 contributors

<a name="pkgsrc-2016Q4"/>

## Changes in pkgsrc-2016Q4

New LTS branch, supported until the release of pkgsrc-2019Q4.

- Number of binary packages available:
  + x86\_64: 15,000+
  + i386: 15,000+
  + multiarch: 14,000+

- New packages introduced (not all necessarily available as binary packages) include:
  + Tryton 4.2.0
  + Firefox 50.1.0
  + Go 1.7.4
  + MySQL 5.7.17
  + PostgreSQL 9.5.5
  + PHP 7.1.0

- Infrastructure changes:
  + cwrappers was enabled by default on Darwin, Linux, NetBSD, and
    SunOS. This replaces some a part of the infrastructure that was
    written in shell with a C implementation and speeds up the build.

- Commit stats since 2016Q3:
  + 159 packages added
  + 1,359 package updates
  + 3,172 commits from 72 contributors

<a name="pkgsrc-2016Q3"/>

## Changes in pkgsrc-2016Q3

- Number of binary packages available:
  + x86\_64: 15,000+
  + i386: 15,000+
  + multiarch: 13,500+

- New packages introduced (not all necessarily available as binary packages) include:
  + Emacs 25.1
  + Firefox 49.0
  + GCC 6.2.0
  + Go 1.7.1
  + KDE Frameworks 5.25.0
  + MySQL 5.7.15
  + PHP 7.1.0rc2
  + Rust 1.11
  + Many more TeX Live 2016 packages

- Package removals include:
  + Python 3.3
  + PHP 5.5

- Infrastructure changes:
  + The pkg\_install tools now support HTTPS, and both pkgin and pkg\_add now
    fetch packages over HTTPS by default.
  + Dovecot plugins are now separate packages instead of compile-time
    options, providing more flexibility for binary package users.
  + The new GFORTRAN\_VERSION variable allows the user to select which GCC
    package will supply the GNU Fortran implementation when PKGSRC\_FORTRAN
    is set to `gfortran`.  The default remains `48` (lang/gcc48).
  + Guile 2.0 is now the default, 1.8 is retained.
  + PHP modules are now automatically enabled at install time with
    per-module .ini files placed into the `PKG_SYSCONFDIR/php.d` directory.
  + PKGSRC\_KEEP\_BIN\_PKGS is now set to `yes` by default, meaning that
    `make install` will now save/overwrite binary packages to PACKAGES.
    Users who prefer to retain a distinction between `make install` and
    `make package`, for example to avoid overwriting known-good binary
    packages during testing, should set this back to `no` in mk.conf.
  + pkg\_tarup is no longer used for `make replace` in DESTDIR mode.
  + RUBY\_VERSION\_SUPPORTED is renamed \_RUBY\_VERSIONS\_ACCEPTED to match
    other multi-version support, and \*\_VERSIONS\_INCOMPATIBLE support is
    added to ruby and php.

- Commit stats since 2016Q2:
  + 276 packages added
  + 1,520 package updates
  + 4,016 commits from 64 contributors

<a name="pkgsrc-2016Q2"/>

## Changes in pkgsrc-2016Q2

- Number of binary packages available:
  + x86\_64: 15,000+
  + i386: 15,000+
  + multiarch: 12,500+

- New packages introduced (not all necessarily available as binary packages) include:
  + Ansible 2.1.0.0
  + Ardour 4.7
  + Code::Blocks 16.01
  + CodeLite 9.1
  + CoreCLR 1.0.0
  + Firefox 45.1.0
  + KDE Frameworks 5
  + LXDE 1.0
  + MATE 1.14.0
  + PostgreSQL Contrib
  + TeX Live 2016

- Infrastructure changes:
  + The tools package set has the beginnings of
    [RFD 42](https://github.com/joyent/rfd/tree/master/rfd/0042) support,
    allowing packages to install without useradd failures in the SmartOS Global
    Zone.
  + The default OpenJDK has been switched back to OpenJDK 7 for 32-bit, due to
    Oracle's deprecation of 32-bit support in OpenJDK 8.  64-bit will continue
    to default to OpenJDK 8.
  + OCaml is now available for 32-bit.
  + A new postgresql-contrib package has been introduced which includes all of
    the additional contrib PostgreSQL utilities.

- Commit stats since 2016Q1:
  + 301 packages added
  + 1,727 package updates
  + 3,861 commits from 73 contributors

<a name="pkgsrc-2016Q1"/>

## Changes in pkgsrc-2016Q1

The 50th quarterly pkgsrc branch!

- Number of binary packages available:
  + x86\_64: 15,000+
  + i386: 15,000+
  + multiarch: 13,000+

- New packages introduced (not all necessarily available as binary packages) include:
  + LLDB 3.8
  + MATE 1.14.0 (included from pkgsrc-wip)
  + PostgreSQL 9.5
  + Rust 1.8.0-dev for testing

- Infrastructure changes:
  + illumos packages are now built with -fstack-protector-strong to enable
    stack smashing protection for functions most at risk.
  + `MANZ=yes` has been enabled, ensuring manual pages are installed compressed.

- Commit stats since 2015Q4:
  + 204 packages added
  + 15 packages removed
  + 1,477 package updates
  + 4,477 commits from 75 contributors

<a name="pkgsrc-2015Q4"/>

## Changes in pkgsrc-2015Q4

New LTS branch, supported until the release of pkgsrc-2018Q4.

- Number of binary packages available:
  + x86\_64: 16,000+
  + i386: 15,000+
  + multiarch: 14,000+

- New packages introduced (not all necessarily available as binary packages) include:
  + PHP 7.0
  + Python 3.5

- Infrastructure changes:
  + The default illumos compiler is now GCC 4.9.3.
  + illumos packages are now built with "-gdwarf-2" to ensure debugging
    information is available, and the core set of bootstrap packages are no
    longer stripped.
  + clang-3.7.0 is provided as an optional compiler for illumos, and has been
    patched to build [over
    12,000](https://mail-index.netbsd.org/pkgsrc-bulk/2015/10/27/msg012139.html)
    packages successfully.
  + The proftpd package has been split up into separate packages, allowing users
    to simply choose which backends to install instead of using compiled-in
    defaults.

- Commit stats since 2015Q3:
  + 172 packages added
  + 58 packages removed, 7 with a successor
  + 1,185 packages updated
  + 3,418 commits from 73 contributors

<a name="pkgsrc-2015Q3"/>

## Changes in pkgsrc-2015Q3

- Number of binary packages available:
  + x86\_64: 14,500+
  + i386: 14,500+
  + multiarch: 13,500+

- New packages introduced (not all necessarily available as binary packages) include:
  + GCC 5.1
  + Various additional Xorg 1.17 packages
  + TeX Live 2015 additions and updates

- Infrastructure changes:
  + Default package versions have been bumped for a number of core packages:
    + Lua 5.1 -> 5.2
    + PostgreSQL 9.3 -> 9.4
    + PHP 5.5 -> 5.6
    + Ruby 1.9.3 -> 2.0.0
  + The postfix package has been split up into separate packages, allowing users
    to simply choose which backends to install instead of using compiled-in
    defaults.
  + All platforms now contain the pkgin enhancements detailed in [this blog
    post](http://www.perkin.org.uk/posts/reducing-ram-usage-in-pkgin.html).
  + OSX users no longer show up in the login window.

- Commit stats since 2015Q2:
  + 225 packages added
  + 27 packages removed, 8 with a successor
  + 1,392 packages updated
  + 3,696 commits from 71 contributors

<a name="pkgsrc-2015Q2"/>

## Changes in pkgsrc-2015Q2

- Number of binary packages available:
  + x86\_64: 14,500+
  + i386: 14,500+
  + multiarch: 13,500+

- New packages introduced (not all necessarily available as binary packages) include:
  + Boost 1.58.0
  + Emacs 24.5
  + MariaDB 5.5
  + Perl 5.22
  + TeX Live 2015
  + XFCE 4.12

- Infrastructure changes:
  + Packages and bootstrap kits are now available over HTTPS from
    <https://pkgsrc.joyent.com/>.
  + pkgin on illumos has significantly reduced memory requirements (for more
    information see [this blog
    post](http://www.perkin.org.uk/posts/reducing-ram-usage-in-pkgin.html)).
  + OSX packages are now installed under the `/opt/pkg` prefix instead of the
    previous `/usr/pkg` in order to be compatible with the "System Integrity
    Protection" feature introduced in El Capitan / 10.11.
  + OSX package defaults have been changed in order to provide fully-featured
    packages out of the box.

- Commit stats since 2015Q2:
  + 1,155 packages have been added.
  + 27 packages removed, 12 with a successor.
  + 2,015 packages updated.
  + 4,135 commits from 68 contributors

<a name="pkgsrc-2015Q1"/>

## Changes in pkgsrc-2015Q1

* OS X packages are now PGP signed and verified by default.
* OS X bootstraps now include the pkgsrc-gnupg-keys package for signature
  verification of the pkg-vulnerabilities file.
* PostgreSQL 9.4 has been added, PHP 5.3 has been removed.
* 216 packages have been added
* 2 packages have been renamed
* 46 packages removed, 11 with a successor
* 2,007 packages updated

The main pkgsrc-2015Q1 announcement is [here](http://mail-index.netbsd.org/pkgsrc-users/2015/04/14/msg021358.html).

<a name="pkgsrc-2014Q4"/>

## Changes in pkgsrc-2014Q4

* This release introduces Long Term Support, PGP signed packages, and some
  other enhancements to our illumos sets.  See [this blog
  post](http://www.perkin.org.uk/posts/pkgsrc-2014Q4-lts-signed-packages-and-more.html)
  for more details.
* This release sees the first set of 64-bit OS X packages, built on OS X
  Mavericks using clang, in addition to our existing 32-bit set.
* 156 packages have been added
* 4 packages have been renamed
* 48 packages removed, 9 with a successor
* 1,575 packages updated

The main pkgsrc-2014Q4 announcement is [here](http://mail-index.netbsd.org/pkgsrc-users/2015/01/02/msg020854.html).

<a name="pkgsrc-2014Q3"/>

## Changes in pkgsrc-2014Q3

* Bootstrap kits now include mozilla-rootcerts and pkg_alternatives installed
  and configured by default.
* 210 packages have been added
* 3 packages have been renamed
* 15 packages removed, 12 with a successor
* 1,123 packages updated

The main pkgsrc-2014Q3 announcement is [here](http://mail-index.netbsd.org/pkgsrc-users/2014/10/03/msg020427.html).

<a name="pkgsrc-2014Q2"/>

## Changes in pkgsrc-2014Q2

* 244 packages have been added
* 2 packages have been renamed
* 18 packages removed, 1 with a successor
* 1,085 packages updated

<a name="pkgsrc-2014Q1"/>

## Changes in pkgsrc-2014Q1

Here are the major changes in the pkgsrc-2014Q1 release:

### OpenSSL update

Everyone has heard of the Heartbleed attacks by now.  2014Q1 of course includes
the latest OpenSSL (1.0.1g) to fix this issue.

### Go 1.3beta1 for illumos 64-bit

Thanks to great work by [Aram Hăvărneanu](https://twitter.com/aramh) Go 1.3
will support illumos systems, and we've included the 1.3beta1 package in our
illumos x86_64 repository.  Please give it a spin and provide feedback so the
main 1.3 release can be great!

<a name="pkgsrc-2013Q4"/>

## Changes in pkgsrc-2013Q4

Here are the major changes in the pkgsrc-2013Q4 release:

### Default version changes

We have updated the default versions for a number of core packages, they are:

* Apache 2.4 (was 2.2)
* Lua 5.1 (was 5.2)
* MySQL 5.6 (was 5.5)
* PostgreSQL 9.3 (was 9.1)
* PHP 5.5 (was 5.4)

The reason for the Lua default going backwards is that 5.2 is incompatible in a
number of ways from 5.1, and there are still a reasonable number of modules
which do not yet work with 5.2.  We will revert back to 5.2 as default once
support is comparable.

We try to provide multiple versions of each of these packages, and many modules
are built for all versions, however some software can only be built against one
version, so these version bumps will ensure those packages use the latest
version.

In addition to these version defaults, we have changed the default
implementation of JPEG to libjpeg-turbo, which is an optimised implementation
of libjpeg using SIMD instructions.

### Disabled -fomit-frame-pointer in GCC

We have disabled the -fomit-frame-pointer option globally in GCC 4.7 on
illumos.  This micro-optimisation creates binaries which are useless for
debugging as the frame pointer is crucial for producing stack traces, and the
potential performance gains are simply not worth the loss of observability.

All of the illumos packages produced in pkgsrc-2013Q4 are built with this
compiler, so any packages which happened to use this option will now be built
correctly.

<a name="pkgsrc-2013Q3"/>

## Changes in pkgsrc-2013Q3

### GHC 7.6 now available

For Haskell users, we are pleased to announce that ghc 7.6.3 is now available
for all architectures.  Previously we were only able to offer the older 6.8.3
release and only for 32-bit, but thanks to the efforts of those in our
community, notably 'oddsignals' and
[Alain O'Dea](https://twitter.com/AlainODea) we have a working bootstrap kit
for the newer releases.

<a name="pkgsrc-2013Q2"/>

## Changes in pkgsrc-2013Q2

Here are the major changes in the pkgsrc-2013Q2 release:

### OpenJDK7 is now default

Thanks to the great work by SmartOS user 'jesse', we now have a working
OpenJDK7 on illumos, built with GCC.  This is now the default JRE/JDK, as we
are unable to provide updated sun-{jre,jdk} packages due to Oracle's more
restrictive redistribution policies.

The only user-visible change from this is that the Java binaries are prefixed
with `openjdk7-`, so call e.g. `openjdk7-java` instead of `java`, or
alternatively put `/opt/local/java/openjdk7/bin` at the front of your `$PATH`.

This allows co-existance with the legacy sun-{jre,jdk} packages.

### Desktop support

Thanks to many Xorg updates from Richard Palo, Xorg is now functional on
illumos, enabling many common desktop environments to now be used.

```console
: Install the meta-package containing Xorg
$ pkgin in modular-xorg

: On OmniOS these are required on top of the basic install.
$ pkg install driver/x11/xsvc developer/macro/cpp

: Also on OmniOS 'od' is located in a different location
$ sed -i -e 's,/usr/bin/od,/usr/gnu/bin/od,' /opt/local/bin/startx
```

A selection of desktops that are available:

GNOME 2.32 with Evolution and Firefox 22

```console
$ pkgin in gnome-session gnome-themes gnome-themes-extras \
           gnome-terminal gnome-backgrounds evolution

$ vi .xinitrc
#!/bin/sh
PATH=/opt/local/sbin:/opt/local/bin:$PATH
/opt/local/bin/gnome-session

$ startx

: Currently the pkgsrc firefox22 fails on startup, so for now use the
: pre-built binaries from Mozilla (with some library hacks).
$ curl -s http://releases.mozilla.org/pub/mozilla.org/firefox/releases/latest/contrib/solaris_tarball/firefox-22.0.en-US.opensolaris-i386.tar.bz2 \
    | bzcat | tar -xf -
$ ln -s /opt/local/lib/libX11.so firefox/libX11.so.4
$ ln -s /opt/local/lib/libXt.so firefox/libXt.so.4
$ env LD_LIBRARY_PATH=/opt/local/lib ./firefox/firefox
```

KDE 4.10.3

```console
$ pkgin in kde-runtime4 kde-workspace4 kde-baseapps4 \
           kde-wallpapers4 kde-base-artwork konsole

$ vi .xinitrc
/opt/local/bin/startkde
```

XFCE 4.6 with Gnumeric and Abiword

```console
$ pkgin in xfce4 gnumeric abiword

$ vi .xinitrc
/opt/local/bin/xfce4-session
```

Enlightenment 0.17 with GIMP

```console
$ pkgin in enlightenment-0.17 gimp

$ vi .xinitrc
/opt/local/bin/enlightenment_start
```

Awesome 3.4.13

```console
$ pkgin in awesome

$ vi .xinitrc
/opt/local/bin/awesome
```

<a name="pkgsrc-2013Q1"/>

## Changes in pkgsrc-2013Q1

Here are the major changes in the pkgsrc-2013Q1 release:

### OpenSSL 1.0.1 with AES-NI support

OpenSSL has been upgraded from the 0.9.8 series to the 1.0.1 series.  The
driving reason to pursue this upgrade was to take advantage of AES-NI support
which significantly improves crypto performance on Intel CPUs which provide
that feature.

On a OSX 10.7 Core i7 laptop the numbers below speak for themselves:

```console
: /usr/bin/openssl 'OpenSSL 0.9.8r 8 Feb 2011'
$ openssl speed -evp aes-128-cbc
  type             16 bytes     64 bytes    256 bytes   1024 bytes   8192 bytes
  aes-128-cbc     157297.05k   173874.73k   176805.45k   177719.17k   179441.78k

: pkgsrc openssl 'OpenSSL 1.0.1e 11 Feb 2013'
$ openssl speed -evp aes-128-cbc
  type             16 bytes     64 bytes    256 bytes   1024 bytes   8192 bytes
  aes-128-cbc     643315.29k   685811.37k   696899.67k   699977.39k   693968.90k
```

A pretty significant 4x improvement for many hundreds of applications which use
OpenSSL for crypto.

### GCC Go support for SmartOS/illumos

[Go](http://golang.org/) is a reasonably new programming language from Google
that a number of our users have asked us to support, so we are pleased to
announce that beginning with pkgsrc-2013Q1 you will be able to use the `gccgo`
front-end to compile and run Go applications on illumos.

You simply compile the go source code as you would for any other language that
GCC supports, for example:

```console
$ pkgin -y install gcc47

: /opt/pkg for the illumos package set, /opt/local for SmartOS datasets..
$ PATH=/opt/pkg/gcc47/bin:$PATH

$ gccgo app.go -o app
$ ./app
```

### Networking utilities on SmartOS

Thanks to initial work by [@postwait](http://twitter.com/postwait) there is now
proper Zone support in libpcap, which has opened up the possibility to run a
number of networking utilities in Joyent SmartMachines.

Yes, this means you can finally run `tcpdump` instead of `snoop`.

<a name="pkgsrc-2012Q4"/>

## Changes in pkgsrc-2012Q4

### Static UID/GID allocations

Up until this release, packages which require a user or group be created would
choose the next available UID/GID on the target system.  This meant that IDs
would be different across a range of machines.

Starting in pkgsrc-2012Q4 we created a list of statically assigned UID and GID,
using the range between 100-999, with a special `_pkgsrc` UID/GID taking 999.
This means that the first `useradd` or `groupadd` done by the user will start
at 1000 and avoid using the area reserved for pkgsrc.
