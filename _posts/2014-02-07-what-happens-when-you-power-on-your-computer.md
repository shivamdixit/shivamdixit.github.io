---
layout: post
title: "What Happens When You Power On Your Computer"
description: "This article explains how each bit of your system is flipped when you power on your computer"
category: Linux
tags: [Linux, GRUB, Operating System]
imagefeature: cover12.jpg
comments: true
share: true
---

###Introduction

Ever wondered what all goes inside your computer when you switch on the power button? How is an Operating System loaded ? How you are presented with an user friendly GUI screen? Through this post I will peek into the booting process of UNIX like Operating Systems and try to answer some of the questions. Some of the things discussed in this post are also valid for Windows operating system.

###An overview of the boot process
![overview]({{ site.url }}/images/power-on-post/sequence.gif "Image Courtseyt: IBM")

When the computer is switched on, it's of no use because the data stored in the memory(RAM) is garbage  and there is no Operating System running. The first thing motherboard does is to initialize its own firmware and get the CPU running. Some of the CPU [registers](http://en.wikipedia.org/wiki/Processor_register) including Instruction Pointer (EIP) have predefined values. In x86 systems the initial value of the EIP is `0xfffffff0` and the instruction stored at this memory location is executed. The instruction is `JMP` (JUMP) to a Read Only Memory (ROM) which contains the [BIOS](http://en.wikipedia.org/wiki/BIOS) and its code starts executing.

###Functions of BIOS

__POST__ (Power On Self Test) to ensure that the various components present in the system are functioning properly. If video card is missing or not functioning properly motherboard emit beeps since error cannot be displayed. Beeps are emitted according to *Beep Codes* of the motherboard and it varies from one motherboard to other. A comprehensive list of beep codes can be found [here](http://www.computerhope.com/beep.htm). If the computer passes the video card test, manufacturer logo is printed on the screen.

It initializes the various hardware devices. It is an important process so as to ensure that all the devices operate smoothly without any conflicts. BIOSes following [ACPI](http://en.wikipedia.org/wiki/Advanced_Configuration_and_Power_Interface) create tables describing the devices in the computer.

It looks for an Operating System to load. Typically, the BIOS will search it in Hard Drives, CD-ROMs, floppy disks etc. The actual search order can be configured by the user by changing `Boot Order` in BIOS settings. If BIOS cannot find a bootable operating system it displays an error message "Non-System Disk Error".

Generally the operating system is present in the hard disk. We will confine our discussion to how operating system boots from the hard disk.

###Master Boot Record

The first sector of the hard disk is called Master Boot Record ([MBR](http://en.wikipedia.org/wiki/Master_boot_record)). The structure of MBR is operating system independent. It is of 512 bytes and it has mainly two components. The first 446 bytes contain a special program called *Bootstrap Loader*. The next 64 bytes contains a partition table. A partition table stores all the information about the partitions in a hard disk and [file system](http://www.linfo.org/filesystem.html) types (a file system describes how data will be stored and retrieved from the partition). A partition table is required to boot up the operating system. The last two bytes of MBR contains a *magic number* `AA55`. It is used to classify whether the MBR is valid or not. An invalid *magic number* indicates that the MBR is corrupt and machine will not be able to boot. A typical structure looks like:

![Master Boot Record]({{ site.url }}/images/power-on-post/mbr.png "Image courtsey: Microsoft")

###Bootstrap Loader

Bootstrap loader or the *boot loader* contains the code to load an operating system. Earlier Linux distributions used *LILO* (LInux Loader) bootloader. Today, most of the distributions use  *GRUB* (GRand Unified Bootloader) which has many advantages over *LILO*. BIOS loads the bootstrap loader into the memory (RAM) and starts executing the code.

Boot loader of traditional operating systems like Windows 98 used to identify an *active* partition in the hard disk by looking at the *active* flag of partition table and loading its *boot sector* into the memory. *Boot sector* is the first sector of each **partition** in contrast to MBR which is the first sector of the hard disk. The *boot sector* is of 512 bytes in memory and contains code to boot an operating system in that partition. However boot loaders like *GRUB* and *LILO* are more robust and boot process is not so straight forward.

![GRUB]({{ site.url }}/images/power-on-post/grub.jpg "GRUB menu of a system with two operating systems")

Booting an operating system with GRUB is a two stage process: **stage 1** and **stage 2**. In some cases an intermediate stage *1.5* may also be used to load stage 2 from an appropriate file system. Stage 1 is the boot loader code itself and its task is to only call the stage 2 which contains the main code. This is done because of the tiny size of the stage 1 (512 bytes). GRUB stage 2 loads the Linux **Kernel** and **initramfs** into the memory.

Kernel is the core component of an operating system. It has complete control of all the things happening in a system. It is the first part of the operating system to load into the memory and remains there throughout the session.

To access a file system it must be first [mounted](http://www.linfo.org/mounting.html). When kernel is loaded into the memory none of the file system is mounted and hence initial RAM based file system (initramfs) is required by kernel to execute programs even before the *root* file system is mounted. Kernel executes a `init` (initialization) program which has [pid](http://en.wikipedia.org/wiki/Process_identifier)=1. It is a [daemon](http://en.wikipedia.org/wiki/Daemon_(computing) process and continues to run until the computer is shut down. It also load the modules and drivers required to mount the *root* file system. Linux stores information about the major file systems in a file `/etc/fstab`

###init

**init** is the last step of the kernel boot sequence. It looks for the file `/etc/inittab` to see if there is an entry for `initdefault`. It is used to determine initial run-level of the system. A *run-level* is used to decide the initial state of the operating system. Some of the run levels are:

<table border="1" style="margin: 0 auto 2em auto;">
	<thead>
		<tr>
			<th>Level</th>
			<th>Description</th>
		</tr>
	</thead>
	<tbody>
		<tr>
			<td>0</td>
			<td>System Halt</td>
		</tr>
		<tr>
			<td>1</td>
			<td>Single user mode</td>
		</tr>
		<tr>
			<td>3</td>
			<td>Full multiuser mode with network</td>
		</tr>
		<tr>
			<td>5</td>
			<td>Full multiuser mode with network and X display manager</td>
		</tr>
		<tr>
			<td>6</td>
			<td>Reboot</td>
		</tr>
	</tbody>
</table>

The above design of init is called *SysV*- pronounced as [System five](http://en.wikipedia.org/wiki/UNIX_System_V). Several other implementations of *init* have been written now. Some of the popular implementatios are *systemd* and *upstart*. Upstart is being used by ubuntu since 2006. More details of the upstart can be found [here](https://help.ubuntu.com/community/UbuntuBootupHowto).

The next step of *init* is to start up various daemons that support networking and other services. X server daemon is one of the most important daemon. It manages display, keyboard, and mouse. When X server daemon is started you see a Graphical Interface and a login screen is displayed.
