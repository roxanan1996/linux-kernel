---
title: "Faux devices vs platform devices"
date: 2025-08-31
---


# What is a platform device
The Linux kernel device model utilizes the concept of buses to manage devices. A bus is basically a link between the CPU and the devices.
For some buses, it is possible to enumerate the devices sitting on them. PCI and USB buses are examples of these kind. These devices are "automatically" discovered during boot, but also later in the case of plug-in devices. 

But, there are a bunch of devices undiscoverable by the CPU because they are not based on comunication via conventional buses. Some examples include legacy port-based devices and host bridges to peripheral buses, and most contollers integrated into system-on-chip platforms.

These devices are known as platform devices. Because they do not sit on conventional buses, the concept of a platform bus was introduced. 

The platform core abstraction consists of:
- [struct platform_device](https://elixir.bootlin.com/linux/v6.16.4/source/include/linux/platform_device.h#L23)
  - As expected it contains a struct device dev
  - Two important fields are
    - name which is supposed to be the platform device name; it is used under certain circumstances (no device tree or ACPI support and no id table match) for matching the device with its driver
    - [struct resource *resource](https://elixir.bootlin.com/linux/v6.16.4/source/include/linux/ioport.h#L21); it is an array of resources assigned to the platform device, like IRQ lines, DMA channels, memory regions, I/O ports etc     
- [struct platform_driver](https://elixir.bootlin.com/linux/v6.16.4/source/include/linux/platform_device.h#L236)
  - the probe is called when a device is matched against the driver
  - id_table represents one way to bind actual devices to the driver. The other way is via the device tree

The old way of enumerating the platform devices was done in board files or in the drivers. That meant that there was no flexibility in adding and removing them dynamically, as it meant recompiling modules or in the worst case recompiling the whole kernel. 
Nowadays, the device tree is used instead. It provides a portable interfaces for declaring non-discoverable devices present in the system. Platform devices are declared as nodes under a node whose 'compatible' string property is 'simple-bus'. Then the kernel ends up creating a platform device of each first-level sub-node under this one.

The most important takeway here is that platform_devices have to be declared somewhere. The old way was in the kernel driver, now device tree are used.
The platform device is extensively used in the kernel when there is no match in hardware. The best way to check if the platform device is "real" is by checking if there's a declaration in the device tree (check for 'compatible' property string), or if it's an old implementation, check if physical resources are declared when the device is instantiated. Otherwhise, this is a hint that you should not use a platform device and a faux device instead.

# What is a faux device
The faux device is supposed to replace the platform device usage when there is no real device in place. To be used when a driver only needs a device to "hang" something off. For example:
- Downloading firmware
  - request_firmware API defined in [include/linux/firmware.h](https://elixir.bootlin.com/linux/v6.16.4/source/include/linux/firmware.h)   
- Device logging via dev_*
  - Defined in [include/linux/dev_printk.h](https://elixir.bootlin.com/linux/v6.16.4/source/include/linux/dev_printk.h)
  - They include a formatted prefix with a device name
  - When an /sys entry is needed

The implementation is very simple and can be found at:
- [include/linux/device/faux.h](https://elixir.bootlin.com/linux/v6.16.4/source/include/linux/device/faux.h)
- [drivers/base/faux.c](https://elixir.bootlin.com/linux/v6.16.4/source/drivers/base/faux.c)

The most important takeaways from the implementations are:
- [struct faux_object](https://elixir.bootlin.com/linux/v6.16.4/source/drivers/base/faux.c#L25); a wrapper structure that holds a struct faux_device that points to a set of callback (probe and remove) [struct faux_device_ops](https://elixir.bootlin.com/linux/v6.16.4/source/include/linux/device/faux.h#L34)
- [the faux_bus_type](https://elixir.bootlin.com/linux/v6.16.4/source/drivers/base/faux.c#L78)
  - which contains the match, probe and remove functions 
- [faux_device_create*](https://elixir.bootlin.com/linux/v6.16.4/source/drivers/base/faux.c#L99)
  - that will create and register with the driver core a faux device ; this will call the probe function specified in the faux_ops structure so that everything will be be properly initialized    

As noticed, there are no resources used by the faux device, otherwise it will be just a platform_device. Register the device will trigger probing the device, which does not hold for a platform device.
