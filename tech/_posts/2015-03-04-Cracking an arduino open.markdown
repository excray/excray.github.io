---
author: gvivekcbe
comments: true
date: 2015-03-04 22:40:33+00:00
layout: post
title: Cracking an arduino open.
---

Arduino UNO R2 expained:
This arduino has two mcu - Atmega8u2 and Atmega328. 
Atmega8u2 has the DFU bootloader and the usb-serial firmware loaded to it by default. The usb-serial firmware is what reads the usb in buffer and converts it to serial data for the main chip - Atmega328 to process. 

DFU bootloader:
The DFU bootloader allows you to update the firmware in Atmega8u2 without an ISP(In system programmer). 

Bootloader:
This bootloader in Atmega328 is the one that loads the main program stored in flash. When the arduino is plugged in, this bootloader runs and then the main program starts running. 

More to follow..
