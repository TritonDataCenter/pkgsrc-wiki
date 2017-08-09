## Contents

1. <a href="#introduction">Introduction</a>
1. <a href="#repositories">Repositories</a>
  1. <a href="#pkgsrc">pkgsrc</a>
  1. <a href="#pkgsrc-joyent">pkgsrc-joyent</a>
  1. <a href="#pkgsrc-wip">pkgsrc-wip</a>
1. <a href="#repository-flows">Repository Flows</a>
1. <a href="#faq">FAQ</a>
  1. <a href="#faq-pkgpath">How do I know where a package came from?</a>

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

<a name="faq"/>

## FAQ

<a name="faq-pkgpath"/>

### How do I know where a package came from?

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
