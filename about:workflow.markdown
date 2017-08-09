## Contents

1. <a href="#introduction">Introduction</a>
1. <a href="#repositories">Repositories</a>

<a name="introduction"/>

## Introduction

Our pkgsrc releases are made up from a number of different repositories and
branches, which can be confusing for newcomers.  This document aims to explain
the relationships clearly and make it easy for users to know where issues and
pull requests should go.

<a name="repositories"/>

## Repositories

```
┌────────────────────────────────────┬───────────────────────────────────────┬────────────────────────────────┐
│                                    │                                       │                                │
│        The NetBSD Foundation       │                 Joyent                │     The pkgsrc-wip project     │
│        =====================       │                 ======                │     ======================     │
│                                    │                                       │                                │
│      Commit directly to pkgsrc     │    Commit directly to pkgsrc-joyent   │  Commit directly to pkgsrc-wip │
│                                    │ Commits to pkgsrc and pkgsrc-wip must │                                │
│       Requires being a pkgsrc      │  only be on joyent-specific branches  │     Available to anyone who    │
│     developer and member of TNF    │                                       │       requests an account      │
│        (sponsorship process)       │  Only available to Joyent employees   │                                │
│                                    │                                       │                                │
│ ┌────────────────────────────────┐ │  ┌─────────────────────────────────┐  │ ┌────────────────────────────┐ │
│ │ cvs.netbsd.org:/cvsroot/pkgsrc │ │  │ github.com/joyent/pkgsrc-joyent │  │ │ wip.pkgsrc.org:/pkgsrc-wip │ │
│ └───────────────┬────────────────┘ │  └─────────────────────────────────┘  │ └──────────────┬─────────────┘ │
│                 │                  │    ┌──────────────────────────────┐   │                │               │
│                [1]                 │    │ github.com/joyent/pkgsrc-wip │<───────[3]─────────┘               │
│                 │                  │    └──────────────────────────────┘   │                                │
│    ┌────────────v─────────────┐    │      ┌──────────────────────────┐     │                                │
│    │ github.com/NetBSD/pkgsrc ├───[2]────>│ github.com/joyent/pkgsrc │     │                                │
│    └──────────────────────────┘    │      └──────────────────────────┘     │                                │
└────────────────────────────────────┴───────────────────────────────────────┴────────────────────────────────┘
```

1. Regular fossil-based full conversion from CVS to Git exported to GitHub
2. Forked repository regularly updated from upstream
3. Loose fork, updates from wip are merged into joyent/pkgsrc-wip manually
