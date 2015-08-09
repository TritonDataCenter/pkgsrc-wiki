## Contents

1. <a href="#introduction">Introduction</a>
1. <a href="#branches">Branches</a>
1. <a href="#images">Images</a>
  1. <a href="#versioning">Versioning</a>
  1. <a href="#image-types">Image Types</a>
  1. <a href="#image-architectures">Image Architectures</a>
  1. <a href="#image-names">Image Names</a>
  1. <a href="#additional-images">Additional Images</a>
1. <a href="#lts">LTS</a>

## Introduction

This page describes the pkgsrc release process.

## Branches

A new pkgsrc branch is created every quarter using the `pkgsrc-YYYYQQ` naming
scheme, with each `Q4` release designated as Long Term Support (LTS) and
supported by Joyent for 3 years.  The approximate timeline is as follows:

| Date          | Branch          | LTS?                |
|---------------|-----------------|---------------------|
| ...           | ...             | ...                 |
| 30 Sept 2015  | `pkgsrc-2015Q3` | No                  |
| 31 Dec 2015   | `pkgsrc-2015Q4` | Until pkgsrc-2018Q4 |
| 31 March 2016 | `pkgsrc-2016Q1` | No                  |
| 30 June 2016  | `pkgsrc-2016Q2` | No                  |
| 30 Sept 2016  | `pkgsrc-2016Q3` | No                  |
| 31 Dec 2016   | `pkgsrc-2016Q4` | Until pkgsrc-2019Q4 |
| 31 March 2017 | `pkgsrc-201671` | No                  |
| ...           | ...             | ...                 |

While the branch may be created on the expected branch date, it may be a few
weeks after that before binary packages and images show up, due to the amount
of time it takes to build them and iron out any final issues.

## Images

Images are an easy way to install a particular pkgsrc release, and are the
primary way that SmartOS users provision zones.  They are a snapshot of a zone
which has had pkgsrc installed inside it along with everything required for the
zone to function.

### Versioning

The version scheme is taken from the pkgsrc branch they are based on:

| Image Version | Branch          |
|---------------|-----------------|
| ...           | ...             |
| 15.3.x        | `pkgsrc-2015Q3` |
| 15.4.x        | `pkgsrc-2015Q4` |
| 16.1.x        | `pkgsrc-2016Q1` |
| 16.2.x        | `pkgsrc-2016Q2` |
| 16.3.x        | `pkgsrc-2016Q3` |
| 16.4.x        | `pkgsrc-2016Q4` |
| 17.1.x        | `pkgsrc-2017Q1` |
| ...           | ...             |

The `x` minor version is bumped whenever changes are made to the image (for
example, providing updated packages), but will continue to use the same pkgsrc
branch.

### Image Types

There are a couple of different basic images available for each version.  They are:

| Image Type | Description                                                         |
|------------|---------------------------------------------------------------------|
| `minimal`  | The smallest zone possible with only core packages installed        |
| `base`     | The `minimal` image plus a number of other commonly-used packages   |

### Image Architectures

These basic image types are available in multiple architectures:

| Image Arch  | Description                                                        |
|-------------|--------------------------------------------------------------------|
| `32`        | 32-bit binaries (`-m32`)                                           |
| `64`        | 64-bit binaries (`-m64`)                                           |
| `multiarch` | 32-bit binaries with additional 64-bit libraries for some packages |

The `multiarch` image only provides 64-bit libraries for certain packages and
is still under development.  It is recommended that users choose the 64-bit
image unless they have specific 32-bit or multiarch requirements.

### Image Names

Each LTS release additionally includes the `-lts` suffix in its name to ease
identification.

Thus example full image names are:

```console
$ imgadm avail -o name,version,published | egrep "NAME|base.*(15\.4|16\.1)"
NAME                    VERSION     PUBLISHED
base-32-lts             15.4.0      2016-01-19T14:02:51Z
base-64-lts             15.4.0      2016-01-19T14:19:02Z
base-multiarch-lts      15.4.0      2016-01-19T14:36:00Z
base-32-lts             15.4.1      2016-03-04T00:18:59Z
base-64-lts             15.4.1      2016-03-04T00:35:45Z
base-multiarch-lts      15.4.1      2016-03-04T00:52:54Z
base-32                 16.1.0      2016-04-27T15:42:47Z
base-64                 16.1.0      2016-04-27T15:52:46Z
base-multiarch          16.1.0      2016-04-27T16:02:53Z
```

### Additional Images

In addition to these primary images, we also provide a `pkgbuild` image.  This
contains all the infrastructure necessary for building and developing packages.
It is always 64-bit (so that it can be used to build both 32-bit and 64-bit
packages), and does not have an `-lts` suffix.

There are also a number of other images available for specialised appliance
deployments, such as databases (`postgresql`, `percona`) and webservers
(`nginx`, `apache`).

## LTS

The most recent pkgsrc branch is maintained by the pkgsrc-releng team at The
NetBSD Foundation.  They will pullup security and bug fixes to the branch as
requested by pkgsrc developers.  Once a new branch is cut, maintenance is
stopped on the the existing branch and is moved instead onto the new branch.

As Joyent customers are not often in a position to upgrade their zones every 3
months, we introduced an LTS designation for each `Q4` release, which means
that we will continue to pullup security and bug fixes to that branch for 3
years after its initial release.

This gives customers more time to plan their upgrades and ensure their existing
applications are not vulnerable to the OpenSSL exploit du jour.
