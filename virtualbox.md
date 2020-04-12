

### Downloads
[Downloads – Oracle VM VirtualBox](https://www.virtualbox.org/wiki/Downloads)
[Download Ubuntu](https://ubuntu.com/download/desktop)

### Install Virtual Box
Allocate 4gigs (4096 MBs) of ram

Follow most of the recommended stuff:

- Choose create hard disk
- Choose VDI
- Choose dynamically allocated
- Keep starting size at 10gigs

### Install Ubuntu on Virtual Box
Click on it in the virtual box and hit start

For selecting the startup-up disk use the Ubuntu iso file that was downloaded and hit start.

Then hit 'install ubuntu' and go through the process:

- Normal installation
- Download updates on install
- Erase disk and install ubuntu (this doesn't impact windows, only the virtual disk)

Hit restart when prompted

Then when it says to remove the medium etc, just hit enter anyway and it works

### Add Guest Additions
In the virtual box menu bar:

- Click 'devices'
- Click 'Insert Guest Additions CD Image'
- Then hit run
- After the image is downloaded, open it and install
- Then shut it down and start it up again
- Then see the new menu in devices that allow for cross drag and drop, shared clipboard, etc.

### Update/Upgrade/Maintenance
- Update/Upgrade - `sudo apt update && sudo apt ugrade -y`
- Maintenance - `sudo apt autoremove`

### Speed up Ubuntu

For windows:
- Add the VirtualBox VMs folder as an exclusion to Windows Security

---

**Following are steps for VM Box Settings**

To get to Ubuntu Settings, right click on the machine (In the VM VirtualBox Manager) to update settings for and choose settings.

- Add a second core in the VM Manager - Click System, choose processor and add a second core (the machine must be powered off to edit this).
- Update Display to maximum - Choose Display and move Video Memory to maximum (128 MBs)
- Enable 3D Acceleration - (also in Display section)
- Update to use host I/O - Choose Storage, select 'Controller: SATA', and check the box labeled "Use Host I/O Cache"
- Connect using bridged adapter - In Network, instead of using the standard NAT, use a bridged adapter.
- Disable the bottom bar - In User Interface, uncheck the bottom bar

---

**Following are steps for Ubuntu**

Launch the virtual machine then follow the below setps:

- Enable Write Cache - Search for disks and open the hard disk for the virtual box, click settings or the dropdown to find Drive Settings, click on the Write Cache tab, and enable write cache
