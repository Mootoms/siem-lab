10/25/27

	~Static Networking
Proxmox with storage set up properly and Ubuntu server running clean. I now need to set a static IP for the Ubuntu server so it's always reachable at the same address.
To do so, I had to write a .yaml file for the VM since it's not setup on first boot. After identifying the interface, IP and netmask with "ip a" (ens18, 192.168.1.109/24), I looked up a .yaml file structure I can use.

network:
  version: 2
  renderer: networkd
  ethernets:
    ens18:
      dhcp4: no
      addresses: 
      gateway4:
      nameservers:
        addresses: 

That's where I ran into my first issue. After running nano /etc/netplan/01-netcfg.yaml to edit it, it posted an empty page, where I learned the only way I can edit one is if I use admin right to access it with sudo. When that did work and I followed the template (that I couldn't copy/paste due to it being a VM) I learned that it's structure with double space indentations, and using tab would make it unreadable by the OS. 
When I filled out the template the first time:

network:
  version: 2
  renderer: networkd
  ethernets:
    ens18:
      dhcp4: no
      addresses: [192.168.1.109/24]
      gateway4: 192.168.1.1
      nameservers:
        addresses: [1.1.1.1,8.8.8.8]

It posted an error, citing that it couldn't read the "gateway4" line as the syntax is depreciated for Ubuntu (it still works but it now prefers a different syntax that I'll later swap it to). As well as posting a permission error, the permissions were too open by default and world-readable or writable, whereas netplan requires it to be restricted. To fix that I used "sudo chmod 600 /etc/netplan/01-netcfg.yaml".
And to fix the depreciated syntax (to just remove the warning) I swapped the gateways4 line to:

network:
  version: 2
  renderer: networkd
  ethernets:
    ens18:
      dhcp4: no
      addresses: [192.168.1.109/24]
      routes:
        - to: default
          via: 192.168.1.1
      nameservers:
        addresses: [1.1.1.1,8.8.8.8]

That's where I felt confident running "sudo netplan apply" and everything went through (I did get a "WARNING:root:Cannot call Open vSwitch: ovsdb-server.service is not running." which is harmless unless I was using an Open vSwitch, which I'm not).
With that done, to make sure everything works I checked "ip a" and "ip route". where I saw IP mismatches but didn't really register it rightfully to ignorance. But when I ran a ping test I was getting 100% packet loss on every attempt even with googles server.
So looking back on the "ip a and ip route" there was a mismatch, the static configuration was applied, but the VM wasn't reaching my host network. So I ran "cat /etc/network/interfaces" on the pve server itself and found this:

root@pve:~# cat /etc/network/interfaces
auto lo
iface lo inet loopback

iface eno1 inet manual
auto vmbr0
iface vmbr0 inet static
address 192.168.1.97/24
gateway 192.168.1.254
bridge-ports eno1
bridge-stp off
bridge-fd 0
source /etc/network/interfaces.d/*

The configuration was valid, and the VMs are bridged properly. There was a disconnect between the VM's gateway (192.168.1.1) and the host's real gateway (192.168.1.254). And that mismatch isolated the VM, as the gateway in the VM mist match the actual router the Proxmox host uses. To fix it I edited the .yaml file to look like this:

network:
  version: 2
  renderer: networkd
  ethernets:
    ens18:
      dhcp4: no
      addresses: [192.168.1.109/24]
      routes:
        - to: default
          via: 192.168.1.254
      nameservers:
        addresses: [1.1.1.1,8.8.8.8]
        
Then ran "sudo netplan apply" and ran a ping test to make sure it finally runs, and yes, the VM is on a static IP.

Lastly I set the hostname with "sudo hostnamectl set-hostname dc1.lab.local" to dc1.lab local, meaning: dc1 = Domain Controller 1, lab.local = the private domain. Followed by installing samba (renaming it makes it easier for organizing and knowing the name to reference for the next step):
This was a long string for me so I'm breaking it down for myself to remember "sudo apt update && sudo apt install samba krb5-user winbind smbclient dnsutils -y":

	- apt update -> refresh the package list
	- samba -> provides the AD and the file-sharing services
	- krb5-user -> Kerberos client tools for authentication
	- winbind -> links Windows IDs to Linux accounts
	- smbclient -> command-line SMB file- share client (for testing)
	- dnsutils -> adds network test tools (dig, host)
	- -y -> auto-confirms the install

When prompted for the Kerberos realm I put "LAB.LOCAL" and when asked for the servers and admin servers I was able to input "dc1.lab.local" since I changed it just before starting the install.