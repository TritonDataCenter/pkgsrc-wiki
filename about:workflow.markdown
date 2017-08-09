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
┌────────────────────────────────────┬───────────────────────────────────────┐
│                                    │                                       │
│        The NetBSD Foundation       │                 Joyent                │
│        =====================       │                 ======                │
│                                    │                                       │
│      Commit directly to pkgsrc     │  Commit directly to pkgsrc-joyent and │
│                                    │    local branches of pkgsrc and wip   │
│                                    │                                       │
│ Access to pkgsrc is limited to TNF │  Access limited to Joyent employees   │
│  members (via sponsorship process) │                                       │
│                                    │                                       │
│ ┌────────────────────────────────┐ │                                       │
│ │ cvs.netbsd.org:/cvsroot/pkgsrc │ │                                       │
│ └───────────────┬────────────────┘ │                                       │
│                [1]                 │                                       │
│    ┌────────────v─────────────┐    │     ┌──────────────────────────┐      │
│    │ github.com/NetBSD/pkgsrc ├───[2]───>│ github.com/joyent/pkgsrc │      │
│    └──────────────────────────┘    │     └──────────────────────────┘      │
│                                    │                                       │
├────────────────────────────────────┤                                       │
│                                    │                                       │
│      The pkgsrc-wip project        │                                       │
│      ======================        │  ┌─────────────────────────────────┐  │
│                                    │  │ github.com/joyent/pkgsrc-joyent │  │
│   Commit directly to pkgsrc-wip    │  └─────────────────────────────────┘  │
│                                    │                                       │
│   Access open to anyone who asks   │                                       │
│                                    │                                       │
│   ┌────────────────────────────┐   │    ┌──────────────────────────────┐   │
│   │ wip.pkgsrc.org:/pkgsrc-wip ├──[3]──>│ github.com/joyent/pkgsrc-wip │   │
│   └────────────────────────────┘   │    └──────────────────────────────┘   │
└────────────────────────────────────┴───────────────────────────────────────┘
```

1. Regular fossil-based full conversion from CVS to Git exported to GitHub
2. Forked repository regularly updated from upstream
3. Loose fork, updates from wip are merged into joyent/pkgsrc-wip manually
