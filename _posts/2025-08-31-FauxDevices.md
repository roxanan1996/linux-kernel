---
title: "Faux devices vs platform devices"
date: 2025-08-31
---


# What is a platform device
The linux kernel device model uses the concept of buses to handle devices. A bus is basically a link between the CPU and the devices.
For some buses it is possible to enumerate the devices sitting on them. PCI, USB buses are en example. This devices are "automatically" discovered during boot, but also later in case of plug-in devices. 

But, there are a bunch of devices undiscoverable by the CPU, because they are on-chip or sit on slow buses with no device discoverabily function.
As a result, the kernel provided a mechanism to receive information about the hardware based on users' information.

These devices are known as platform devices. 

# What is a faux device
The faux device is supposed to replace the platform device usage when there is no real device in place. To be used when a driver only needs a devices to "hang" something off. For example:
- downloading firmware
- device debug printing via dev_debug TODO

# Which old "platform" devices should be faux devices
To check if the platform device usage is real, we need to take into account the API used in the kernel. Some old drivers register the platform device manually and allocate it's resources based on static information. The new way is providing the device information and its resources in the dts or acpi file. 
TODO 
