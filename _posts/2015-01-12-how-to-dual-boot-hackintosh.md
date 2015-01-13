---
layout: post
title: Dual Boot Hackintosh (Yosemite) & Windows 8.1 UEFI Boot using Boot Camp
description: "Dual booting Hackintosh"
headline: "EFI Dual boot Hacktintosh"
categories: "Softwaretools"
tags: 
  - Hackintosh
  - yosemite
  - Dual Boot
  - UEFI Dual Boot
  - Boot Camp Dual Boot
comments: true
mathjax: null
featured: true
published: true
---

I am not the first one to write how to dual boot hackintosh alongside Windows 8.1 but i wanted to write this post when my memory is fresh with the all the mistakes and hiccups i did. I have used GPT and UEFI compared to legacy methods. This post is more of general overview rather than step by step guide. 


Pre-requirements
================

1) minimum 8 gb usb flash drive 

2) Hackintosh distro (yosemite or hackintosh zone)

3) Windows 8.1 iso 

4) Clover EFIv2 bootloader 

Caution : I clean wiped my entire hard disk and started from scratch. If you are looking for ways to install hackintosh or windows 8.1 as the second operating system in your machine this might not work. Since In this tutorial i have formatted my harddisk using GPT. if you are already using GPT you might cherry pick the steps to install the second os.


Step 1 - Prepare USB Drive 
======

I used TransMac to burn the DMG file to the usb following this [tutorial](http://www.macbreaker.com/2014/11/how-to-install-os-x-yosemite-on-your-pc-with-yosemite-zone.html). Follow the section 1b to create the os x installer.

Note: Dont forget to format the drive before restoring the iso image 

Step 2 - Install OS X
=====================

Change your boot sequence to boot from the USB drive we created. After successfull boot at the OS X installer select utilities->Disk utility option. Erase all the existing partitions and format your hard drive using GPT tables.

![Disk Utility]({{ site.url }}/images/DiskUtility.png)

Select the number of partitions you needed and select GUID by clicking options buttons. I created 3 partitions since i wanted to try triple boot later.This installation will automatically create recovery drive and EFI partitions. These partitions are generally hid. When you are done format the partition by selecting apply. 

After the installation you might not be able to boot from the hard disk directly. Dont panic use the USB drive to boot into the OS X partition and complete your installation. Once everything is finished download clover bootloader from this [location](http://sourceforge.net/projects/cloverefiboot/).

Now run the clover installation and select UEFI only install option. Write the bootloader to your OS X partition and this will automatically write to the EFI partition. 

![Clover]({{site.url}}/images/Clover.png)

Now you can remove the USB and boot directly using clover.


Step 3 - Installing Windows 8.1
===============================

By Now i assume you have already have valid working windows 8.1 ISO file. Now boot in your hackintosh and select Boot camp from application -> utilities -> Boot Camp. In the Bootcamp wizard select the option to create windows installation usb. There are numerous ways to create the bootable windows USB. I found this way easy compared to others. Run the wizard and once its complete you should be in possession of a bootable windows 8.1 flash drive which is also formated using GUID parition.


Now plug-in your bootable usb and Run the windows setup. Since we are already using EFI it should automatically take you to the partition selection dialogue. In that select the ntfs partition we have created earlier. If you haven't created the ntfs partition you can hit the delete button to delete and create a new ntfs partition to use. Now select ok and complete the installation.


Step 4 - Optional 
================

By default you might have enabled legacy and UEFI boot. Since we have installed both the operating system using UEFI we can disable the legacy boot support and set the boot priority to boot from os x EFI parition instead of Windows boot manager in your advanced bios option. Else everytime you will boot automatically into windows instead of OS X.Changing the boot priority to OS X partition will cause clover to load as default. Dont worry Clover is capable of detecting windows os as well hence you can boot to either os x or windows from clover bootloader menu. 

Congrats !!!! cheers to yourself . Now you should have a dual boot hackintosh running smoothly without any boot issues. 


Some of the Issues I encountered 
============================

Initially i tried to load OS X along already existing windows 8.1. I know there are tutorials out there to do it. I wasn't lucky enough to make it work. I experienced boot0: done error. I read through the forums to manually write boot1h file to the os X partition and setting as active partition. 

But for some unknown reason i coudn't getting it working. Ideally it should have worked. So i decided to clean wipe my drive and start fresh with GPT instead of MBR. 

if you are encountering boot0: error. This means something is wrong with your MBR . you can trying installing clover or chameleon to the OS X partition. Hope it helps :)





