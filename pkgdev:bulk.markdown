## Contents

1. <a href="#introduction">Introduction</a>
1. <a href="#host-setup">Host setup</a>
1. <a href="#generate-ssh-key">Generate SSH key</a>
1. <a href="#build-pbulk-tools">Build pbulk tools</a>
1. <a href="#install-manta-tools">Install Manta tools (optional)</a>
1. <a href="#configure-pbulk">Configure pbulk</a>
  1. <a href="#configure-pbulk-pbulk-conf-local">pbulk.conf.local</a>
  1. <a href="#configure-pbulk-pkgbuild-conf-local">pkgbuild.conf.local</a>
  1. <a href="#configure-pbulk-mk-conf-local">mk.conf.local</a>
1. <a href="#perform-bulk-build">Perform bulk build</a>

<a name="introduction"/>

## Introduction

Bulk builds are a way to automatically build a large number of packages,
generating a nice report at the end summarising the results.  We use bulk
builds to build our official package sets.

Results for various operating systems are posted regularly to the
<https://mail-index.netbsd.org/pkgsrc-bulk/> mailing list.

Note that the steps below should be NOT be performed inside a `run-sandbox`
session, but on the primary OS.  The bulk build software and build scripts set
up sandbox chroots automatically as part of the build process.

<a name="host-setup"/>

## Host Setup

Ensure you have followed the necessary [setup
instructions](/joyent/pkgsrc/wiki/pkgdev:setup) for your target OS first.

<a name="generate-ssh-key"/>

## Generate SSH key

Bulk builds support distributed builds via SSH, but even for single host builds
we use SSH to launch local builds.  Generate a private key for the host in
question to allow unprompted logins.

```console
$ ssh-keygen -f ~/.ssh/pkgsrc_pbulk_rsa -N '' -b 4096 -t rsa
$ cat ~/.ssh/pkgsrc_pbulk_rsa.pub >>~/.ssh/authorized_keys

$ vi ~/.ssh/config
Host 127.0.0.1
	IdentityFile ~/.ssh/pkgsrc_pbulk_rsa

: Test that unprompted logins work and cache the host key.
$ ssh 127.0.0.1 echo
```

<a name="build-pbulk-tools"/>

## Build pbulk tools

The first set of packages we need to build are the pbulk tools, and any
software required to perform the main bulk builds.

First checkout the `joyent/feature/pbulk/trunk` branch.  This contains some
modifications to the pbulk code, for example to enable publishing to
[Manta](https://www.joyent.com/manta).

```console
$ cd /data/pkgsrc
$ git checkout -b joyent/feature/pbulk/trunk origin/joyent/feature/pbulk/trunk
```

Then execute the build script, passing an appropriate `PKGBUILD` configuration
in the environment.

```console
: Choose one of the options below based on your target OS.
$ export PKGBUILD=linux-trunk-pbulk32   # Generic Linux 32-bit
$ export PKGBUILD=linux-trunk-pbulk64   # Generic Linux 64-bit
$ export PKGBUILD=osx-trunk-pbulk32     # Mac OS X 32-bit
$ export PKGBUILD=osx-trunk-pbulk64     # Mac OS X 64-bit

: Execute the tools build script
$ /data/pkgbuild/scripts/run-jenkins-tools
```

This may take a while, during which time it will build a pkgsrc bootstrap kit
and a bunch of packages.

<a name="install-manta-tools"/>

## Install Manta tools (optional)

If you wish to publish your results to Manta, install the Manta CLI into a
separate directory, using a sandbox to avoid host pollution.

```console
$ /data/pkgbuild/scripts/run-sandbox ${PKGBUILD}

: Within the sandbox
$ mkdir -p /data/manta
$ cd /data/manta
$ pkg_add nodejs
$ npm install manta
$ exit
```

<a name="configure-pbulk"/>

## Configure pbulk

Now we can switch to configuring our main bulk build.  Start by setting
`PKGBUILD` to an appropriate configuration.

```console
: Choose one of the options below based on your target OS.
$ export PKGBUILD=linux-trunk-i386      # Generic Linux 32-bit
$ export PKGBUILD=linux-trunk-x86_64    # Generic Linux 64-bit
$ export PKGBUILD=osx-trunk-i386        # Mac OS X 32-bit
$ export PKGBUILD=osx-trunk-x86_64      # Mac OS X 64-bit
```

Then edit the following files under that pkgbuild directory.

```console
$ cd /data/pkgbuild/conf/${PKGBUILD}
```

<a name="configure-pbulk-pbulk-conf-local"/>

### pbulk.conf.local

This file contains overrides from the primary `pbulk.conf` file, so look at
that to get an idea of what variables can be set.  Here's similar to what is
used for the Ubuntu Linux 16.10 builds.

```console
$ vi pbulk.conf.local
```

```bash
pkgsrc=/data/pkgsrc
report_from_addr="pkgsrc@example.com"
report_from_name="pkgsrc bulk builds"
report_recipients="pkgsrc-reports@example.com"
build_chroots=8
scan_chroots=8
base_url=https://us-east.manta.joyent.com/pkgsrc/public/reports/Linux/trunk/x86_64
MANTA_USER="pkgsrc"
MANTA_SUBUSER="linux"
MANTA_KEY_ID="aa:bb:cc:dd:ee:ff:gg:00:11:22:33:44:55:66:77:88"
MANTA_URL="https://us-east.manta.joyent.com"
MANTA_PATH="/usr/pbulk/bin:/data/manta/node_modules/.bin"
report_manta_target="/pkgsrc/public/reports/Linux/trunk/x86_64"
report_platform="Ubuntu Linux 16.10/x86_64"
```

If you wish to perform a simpler build to start with, just to check that
everything is working ok, or if you only need to build certain packages, then
here are some recommended settings for a limited bulk build:

```bash
limited_list=/data/build-packages-list
build_chroots=1
scan_chroots=1
```

Then populate `/data/build-packages-list` with a list of package directories to
build, for example:

```console
$ echo "pkgtools/cwrappers" >/data/build-packages-list
```

If you'd prefer to simply rsync the report to an existing HTTP server rather
uploading than Manta, configure the following variables and remove the
`manta_*` variables:

```bash
report_rsync_args="-avz --delete-excluded"
report_rsync_target="192.168.1.10:/data/reports"
base_url="http://192.168.1.10/reports"
```

You will need to pre-create the `${report_rsync_target}` directory to avoid
rsync failures.

<a name="configure-pbulk-pkgbuild-conf-local"/>

### pkgbuild.conf.local

This file allows you to override any variables from the main `pkgbuild.conf`
file.  You will probably want to set `FAILURE_RECIPIENTS` so that you get
notification when the pbulk scan fails, and you may also want to set
`PKGBUILD_GPG_SIGN_AS` if signing your own packages.

```console
$ vi pkgbuild.conf.local
```

```bash
FAILURE_RECIPIENTS=pkgsrc@example.com
PKGBUILD_GPG_SIGN_AS=DEADBEEF
```

<a name="configure-pbulk-mk-conf-local"/>

### mk.conf.local

You can set your own pkgsrc variables in `mk.conf.local`, for example if you
wish to change the default package options.

```console
$ vi mk.conf.local
```

```make
# Enable compressed mailboxes with mutt
PKG_OPTIONS.mutt+=	mutt-compressed-mbox
```

<a name="perform-bulk-build"/>

## Perform bulk build

Now that everything is set up we can perform the bulk build.

First, switch over to the target branch.  This will likely be either one of the
`joyent/release/*` branches if you wish to track a quarterly release branch, or
will be the latest pkgsrc trunk from upstream.

```console
$ cd /data/pkgsrc

: Build against a Joyent quarterly release..
$ git checkout -b joyent/release/2016Q4 origin/joyent/release/2016Q4

: ..or fetch the latest pkgsrc trunk and build that
$ git remote add upstream https://github.com/jsonn/pkgsrc.git
$ git fetch upstream
$ git checkout -b upstream/trunk upstream/trunk
```

Then, assuming you have correctly set `PKGBUILD` to an appropriate value, run
the bulk build script.

```console
$ /data/pkgbuild/scripts/run-jenkins-build
```
