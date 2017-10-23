---
author: "Ross Schulman"
title: "Mozfest and Matrix, Part 2"
tags: ["MozFest 2017", "matrix", "portable", "decentralization"]
categories: ["MozFest 2017"]
date: 2017-09-12T15:40:02-04:00
draft: false 
---
My estimates of what we need to accomplish before MozFest are roughly:

1. Get a RaspberryPi 3 and install Raspbian Lite as well as [synapse](https://github.com/matrix-org/synapse)
1. Install PostgreSQL to serve as synapse's database instead of sqlite
1. Configure synapse
1. Engage with MozFest wranglers to determine appropriate network settings (hostname, DNS, WiFi, etc.)
1. Figure out a carrying case or some way to make it all portable
1. Figure out a display of some kind
1. Determine how to display visualizations, and what synapse APIs are usable for this purpose
1. Prepopulate logins database table with registration info from MozFest (and ponder privacy concerns)
1. Think about data retention post MozFest

There's almost certainly other tasks I'm not thinking of at the moment.

Here's what we've got so far:
![Early Setup](/mozfest/early_rpi.jpg)

We're using a Raspberry Pi 3, currently hooked up to ethernet, though the plan is to use WiFi at the end of the day. Attached to it via USB is a 256GB SSD hard drive where our database backend, PostgreSQL, will keep its data files. My hope is that using an SSD drive will allow us to keep a smaller synapse cache in the Pi's minimal RAM.

Already installed on the Raspberry Pi is Raspbian Lite, PostgreSQL, and synapse. The database is configured and synapse is up and running.

Right now, the main blocker for me is that local network mDNS (which allows us to connect to the Raspberry Pi without assigning it a global domain name) is flaky on the Raspberry Pi. It works for a few minutes but then fails and I haven't been able to figure out why.

Next steps is to find a time to talk to folks at MozFest about the network topology there and begin thinking about visualizations and a case.
