---
author: gvivekcbe
comments: true
date: 2015-02-01 22:40:33+00:00
layout: post
title: Reading and writing to a virtual com port device using libusb.
---

1)Download LUFA
2)Put your arduino into DFU mode and flash the 
Virtual Serial firmware
3)Use a protocol analyzer and decode the command packets
4)Replace the com port driver with libusb
5)Send the control messages by setting the baud rate, line length encoding etc.
6)Now you can talk to the de
vice using libusb.