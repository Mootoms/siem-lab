10/22/25

	~ Proxmox Clean Install
Step one is to set up Proxmox, now I've already done this, a few times even. the SFF Lenovo I got initially was to run the Home Assistant and it evolved from there, and because of that I ended up building up and wiping it many times, a few of the wipes going back to Proxmox for a clean slate.
Right now after boot it has Proxmox set up and running Home Assistant as a VM. However I dedicated all the resources to it and since I plan on toying around with a SIEM setup I'll be wiping it. For what it counts Home Assistant runs well, with Zigbee integration, and I'm sure if I ran the ethernet cables directly to the router connected to the system I'd be able to run all the sensors and cameras with little further setup. 
After deleting Home Assistant and the 2tb HDD I had trouble with mounting (it was mounted as thinpool, not sure what that means but I was exhausting every option I had at that point), I'm left with the Datacenter dropdown, where pve lies with localnetwork and local (pve) and local-lvm (pve) under it. Local (pve) has 4.41GB of the 41.49GB used up but I'm assuming that's the Proxmox install. It's not a fresh install but as clean as it needs to get.

	~Storage Layout Decided
Now the last time I was playing around with this I had a hard time managing the storage as the 2tb HDD gave me trouble mounting onto the VM. I'm not sure if I'll run into the same problem because the way I see it the issue stemmed from me wanting to mount the HDD onto Home Assistant to use to store video from the cameras, but since HA itself is a VM when it shuts down so does the link between it and the storage. At least at the moment I think the solution would of been a script I'd have to somehow get to run on every launch that automates the mounting process.
But now I'll be needing the HDD for the VMs themselves.

Because the NVME is probably 120Gb and is storing the host OS, I'll be looking to not touch local or local-lvm for VM storage or backup of any kind. But to be honest, I get that local is probably the m.2 chip in the Lenovo, but why does it get split into two categories (local and local-lvm).
Learning experience, proxmox automatically split the 128gb ssd into three parts, 1-2gb EFI/boot, and then the remainder down the middle for the root file system (local) and LVM thinpool (local-lvm).
Local being the directory that will store the core, like the ISO images and config files, something I wont be touching unless something very specific comes up, and local-lvm is reserved for VM disks. What I find strange is why it didn't automatically do anything with the HDD.
Looks like the ideal strategy would be to remove local-lvm and reclaim that storage for the root filesystem, and instead transfer that load on the 2tb HDD. And now I'm learning that Proxmox only touches the disk you install the OS on, answering why the HDD wasn't touched.
Before I move on I'll try to se the storage up, removing the local-lvm cutout and returning the storage to local, and setup the HDD in its place.

Started out with a basically clean install of Proxmox, which takes the 128GB SSD by default and splits it, reserving half for VM use. The 2TB SSD isn't detected by default because Proxmox doesn't automatically use any extra disks. But because the SSD is small I deleted the local-lvm and returned the storage space to local. Then I added the HDD as a directory storage and called it vm-storage, then directed it as the main option when setting up VMs. To mount it I created a mountpoint at /mnt/pve/vm-storage and I did it through the GUI not the command line. Then made sure that in the dropdown when creating it disk image + backup files was ticked so I know that Proxmox knew I wanted to use it for VMs and backing up. 
Then I removed local-lvm which was the partition created on first boot because it assumed I'd be also using it for storage and lived on the tiny NVME, and now I'm using the HDD in its place for VM disks. Again, did all this through the GUI, by destroying the node then the thinpool data that deletes the LVM volume freeing the space it took up.
And finally to reclaim the space after destroying the thinpool I had to identify the LV by using the shell command: "lvs" to show the logical volumes and names. Then to extend the LV I used: "lvextend -l +100%FREE /dev/pve/root" to take all the remaining space in the volume group. And then resized the filesystem with: "resize2fs /dev/pve/root" = the filesystem must be resized to match the larger LV.
The result was a root filesystem expanded to ~109 GB. All free space from destroyed thin pool is now useable bu OS, ISOs and templates.
- VG = Volume group, a pool of physical disk space
- LV = logical volume, like a virtual partition inside VG
- Thinpool = specialized LV for efficient VM disk storage
Why separate storage
- OS + small files on SSD -> fast and safe
- Large VM disks_ backup on HDD -> more space, less critical speed
Filesystem resize vs LV extend 
- LV extend - allocate more raw space
- resize2fs = tell the filesystem to actually use that space


