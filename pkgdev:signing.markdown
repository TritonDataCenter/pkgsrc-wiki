## Contents

1. <a href="#introduction">Introduction</a>
1. <a href="#setup">Setup</a>
1. <a href="#create-pgp-key">Create PGP Key</a>
1. <a href="#configure-gnupg">Configure GnuPG</a>
1. <a href="#configure-pkgbuild">Configure pkgbuild</a>
1. <a href="#test-package-signing">Test Package Signing</a>
1. <a href="#offline-signing">Offline Signing</a>

<a name="introduction"/>

## Introduction

This guide shows how to set up PGP signing of packages in a pkgbuild
environment.  If you aren't using pkgbuild, there should be enough details here
to get you started.

<a name="setup"/>

## Setup

In a pkgbuild zone, but not inside a sandbox, install GnuPG from pkgsrc, as the
one provided by the platform is too old and incomplete.

```console
$ pkgin in gnupg2
```

Note that the command installed from pkgsrc is `gpg2`, while `gpg` will still
be the one from the platform in `/usr/bin/gpg`.

<a name="create-pgp-key"/>

## Create PGP Key

Create a new PGP key that will be used to sign your packages.  Obviously use
your own email address and comment instead of the ones shown here.  You will be
asked to set a password, make sure it is highly secure (e.g. `pwgen -sy 64`
using the `pwgen` package from pkgsrc).  The GPG agent will cache it so you do
not need to type it in every time.

```console
$ gpg2 --gen-key

Please select what kind of key you want:
   (1) RSA and RSA (default)
   (2) DSA and Elgamal
   (3) DSA (sign only)
   (4) RSA (sign only)
Your selection? 1
RSA keys may be between 1024 and 4096 bits long.
What keysize do you want? (2048) 4096
Requested keysize is 4096 bits
Please specify how long the key should be valid.
         0 = key does not expire
      <n>  = key expires in n days
      <n>w = key expires in n weeks
      <n>m = key expires in n months
      <n>y = key expires in n years
Key is valid for? (0)
Key does not expire at all
Is this correct? (y/N) y

GnuPG needs to construct a user ID to identify your key.

Real name: My Package Key
Email address: pkgsrc@example.com
Comment:
You selected this USER-ID:
    "My Package Key <pkgsrc@example.com>"

Change (N)ame, (C)omment, (E)mail or (O)kay/(Q)uit? O
You need a Passphrase to protect your secret key.

gpg: key F527BD41 marked as ultimately trusted
gpg: checking the trustdb
gpg: 3 marginal(s) needed, 1 complete(s) needed, PGP trust model
gpg: depth: 0  valid:   1  signed:   0  trust: 0-, 0q, 0n, 0m, 0f, 1u
pub   4096R/F527BD41 2017-04-21
      Key fingerprint = A84D 1638 63E2 420A B0EB  047D 3349 5548 F527 BD41
uid       [ultimate] My Package Key <pkgsrc@example.com>
sub   4096R/5704520E 2017-04-21
```

1. <a href="#configure-gnupg">Configure GnuPG</a>

## Configure GnuPG

To enable automatic signing of packages without having to type in the password
every time we can configure `gpg-agent` to cache the password.  Follow these
instructions if you wish to do so.

Configure `/root/.gnupg/gpg-agent.conf`:

```conf
daemon
use-standard-socket
max-cache-ttl 315360000
default-cache-ttl 315360000
pinentry-program /opt/local/bin/pinentry-tty
```

Configure `/root/.gnupg/gpg.conf`:

```conf
lock-never
no-tty
keyserver hkp://keys.gnupg.net
```

Launch the agent and cache the password

```console
$ gpg-agent
$ echo "hi" >f
$ gpg2 --sign f

: Test that subsequent runs do not prompt for the password
$ rm -f f.gpg
$ gpg2 --sign f

: Check the signature
$ gpg2 --verify f.gpg
gpg: Signature made Fri Apr 21 08:57:07 2017 UTC using RSA key ID F527BD41
gpg: Good signature from "My Package Key <pkgsrc@example.com>" [ultimate]

$ rm -f f{,.gpg}
```

<a name="configure-pkgbuild"/>

## Configure pkgbuild

The final part is to configure pkgbuild to use the key we have generated.  If
you do not wish to sign inline and prefer to sign packages separately, skip
this part and go straight to the <a href="#offline-signing">offline signing</a>
section.

Add to `mk.conf.local` for your target build (e.g.
`/data/pkgbuild/conf/2017Q1-x86_64/mk.conf.local`):

```make
SIGN_PACKAGES=	gpg
```

Add to `pkgbuild.conf.local` for your target build (e.g.
`/data/pkgbuild/conf/2017Q1-x86_64/pkgbuild.conf.local`):

```make
PKGSRC_GPG_SIGN_AS=F527BD41
```

<a name="test-package-signing"/>

## Test Package Signing

Let's now check that everything works as expected.

Start the sandbox:

```console
$ run-sandbox 2017Q1-x86_64
```

Build a small package:

```console
$ cd /data/pkgsrc/pkgtools/digest
$ bmake package
[...]
===> Building binary package for digest-20160304
=> Creating binary package /data/packages/SmartOS/2017Q1/x86_64/All/digest-20160304.tgz
pkg_info: unable to verify signature: Signature key id 33495548f527bd41 not found
*** Error code 1
```

At this point we are successfully signing the package, but the install is
failing because our new key is not yet trusted by the packaging tools.

Import the new key into the pkgsrc keyring (as configured in
`/opt/local/etc/pkg_install.conf`):

```console
$ gpg2 --export F527BD41 | \
  gpg2 --no-default-keyring \
       --keyring=/opt/local/etc/gnupg/pkgsrc.gpg \
       --import -
```

Then try to install the package again.  This time it should complete
successfully with no errors:

```console
$ pkg_add /data/packages/SmartOS/2017Q1/x86_64/All/digest-20160304.tgz
```

You will need to install this updated keyring onto any host that uses your
signed packages.

<a name="offline-signing"/>

## Offline Signing

Some people prefer not to sign packages inline, usually because their security
policies don't permit cached passwords, or they do not allow the build hosts to
have access to the signing key.  If you prefer to leave signing to a separate
step, ensure you have not configured pkgbuild with `SIGN_PACKAGES=gpg` as
described above.

Build the unsigned package in a sandbox then exit it.

```console
$ run-sandbox 2017Q1-x86_64
$ cd /data/pkgsrc/pkgtools/digest
$ bmake package
[...]
===> Building binary package for digest-20160304
=> Creating binary package /data/packages/SmartOS/2017Q1/x86_64/All/digest-20160304.tgz
$ exit
```

Configure `/opt/local/etc/pkg_install.conf` (wherever you have chosen to
perform the signing) with your key details:

```make
GPG=/opt/local/bin/gpg2
GPG_SIGN_AS=F527BD41
```

Sign the package.  Note the different file formats, unsigned packages are
ordinary compressed tarballs, whereas signed packages are ar(1) archives of the
compressed tarball plus the signing files.

```console
$ mkdir signed unsigned
$ cp /data/packages/SmartOS/2017Q1/x86_64/All/digest-20160304.tgz unsigned
$ pkg_admin gpg-sign-package unsigned/digest-20160304.tgz signed/digest-20160304.tgz
$ file unsigned/digest-20160304.tgz signed/digest-20160304.tgz
unsigned/digest-20160304.tgz:	gzip compressed data - deflate method
signed/digest-20160304.tgz:	current ar archive, not a dynamic executable or shared object
```
