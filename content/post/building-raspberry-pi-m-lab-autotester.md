---
title: "Building Raspberry Pi M Lab Autotester"
author: "Chris Ritzo"
date: 2017-10-24T10:02:53-04:00
tags: ["M-Lab", "Raspberry Pi", "Measurement Kit"] 
draft: false 
---

As anyone who has run a network measurement test more than once will tell you, a single measurement is not as good as the trend of test results over time. [Measurement Lab’s (M-Lab)](https://www.measurementlab.net/) extension for Google Chrome lets you schedule tests and see trends over time, but you have to leave Chrome open all the time to get the benefit. Other measurement initiatives like RIPE Atlas, OONI, and others, have developed a small, dedicated testing device that’s connected to a network which runs tests automatically. I’ve experimented in the past with running the Network Diagnostic Tool (NDT) automatically from small computers like Odroid or Raspberry Pi. In the [Measuring Broadband in Schools](https://www.newamerica.org/in-depth/measuring-broadband-alexandrias-schools/) pilot, we used a basic script to run the NDT command line test from Odroid computers within school classrooms. These were basic prototype devices that we manually setup and managed during the short-term research study that we completed earlier this year.

During that study, we learned a lot about both what the user needs are or a dedicated measurement device and about the challenges of deploying and managing them. Since then, we’ve been working to improve our ability to support and manage the development of small measurement devices that run M-Lab tests on a larger scale, using an internet-of-things approach. More on that in a future post. We want to build a system that can be used for both larger research studies and by individuals interested in tracking the performance of their home or small business connections.

In this post, I’ll outline how to setup a Raspberry Pi computer that you can connect to your router at home to periodically run the NDT test provided by [Measurement Kit](https://github.com/measurement-kit/measurement-kit). Measurement Kit is a C++ library that runs a number of network measurement tests, including the OONI test suite, Neubot’s MPEG DASH test, and NDT, which is hosted on the Measurement Lab Platform. For now, this code is a work in progress, but we plan to build out additional features like a lightweight analysis and visualization tool. In this iteration, the test results are stored on your Pi, which you can then analyze and look for trends in network performance.

Ok, let’s get started. We’re currently hosting the code for this project on Github in the [mk-rpi repository](https://github.com/opentechinstitute/mk-rpi). For those who want to get started quickly, just jump over to the [README](https://github.com/opentechinstitute/mk-rpi/blob/master/README.md) and follow the instructions there. Here I’ll dig into the details for those who may want a more step-by-step tutorial.

Setup Your Pi
---

The first thing to do is install the latest Raspbian operating system onto your Pi and configure it to your liking. The remainder of this guide assumes you are logged into your Pi locally or over SSH, running as the pi user.

* Download the the latest [Raspbian OS (Debian 9/Stretch)](https://www.raspberrypi.org/downloads/raspbian/), flash it onto an SD card, insert into your Pi, connect a keyboard and monitor, and boot it up.
* Use the [raspi-config program](https://www.raspberrypi.org/documentation/configuration/raspi-config.md) to set the device’s hostname, passwords, and enable SSH access.
* Run updates: `$ sudo apt update && sudo apt upgrade`
* Install git: `$ sudo apt install git`

Prepare and Install mk-rpi
---

Next, we’ll use Git to copy the code and run our setup script.

* Create a folder called “mk-pi” to store the files from our Github repo: `$ mkdir mk-pi && cd mk-pi`
* Clone this repository (be sure to include the . at the end of the line): `$ git clone --recursive git@github.com:opentechinstitute/mk-rpi.git`.

Now we’ll run the setup script. The setup.sh script will compile Measurement Kit and installing it to your Pi. This process will take about 20 minutes, so kick off the command below and take a break! Run `$ sudo ./setup.sh -u pi` to install required packages, build the Measurement Kit program, and configure the target system. If you choose to run as a different user than `pi`, replace `pi` with your username in the command above.

Once the script is done, let’s test that everything is working. Navigate to the folder “test-runner” and run this command: `$ python run.py` If all goes well, NDT should run once, then wait for a random time amount, after which another test will run. Press Ctrl-c to stop the test runner. Leaving the test running in a terminal window isn’t convenient, so instead we’ll run it in the background using the program “screen”. We advise you to read the screen manual page: `$ man screen`, but essentially we’ll use it to run our test-runner script in a session that we can disconnect from and leave running. Some basics about screen:

When you start a screen session, it is like you’ve logged in again, but by using screen commands you can leave that session running and do other things. First, let’s start the test runner again, but using screen this time: `$ screen -d -m python run.py`

Everything should be working like it was before, but now you can experiment with screen. Press Ctrl a d to detach from a screen session and leave it running in the background. You can confirm that the test runner script is still running by listing the screen sessions running on the computer: `$ screen -list`.  The output should look something like this: `9281..odroid (07/30/2017 09:47:25 PM) (Detached)`

You can re-connect to a single running screen session using this command: `$ screen -x`

Detach from the screen session again using `Ctrl a d`

If you disconnect your SSH session, and login later, you can reconnect to the test runner screen session using `$ screen -x`.

Accessing Test Result Data
---

Currently test results are uploaded to M-Lab, and also saved on the Pi in the folder where the test is run. If using the defaults outlined above, that is in `/home/pi/mk-pi/`. Each test result is saved as a single file that looks something like this: `report-ndt-2017-10-03T191408Z-0.njson`

Mozilla Festival Raffle
---

We’re participants in the Mozilla community, and will be attending Mozilla Festival, October 27-29, 2017 at Ravensbourne College, London. If you’re coming to MozFest, look us at the Internet Research Hub to learn more about internet health with us. We’ll be giving away a Raspberry Pi 3 Friday and Saturday, and there may be some other surprises also!

