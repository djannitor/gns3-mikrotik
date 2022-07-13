# MikroTik CHR on GNS3 Qemu

Deploying the MikroTik Cloud Hosted Router image in GNS3 Qemu on Windows 10.

While [GNS3 recommends using the GNS3 VM on Windows and Mac OS](https://docs.gns3.com/docs/emulators/which-emulators-should-i-use), that didn't work for the MikroTik CHR on my host PC (Intel Core i5-3570), possibly because I'm using VirtualBox rather than VMware.

The GNS3 documentation does recommend using Qemu for creating and using Cisco GNS3 topologies.

Skip to "Re-do with GNS3 Local Qemu" after downloading the pre-requisite MikroTik CHR image if you're not interested in my failed attempt to use GNS3 VM.

## Pre-requisites

This document assumes you've already downloaded and installed GNS3.

To download the MikroTik image, go to

https://mikrotik.com/download

Find the Cloud Hosted Router section. For Qemu, download the raw disk image for 7.3.1 stable.

https://download.mikrotik.com/routeros/7.3.1/chr-7.3.1.img.zip

Extract the image from the zip file.

## Importing the MikroTik CHR into GNS3 VM

In GNS3 > Edit > Preferences > QEMU > Qemu VMs, click "New".

On "Server" screen, change the "Server type" to "Run this Qemu VM on the GNS3 VM" and click "Next".

On the "QEMU VM name" screen, choose a name for the Qemu VM, such as "MikroTikQemu", and click "Next".

On the "QEMU binary and memory" screen, accept the default 256 MB RAM setting and click "Next".

On the "Console type" screen, accept the default "telnet" option and click "Next".

On the "Disk image" screen, select "New Image" and choose the CHR img file you downloaded from MikroTik and click "Finish" after the upload completes.

Click the "Edit" button". Notice in "General settings" that GNS3 added the MikroTik VM as "End devices". Change this to "Routers". Change the "Symbol" accordingly. Optionally, check the box to "Auto start console".

In the "Network" tab, GNS3 defaulted to one network adapter, so click the "Edit" button and set "Adapters" to "2" (or as many as needed) and click "OK".

Click "OK" to close the "Preferences" window.

The MikroTik CHR Qemu VM should now should up in GNS3 Routers.

## Running the MikroTik CHR in GNS3 VM

Create a new project in GNS3 and add the MikroTik CHR Qemu VM.

Start the router and connect to the console.

While GNS3 indicates a green status for the MikroTik Qemu router, there's no response on the console and GNS3 Servers Summary shows the GNS3 VM's single CPU pegged at 100%. Re-configuring the GNS3 VM to use two CPU cores resulted in the CPU utilization pegged at 50% and a non-responsive console.

## Re-do with GNS3 Local Qemu

In GNS3 > Edit > Preferences > QEMU > Qemu VMs, click "New".

On "Server" screen, change the "Server type" to "Run this Qemu VM on my local computer" and click "Next".

On the "QEMU VM name" screen, choose a name for the Qemu VM, such as "MikroTikQemuLocal", and click "Next".

On the "QEMU binary and memory" screen, accept the default 256 MB RAM setting and click "Next".

On the "Console type" screen, accept the default "telnet" option and click "Next".

On the "Disk image" screen, select "New Image" and choose the CHR img file you downloaded from MikroTik and click "Finish" after the upload completes.

Click the "Edit" button". Notice in "General settings" that GNS3 added the MikroTik VM as "End devices". Change this to "Routers". Change the "Symbol" accordingly. Optionally, check the box to "Auto start console".

In the "Network" tab, GNS3 defaulted to one network adapter, so click the "Edit" button and set "Adapters" to "2" (or as many as needed) and click "OK".

Click "OK" to close the "Preferences" window.

The MikroTik CHR Qemu VM should now should up in GNS3 Routers.

Adding the Qemu CHR to a project and attempting to start it results in an error message that HAXM acceleration support isn't installed on my host (Intel Core i5-3570).

## Install HAXM

HAXM is Intel's Hardware Acceleration Execution Manager:

> a cross-platform hardware-assisted virtualization engine (hypervisor), widely used as an accelerator for Android Emulator and QEMU

Downloads for Windows and Mac OS are available at

https://github.com/intel/haxm/releases

Install HAXM and reboot the PC.

## Running the MikroTik CHR in GNS3 Local Qemu

Add the previously created Local Qemu CHR to a project and the router now successfully starts.

I'm able to login to the router, configure it, and establish network connectivity with a second CHR instance.
