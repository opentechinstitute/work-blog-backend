---
title: "Matrix and OTI at Mozfest"
author: "Ross Schulman"
date: 2017-11-01T17:00:08Z
draft: false 
---
At [Mozilla Festival](https://mozillafestival.org/) (Mozfest for short), the annual open web/internet freedom conference/party, last weekend, OTI facilitating an [astonishing number of sessions](https://www.newamerica.org/oti/blog/oti-mozfest-2017/). Within the Decentralization space, I organized a local and portable chat server, using Matrix, that was available to attendees as an alternative to something like Slack or Google Hangouts. Decentralization means different things to different people but in this context I wanted to focus on demonstrating a viable alternative to the use of something like Slack or Google Hangouts that retained the control over the service in the hands of the community using it--the attendees of Mozfest. 

What is Matrix?
---
[Matrix](https://matrix.org/) is a relatively new protocol for federated transfer of arbitrary data. The fact that it is federated means that two friends, Alice and Bob, can communicate, each using their own server. Alice can have her own node and Bob can have his and the two of them can still exchange messages. By and large, the matrix protocol is used to exchange real-time chat or images, much like IRC or Slack does, but it has also been used for things such as MIDI control and WebVR. The protocol also implements end to end encryption.

What did We Build?
---

![Photo of the end result](/images/mozfest-result.png)

What I ended up building made use of a system-on-a-chip mini-computer from [Odroid](http://www.hardkernel.com/main/products/prdt_info.php?g_code=G145457216438). It is a credit-card sized fully featured computer that runs Linux. I initially planned to use a Raspberry Pi 3, but the current Matrix server implementation, called [Synapse](https://github.com/matrix-org/synapse) is rather RAM-hungry and the Odroid has 2 GB of RAM to the Pi's 1. OTI also happened to have a touch and HDMI capable screen designed to work with the Odroid and on which I planned to display statistics about the server itself for people to interact with. Finally, we had a lithium ion battery and a charge controller chip with which I hoped to make the whole box portable without needing to be plugged in.

Synapse is actually fairly easy to install although it does require knowing a bit about servers and the DNS system. There are packages available for a number of Linux distributions. Check out the [Synapse Github Repo](https://github.com/matrix-org/synapse) for detailed instructions. Synapse can use a simple sqlite database, which works fine for smaller installations, but given the limited resources of the computer, I had a hunch that using a dedicated database might be faster. Synapse supports PostgresQL as well, so that's what I used.

One major issue with trying to run any kind of server that isn't a cloud-based virtual machine is making that server accessible to the broader internet. Most networks, including the one at Mozfest, use Network Address Translation (NAT), Dynamic Host Configuration Protocol (DHCP), and firewalls, all of which make running a server difficult if not impossible. One tempting solution was to further emphasize the local nature of the server by making it only accessible on the local network--thereby resolving all of these problems by effectively ignoring them. In the end, however, making the server accessible throughout the weekend, even when people were not physically in the space seemed like it would provide the best opportunity to reach the people who might be interested.

To make that possible, I tunneled the necessary ports through an `ssh` connection to a server here in the DC office of OTI. This relay allowed the Synapse server to be reached by a consistent IP address that we could point DNS at, and which had the necessary ports exposed to the broader internet. These kinds of problems are endemic in the decentralization space and need better and easier to implement solutions. For this particular situation, the following command did the trick:

```~shell
~/.synapse$ while true; do ssh -R 8448:localhost:8448 -R 8008:localhost:8008 -R 9092:localhost:9092 -n mozfest.matrix.org sleep 1d; done
```

This snippet forwarded three ports, 8448, 8008, and 9092, from `mozfest.matrix.org` (the domain name we were using) to the portable box. Putting it in the loop made sure that if the connection dropped, it would keep trying until it could reconnect. Ports 8448 and 8008 are Matrix's, while 9092 is for Prometheus.

To capture statistics, both out of curiosity and to power the visualizations, I used [Prometheus](https://prometheus.io/), a popular open source monitoring program that Synapse already has hooks built for. To generate the pretty pictures, [Grafana](https://grafana.com/) did the trick, and interfaces with Prometheus with a minimum of setup.

![Grafana Graph Example](/images/mozfest-grafana.png)

Performance and Stats
---
The Odroid held up well in tough circumstances. I noticed no slowness on the part of the server, and messages seemed to sync nearly immediately. We even conducted a virtual reality tele-conference that was set up by the server and it went without a hitch. It looked like none of the users tried to join some of the truly large rooms in the Matrix network (which can have many tens of thousands of users). If they had, it would have put a huge strain on the available RAM of the Odroid.

The network on the Odroid, on the other hand, caused significant problems. For reasons that I have still not diagnosed, the WiFi card on the Odroid would abruptly stop working at random intervals, requiring a restart of the entire system. This is a process that could take quite a while if I was not physically near the server. 

The other feature that did not work as planned was the battery power. The battery system simply did not provide enough amperage to support the Odroid and the screen at the same time. Unplugging the server with the screen off did allow for some untethered time, but was less impressive without the visuals.

Use of the server ranged from zero connected users at night up to a max of 10-15 during the day, so this was not a high traffic test. It is worth noting, however, that the server was bridged into two of the conference Slack channels which had higher usage and a good deal of traffic that the Synapse server handled without a problem.

Future Plans
---
There are a few different further ideas that I think could be worth following up on here.

1. **Do away with the large LCD touch screen.** The screen was a fun idea, but the reality is that it draws way too much power, produces too much heat, and isn't that easy to see anyway. In addition, one of the Decentralization space staff came up with the good idea of using a projector to throw the current stats up a wall, which made the screen redundant. Instead, a [small LCD](https://www.adafruit.com/product/181) or [OLED display](https://www.adafruit.com/product/326) would have lowered the power requirements as well as the footprint (not to mention the cost).

2. **Design a smaller case.** With the large LCD out of the way, the case can get a lot smaller. While this prototype was portable, it was bulky and difficult to hold and carry. A smaller design would make it much easier to hand around or toss in a bag. It would also cut down on the cost of 3D printing.

3. **Use a bigger battery.** The battery in the box was a 3000mAh which isn't bad, but bigger is always better, of course. If the end goal is something that can be carried around for long periods between recharges, conserving power and making it last longer will be crucial.
