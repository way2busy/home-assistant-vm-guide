# Installing Home Assistant on VirtualBox

Updated guide to setting up Home Assistant in Oracle VirtualBox on a Windows 10 host system.  

Based on a orginal guide (Installing_HASSOS_on_VirtualBox.pdf) written by Mark M for [DrZzs' livestream](https://www.youtube.com/watch?v=Rg3xTmuHr30).  Rob also did a great walkthrough on his YouTube channel [The Hook Up](https://www.youtube.com/watch?v=vnie-PJ87Eg).

## 1. Installation

Download VirtualBox & VirtualBox Extension Pack [here](https://www.virtualbox.org/wiki/Downloads)

Right click (Run as Administrator) on the executable you downloaded e.g.VirtualBox-6.1.10-138449-Win.exe

Once complete double click on Oracle VM VirtualBox Extension Pack

## 2. Download Home Assistant Image file

Goto [Installing Home Assistant](https://www.home-assistant.io/hassio/installation/) page and download the VMDK file under virtual appliance bullet.

## 3. Setup your virtual machine

Run the VirtualBox application then click the **​New**​ button

![New VM](\images\NewVM.png "New VM")

Give your machine a name (avoid using spaces). This name
will also be the name of the folder that the disks and configuration files will be
stored. The default location for this is *C:\Users\<username>\VirtualBox VMs*.
You can change the default location in *"Machine Folder:"*

- Choose Type: Linux
- Choose Version: Ubuntu (64-bit)

Click *"next"*

![New VM](\images\NewVM2.png "New VM")

Choose the amount of RAM you will be giving the guest, 2048Mb (2Gb) is recommended on a machine with 8Gb or more RAM. Click *"next"*

![New VM](\images\NewVM3.png "New VM")

Choose a hard disk for the Virtual machine
*“​U​se an existing virtual hard disk file​”* and click the folder icon on the right. 

![Image 4](\images\NewVM4.png "Image 4")

Select the vmdk file downloaded previously if avaliable or click *"Add"* then browse to find the file. Then click *“Choose”*

![Image 4b](\images\NewVM4b.png "Image 4b")

then click *“Create”*

![Image 4c](\images\NewVM4c.png "Image 4c")

**BEFORE** we run the VM we need to make some configuration changes:

Click *“File”* >> *“Virtual Media Manager”*

![Image 6](\images\NewVM6.png "Image 6")

Then right click on the “hassos_ova-4.10.vmdk” image and then choose *“Copy”*

![Image 7](\images\NewVM7.png "Image 7")

Choose *“VDI (VirtualBox Disk Image)”* this is the native disk format for VirtualBox. Then click *"Next"*

![Image 8](\images\NewVM8.png "Image 8")

Give the disk a name e.g. hassos_vm (avoid using spaces). Then click *"Copy"*

![Image 9](\images\NewVM9.png "Image 9")

Expand your new .vdi disk, 32GB is the minimum recommended size. Drag the slider or type new size into the box then click *"Apply"*, then *"Close"*

![Image 7b](\images\NewVM7b.png "Image 7b")

Click *“Settings”* then **“Storage”** tab. Click on the *“Controller: SATA”* then the *“Add Disk”* icon. Choose the VDI disk you created above.

![Image 13](\images\NewVM13.png "Image 13")

![Image 14](\images\NewVM14.png "Image 14")

Now you can delete the original .vmdk file you attached by selecting “hassos_ova-4.10.vmdk” and clicking the small disk with the red cross through it. And then hit *“OK”*

![Image 15](\images\NewVM15.png "Image 15")

Click on **"Network"** tab. Change it from *“Nat Network”* to *“Bridged Adaptor”* (if you have more than one network interface on your host make sure you select the one you want to use, usally the ethernet adaptor). Click on the blue triangle for *"Advanced"* settings. Change *“Promiscuous Mode”* from *"Deny"* to *"Allow All”*

![Image 16](\images\NewVM16.png "Image 16")

Click on the **"System"** tab and check *“Enable EFI (Special OSes only)”*. You have another opportunity to change the RAM allocation again here.

![Image 12](\images\NewVM12.png "Image 12")

Congratulations! Thats it, Now start your VM and wait a few minutes for it boot and configure itself.

Open your browser and go to the address http://homeassistant:8123/ if the above doesn’t connect you may have to wait a few minutes more or the problem may be that your router may not support mDns. If so you will need to log into your router and see which IP address was allocated to HASSIO.

## 4. Optional

### Static IP address

To give the VM a static IP address, click on the VM window and press enter. It should now ask you for a login name and this is ```root``` This should give you the hassio prompt.

Now type ```login``` then hit enter.

![Login](\images\login.png "Login")

https://www.tecmint.com/configure-network-connections-using-nmcli-tool-in-linux/
https://manpages.ubuntu.com/manpages/bionic/en/man1/nmcli.1.html

```bash
nmcli c s
nmcli c e "HassOS default"
print ipv4
set ipv4.addresses [hassio ip address]/24
set ipv4.dns [router ip address]
set ipv4.gateway [router ip address]
save
quit
```

Power down your VM using Close > Acpi Shutdown

![Shudown](\images\shutdown.png "Shutdown")

Restart the VM and check you can access via new fixed IP address

### Restore previous config

install samba

copy snapshot

hit refresh in top right of snapshot window

click on snapshot

*"restore selected"*

### Auto Starting our VM

There are a couple of options to auto start our VM

#### Use a Windows Startup Shortcut

1. Right click on the VM then click *"Create Shortcut on Desktop"*
2. Win+R to bring up Run dialog then type `shell:startup` to bring up startup folder
3. Move shortcut to VM from desktop to this startup folder
4. The VM will not start until you login, so optionally you can get Windows to autologin (:warning:**security warning**). Do this by Win+R to bring up Run dialog then type `netplwiz` then uncheck the box *"Users must enter a username and password to use this computer"*

![Windows Autologin](\images\autologin.png "Windows Autologin")

#### Run as a service using VmServiceControl

Setup a service to start our VM (downside: the VM doesn't show as running state in the VirtualBox application) but can be controlled via tray

1. Download and install VmServiceControl from [github:](https://github.com/onlyfang/VBoxVmService/releases) [direct link here](https://github.com/onlyfang/VBoxVmService/releases/download/6.1-Kiwi/VBoxVmService-6.1-Kiwi.exe)
2. Open VBoxVmService.ini in your favourite text editor. Under Vm0 section edit the following entries:

VmName = ``<name of your virtual machine>``  
ShutdownMethod = ``acpipowerbutton``

3. Delete any un-needed blocks (e.g. Vm1) and save

![VmServiceControl](\images\VmServiceControl.png "VmServiceControl")

4. Bring up a command prompt by typing `cmd` into start menu, then selecting *"Run as administrator"*
5. At the command prompt type:

```bash
C:\vms\VmServiceControl.exe -u
C:\vms\VmServiceControl.exe -i 
```

> Any time the .ini is changed you will need to run these to commands to unload then reload the service.

6. Open the **vms** folder and right click on **VmServiceTray.exe** and then *"Copy"*
7. Win+R to bring up Run dialog then type `shell:startup` to open the startup folder
8. Right click and then *"Paste shortcut"*
9. Restart your computer and the VM should start automatically, before you even login.

![VmService tray icon](\images\tray.png "VmService tray icon")

That's it! I hope you found guide useful :grinning: