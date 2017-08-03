## Contents

1. <a href="#introduction">Introduction</a>
1. <a href="#dnsmasq">dnsmasq</a>
    1. <a href="#dnsmasq-setup">Setup</a>
    1. <a href="#dnsmasq-example">Example Config</a>

<a name="introduction"/>

## Introduction

This document describes how to set up DNS and DHCP services using pkgsrc
packages available on SmartOS.  Most of the information is applicable to other
pkgsrc platforms but you may need to change paths and init services.

<a name="dnsmasq"/>

## dnsmasq

[dnsmasq](http://www.thekelleys.org.uk/dnsmasq/doc.html) is a lightweight
daemon which supports DNS, DHCP, router advertisement, and network boot
services.  It's a good choice as it is easy to configure and get up and running
quickly.

<a name="dnsmasq-setup"/>

### Setup

```console
: Install package
$ pkgin -y install dnsmasq

: Edit config file
$ vi /opt/local/etc/dnsmasq.conf

: Start service
$ svcadm enable pkgsrc/dnsmasq
```

<a name="dnsmasq-example"/>

### Example Config

A simple example configuration which will provide a DNS and DHCP server for a
local network, forwarding DNS queries to servers configured in
`/etc/resolv.conf`.

```
#
# /opt/local/etc/dnsmasq.conf - configuration file for dnsmasq
#

# Recommended default options
domain-needed
bogus-priv

# Use the custom user/group created by the package
user=dnsmasq
group=dnsmasq

# Enable extra logging
log-facility=/var/log/dnsmasq.log
log-dhcp
log-queries=extra

#
# Enable a DHCP server.  Unknown clients are given addresses in the range
# 192.168.1.128-192.168.1.254 with a 12h lease time.  If supported, clients
# will be configured to use the local NTP server at 192.168.1.10.
#
dhcp-authoritative
dhcp-leasefile=/var/run/dnsmasq.leases
dhcp-option=42,192.168.1.10
dhcp-range=192.168.1.128,192.168.1.254,255.255.255.0,12h

# Add a static DHCP entry for a known host
dhcp-host=aa:bb:cc:dd:ee:ff,192.168.1.20,macbook.local
```
