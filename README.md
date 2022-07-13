# gns3-mikrotik

Deploying the MikroTik Cloud Hosted Router image in GNS3 on Windows 10 with VirtualBox.

## Installation of pre-requisites

Download GNS3 All-in-one installer from GitHub because I didn't want to create an account on gns3.com

https://github.com/GNS3/gns3-gui/releases/tag/v2.2.33.1

Install with the default settings.

I noticed the GNS3 AIO installer installs an older version of Wireshark (3.4.8) and Npcap (0.9990), so I downloaded and installed the latest versions of Wireshark (3.6.6) and Npcap (1.60)

https://www.wireshark.org/#download

On first run, GNS3 suggests downloading and installing the GNS3 VM. This has a dependency on VMware and will warn you if VMware is not installed.

I don't have VMware so I downloaded the VirtualBox image from:

https://github.com/GNS3/gns3-gui/releases/download/v2.2.33.1/GNS3.VM.VirtualBox.2.2.33.1.zip

Extract the GNS3 VM ova image from the zip archive.

Start VirtualBox and Import the GNS3 VM image (GNS3 VM.ova) with the default appliance settings, but choose the desired location for the VM files (e.g., C:\Homelab) if you don't like the default. Click "Import".

In GNS3 > Help > Setup Wizard, "Run appliances in a virtual machine" is selected by default. Click "Next".

Accept default values in the "Local server configuration" screen and click "Next".

"Local server status" screen should show successful connection to the local GNS3 server. Click "Next".

GNS3 will complain again that it can't find VMware.

On the "GNS3 VM" screen, click "VirtualBox". GNS3 should find the "GNS3 VM" VirtualBox image and populate the "VM name" field with "GNS3 VM". Click "Next".

The GNS3 VM will be started in VirtualBox. Wait until "GNS3 VM" shows green status in GNS3's "Servers Summary" window. Click "Finish".

## Deploying the MikroTik Cloud Hosted Router in VirtualBox

To download the MikroTik image, go to

https://mikrotik.com/download

Find the Cloud Hosted Router section. For VirtualBox, I downloaded the VDI image for 7.3.1 stable.

https://download.mikrotik.com/routeros/7.3.1/chr-7.3.1.vdi

Open VirtualBox and create a New VM. Set the name (e.g., MikroTikv731) and folder for the VM (e.g., C:\Homelab), as Type "Linux" and Version "Other Linux 64-bit" and click "Next". 

VirtualBox will create a new folder as the name of the VM (e.g., C:\Homelab\MikroTikv731).

Accept the default 512 MB memory and click "Next".

Copy the MikroTik CHR VDI image into the VM folder (e.g., C:\Homelab\MikroTikv731).
 
Choose the existing virtual hard disk file as the MikroTik CHR VDI file from the VM folder (e.g., C:\Homelab\MikroTikv731) and click "Create".

By default, there will only be a single Network Adapter attached in NAT mode. This will be the router's WAN interface if you want Internet access. Go into the "Network" settings for the VM and set "Adapter 1" to "Not attached" if you don't want Internet access. You can change this later if desired.

Click the "Adapter 2" tab and click "Enable Network Adapter" as "Not attached" and click "OK". Add additional adapters if desired.

> The above step of defining multiple network adapters in VirtualBox might not be necessary?

Start the MikroTik CHR VM and login as "admin" with no password.

You can confirm the NAT connectivity to the Internet with "ping 8.8.8.8".

You can shut down the MikroTik CHR VM as you've confirmed it's working.

## Importing the MikroTik CHR into GNS3

In GNS3 > Edit > Preferences > VirtualBox > VirtualBox VMs, click "New".

On "Server" screen, accept "Run this VirtualBox VM on my local computer" and click "Next".

Choose the MikroTik VM (e.g., MikroTikv731) from the pull-down menu and check the box "Use as a linked base VM" (this will allow you to add multiple MikroTik router instances to the network topology) and click "Finish".

Click the "Edit" button". Notice in "General settings" that GNS3 added the MikroTik VM as "End devices". Change this to "Routers". Change the "Symbol" accordingly. Also change "Console type" from "none" to "telnet".

In the "Network" tab, GNS3 defaulted to one network adapter, even if you created the VM with two, so click the "Edit" button and set "Adapters" to "2" (or as many as needed) and check the box "Allow GNS3 to use any configured VirtualBox adapter" and click "OK".

Click "OK" to close the "Preferences" window.

The MikroTik CHR VM should now should up in GNS3 Routers.

## Running the MikroTik CHR in GNS3

Create a new project in GNS3 and add the MikroTik CHR.

Start the router and connect to the console.

Use the `interface/print` command to show the interface names as they may not be "ether1" and "ether2".

Add an IP address using the following syntax:

```
ip address add address=10.10.10.1/24 interface=ether3
```

Add a second MikroTik CHR into the topology, start and assign an IP address, then confirm that the routers can ping each other.

Save the router configuration with:

```
system/backup/save
```

## Integrating GNS3 with Microsoft Windows Terminal

The GNS3 All-in-one installer from GitHub doesn't include the Solar-PuTTY tabbed PuTTY client that's included in the installer from

https://www.gns3.com/software/download

and I didn't want to register my email address with SolarWinds.

Because it's convenient to have a tabbed terminal client, a free option in Windows 10 is the Electron-based Microsoft Windows Terminal, which is available from the Microsoft Store or from GitHub:

https://github.com/microsoft/terminal

The GitHub version doesn't auto-update, while the Microsoft Store version does.

Once you've installed Microsoft Windows Terminal, launch a Terminal window and right-click the title bar to access the "Settings" menu.

Click "Profiles > Defaults" from the left-hand navigation, then "Additional settings > Advanced" from the main window.

Turn on the first option "Suppress title changes" and click "Save". This will allow you to set the title of the Terminal tab to the name of the network device in GNS3.

Windows 10 doesn't support telnet by default, so you'll need to enable it by clicking the Windows Start Menu and typing "telnet". Click "Turn Windows features on or off" and enable the "Telnet Client".

In GNS3, go to "Edit > Preferences" and click the "Console applicatons" tab under "General preferences".

Make a backup copy of the default setting:

```
putty_standalone.exe -telnet %h %p -loghost "%d"
```

Click the "Edit" button and replace the default with:

```
wt -w 1 new-tab --title %d PowerShell telnet %h %p
```

Then click "OK".

The above will open consoles as tabs in a new Windows Terminal instance "1", while an already running Windows Terminal window is instance "0".

You may need to press the "Enter/Return" key to display the device's login prompt.

## References

[Download and install GNS3 Virtualbox and MikroTik CHR image version 7 - YouTube -  MAICT Consult](https://www.youtube.com/watch?v=-0N8unW5ZC4)
