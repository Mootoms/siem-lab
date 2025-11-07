10/23/25

	~Establishing a Ubuntu Server
At this point I just need to setup a ubuntu server to use for the tinkering. I've set up VMs before and even though it was relatively easy, that was mainly due to the fact that i was hand held by chatGPT and had no real understanding of the process. 
Given that I know the motions and have navigated Proxmox before that was not a problem initially. What gave me trouble was the understanding of what I was actually pressing when it came to configuring and organizing. The first phase was to clean up and set up a clean environment, that mainly being deleting the unnecessary configurations from initial boot and registering the extra hardware that wasn't set up from the start. from there, I booted up a Ubuntu server ISO in a VM to test if everything is running fine and I can snapshot without any issues given that I'm running it purely on the untested HDD. A few things I learned from that setup:
- Proxmox required memory to be measured in MiB not GB, where 1GB = 1024MiB
- Proxmox has a way to download an ISO directly onto the SSD with a URL, but to get it I've only ever copy and pasted from ChatGPT commands. Since I learned that those .iso files show their url when you press download, but you never actually see it as most people have no need for that. So I went to their release page and grabbed the link that way.
- vmbr0 bridge is the way Proxmox attaches the VM to my LAN through its bridge interface.
After that it was simple given I've done it a few times before, after I Ubuntu was installed I unmounted the CD drive and rebooted and logged into a fully running VM shell. The SSH plugin was an option to install during the installation setup of Ubuntu server so it was already included. I tested if it works with windows powershell on my main machine with: ssh user@ip and it went through no problems (finding the ip address via ip a command on the headless machine, something I had to learn early on).
A few things I'm needing to define and understand, I have a general knowledge of most but I'll still redefine them below in my own-ish words for understanding and memory sake:
- Host Network: The LAN inside my home, the same network all my local devices use. The network itself is not owned by the isp, and that includes the Proxmox server, my VM, and any other laptop or desktop plugged to the same router in any capacity.
- Bridge interface: vmbr0, is the interface Proxmox uses by default to link my VMs to the physical NIC on the host so my VMs appear on my home LAN as it's own devices.
- IP Scheme: This was a new definition for me, and it means the address plan of my LAN -- the range my router gives out and the actual IPs in use. And like I guessed, the way I'd find them is by running ip a in a console and noting the output.
- Internal Domain Name: OT a hostname. It apparently is a private domain suffix that I'll be using for my active directory or DNS setup, and it does not have to exist on the internet. Basically naming my devices.
- Subnet mask: is a term I vaguely remember as being something that the router decides organizing the IP addresses it has to sift through that are part of it's LAN.
- Gateway IP: The router's IP address on the LAN. The device that forwards traffic out to the internet.
- DNS: It translates names to IPs, I have my router taking care of that since it does not support port forwarding.
Having a basic understand of those terms is essential as the next step was build a network layout draft:
1. Network Name: Home LAN
2. Subnet: 192.168.1.0/24
3. Gateway: 192.168.1.254
4. DNS: 192.168.1.254
5. Bridge: vmbr0
6. Proxmox Host: 192.168.1.97/24 (Web UI: https://192.168.1.97:8006)
7. Ubuntu Server: 192.168.1.109/24
8. Windows PC: 192.168.1.102
9. Internal Domain: mootom.lab

A few commands I used here that were important:
- On Ubuntu: ip a, ip route, cat /etc/resolv.conf
- On Windows: get-netipconfiguration
- Ping test: ping xxx.xxx.x.xxx

Something I should do is add DHCP reservation for Proxmox and Ubuntu so the IPs don't change, setting the IPs as static. I'm aware, but I've done that before so it's nothing new, and I'd rather have it backfire and have to fix it for learning purposes. At this point I've achieved everything I've been shooting to accomplish this week.
  

