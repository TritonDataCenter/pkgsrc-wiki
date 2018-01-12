## Contents

1. <a href="#introduction">Introduction</a>
1. <a href="#repositories">Repositories</a>
    1. <a href="#pkgsrc">pkgsrc</a>
    1. <a href="#pkgsrc-joyent">pkgsrc-joyent</a>
    1. <a href="#pkgsrc-wip">pkgsrc-wip</a>
1. <a href="#repository-flows">Repository Flows</a>
1. <a href="#branches">Branches</a>
    1. <a href="#pkgsrc-yyyyqq">pkgsrc-YYYYQQ</a>
    1. <a href="#backports">backports</a>
    1. <a href="#ctf">ctf</a>
    1. <a href="#miscfix">miscfix</a>
    1. <a href="#multiarch">multiarch</a>
    1. <a href="#pbulk">pbulk</a>
    1. <a href="#pbulkmulti">pbulkmulti</a>
    1. <a href="#release">release</a>
1. <a href="#faq">FAQ</a>
    1. <a href="#faq-pkgpath">Find which repository a package came from</a>

<a name="introduction"/>

## Introduction

Our pkgsrc releases are made up from a number of different repositories and
branches, which can be confusing for newcomers.  This document aims to explain
the relationships clearly and make it easy for users to know where issues and
pull requests should go.

<a name="repositories"/>

## Repositories

We have three main repositories, though two of them are themselves forks of
upstream repositories:

<a name="pkgsrc"/>

### pkgsrc

This is the primary repository, located at <https://github.com/joyent/pkgsrc/>,
and contains the majority of packages.  It is a downstream fork of the official
pkgsrc repository.

<a name="pkgsrc-joyent"/>

### pkgsrc-joyent

This is a secondary repository, located at
<https://github.com/joyent/pkgsrc-joyent/>.  This repository contains a number
of packages that are not (yet) suitable for committing either to pkgsrc or
pkgsrc-wip, mostly because they have Joyent-specific requirements.

<a name="pkgsrc-wip"/>

### pkgsrc-wip

This is another secondary repository, located at
<https://github.com/joyent/pkgsrc-wip/>, and based on the pkgsrc-wip project.
This repository contains packages that are considered as not yet ready for
inclusion into the main pkgsrc repository, but we find enough merit in some of
them to include them in our package repositories.

<a name="repository-flows"/>

## Repository Flows

The following diagram shows how changes flow through the different
repositories.

```
┌──────────────────────────────────────┬───────────────────────────────────────┐
│                                      │                                       │
│         The NetBSD Foundation        │                 Joyent                │
│         =====================        │                 ======                │
│                                      │                                       │
│       Commit directly to pkgsrc      │  Commit directly to pkgsrc-joyent and │
│                                      │    local branches of pkgsrc and wip   │
│                                      │                                       │
│  Access to pkgsrc is limited to TNF  │  Access limited to Joyent employees   │
│   members (via sponsorship process)  │                                       │
│                                      │                                       │
│  ┌────────────────────────────────┐  │                                       │
│  │ cvs.netbsd.org:/cvsroot/pkgsrc │  │                                       │
│  └───────────────┬────────────────┘  │                                       │
│                 [1]                  │                                       │
│     ┌────────────v─────────────┐     │     ┌──────────────────────────┐      │
│     │ github.com/NetBSD/pkgsrc ├────[2]───>│ github.com/joyent/pkgsrc │      │
│     └──────────────────────────┘     │     └──────────────────────────┘      │
│                                      │                                       │
├──────────────────────────────────────┤                                       │
│                                      │                                       │
│       The pkgsrc-wip project         │                                       │
│       ======================         │  ┌─────────────────────────────────┐  │
│                                      │  │ github.com/joyent/pkgsrc-joyent │  │
│    Commit directly to pkgsrc-wip     │  └─────────────────────────────────┘  │
│                                      │                                       │
│    Access open to anyone who asks    │                                       │
│                                      │                                       │
│    ┌────────────────────────────┐    │    ┌──────────────────────────────┐   │
│    │ wip.pkgsrc.org:/pkgsrc-wip ├───[3]──>│ github.com/joyent/pkgsrc-wip │   │
│    └────────────────────────────┘    │    └──────────────────────────────┘   │
│                                      │                                       │
└──────────────────────────────────────┴───────────────────────────────────────┘
```

1. Regular fossil-based full conversion from CVS to Git exported to GitHub
2. Forked repository regularly updated from upstream
3. Loose fork, updates from wip are merged into joyent/pkgsrc-wip manually

<a name="branches"/>

## Branches

We have developed a number of patch sets that are not yet suitable for pushing
upstream to pkgsrc.  In order to keep track of them they are separated into
feature branches and then merged together to form our release branch.

For our 2017Q4 release it looks something like this:

```
┌──────────────────────────────────────┬───────────────────────────────────────┐
│                                      │                                       │
│         The NetBSD Foundation        │                 Joyent                │
│         =====================        │                 ======                │
│                                      │                                       │
│     ┌──────────────────────────┐     │     ┌──────────────────────────┐      │
│     │ github.com/NetBSD/pkgsrc │     │     │ github.com/joyent/pkgsrc │      │
│     └────────────┬─────────────┘     │     └──────────────────────────┘      │
│                  v                   │                                       │
│     ┌──────────────────────────┐     │     ┌──────────────────────────┐      │
│     │       pkgsrc-2017Q4      ├──────────>│      pkgsrc-2017Q4       │      │
│     └──────────────────────────┘     │     └────────────┬─────────────┘      │
│                                      │                  v                    │
│                                      │ ┌──────────────────────────────────┐  │
│                                      │ │ joyent/feature/backports/2017Q4  │  │
│                                      │ │ joyent/feature/ctf/2017Q4        │  │
│                                      │ │ joyent/feature/miscfix/2017Q4    │  │
│                                      │ │ joyent/feature/multiarch/2017Q4  │  │
│                                      │ │ joyent/feature/pbulk/2017Q4      │  │
│                                      │ │ joyent/feature/pbulkmulti/2017Q4 │  │
│                                      │ └────────────────┬─────────────────┘  │
│                                      │                  v                    │
│                                      │     ┌──────────────────────────┐      │
│                                      │     │   joyent/release/2017Q4  │      │
│                                      │     └──────────────────────────┘      │
└──────────────────────────────────────┴───────────────────────────────────────┘
```

The branches are as follows:

<a name="pkgsrc-yyyyqq"/>

### pkgsrc-YYYYQQ

The pristine branch from upstream.  This is managed by the
<releng-pkgsrc@pkgsrc.org> team who backport changes from pkgsrc trunk as
requested by developers.  This process is documented at
<https://www.netbsd.org/developers/releng/pullups.html>

We pull this branch as-is into our own pkgsrc-YYYYQQ branch, and that is then
used as the basis for our feature branches documented below.

<a name="backports"/>

### backports

A branch where we perform our own pullups of changes from pkgsrc trunk that
might not be suitable for general pkgsrc use, or are done after releng-pkgsrc
have stopped maintaining their branch.

<a name="ctf"/>

### ctf

Our CTF work for pkgsrc-2017Q4 that enables CTF debugging support in supported
packages.

<a name="miscfix"/>

### miscfix

A general dumping ground for changes that aren't big enough for their own
branch.

<a name="multiarch"/>

### multiarch

Our branch to add multiarch support to our packages, see
<http://www.perkin.org.uk/posts/multiarch-package-support-in-smartos.html> for
more details.

<a name="pbulk"/>

### pbulk

A set of changes to enhance pbulk, the software that we use to bulk build our
package sets.

<a name="pbulkmulti"/>

### pbulkmulti

Changes to enable more multi-package builds, for example building p5-DBD-MySQL
against every version of MySQL, MariaDB, and Percona that are supported in
pkgsrc instead of just the default.

<a name="release"/>

### release

This is a combined merge of all of the feature branches, plus the addition of
our pkgsrc-joyent and pkgsrc-wip submodules.  This is the branch that is used
to build the release packages.

<a name="faq"/>

## FAQ

<a name="faq-pkgpath"/>

### Find which repository a package came from

You can use `pkg_info` to show where a package came from, and thus which
repository to log any issues or pull requests against.  For example:

```console
$ pkg_info -Q PKGPATH runit rust zoneinit
wip/runit
lang/rust
joyent/zoneinit
```

If it starts with `joyent/` then <a href="#pkgsrc-joyent">pkgsrc-joyent</a>, if
`wip/` then <a href="#pkgsrc-wip">pkgsrc-wip</a>, otherwise <a
href="#pkgsrc">pkgsrc</a>
