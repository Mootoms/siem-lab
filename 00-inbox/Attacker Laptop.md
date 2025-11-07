11/5/25

	~Windows Prep
I have s old HP laptop I can use for this lab, so I wiped it and booted up Windows OS on it since I already had a Ventoy USB ready. After set up I needed the laptop on the same network as the Ubuntu DC and I did did that by changing and matching the IP, Subnet, Gateway and preferred  DNS through control panel.
On setup I just used my name but for the sake of organization I'll also rename it to "win10-lab" through System properties and rebooted.

This is where I ran into a slight issue. I matched the network details on the HP laptop with the SIEM network, and neither ping or nslookup would post anything.
First thing I thought is that it was the Firewall on Proxmox or Ubuntu, but they're off.
The next thought was maybe obvious but not to me initially, was that the laptop I'm using isn't connected via ethernet like my main PC or Proxmox server (where Ubuntu is getting internet via Vmbr0). And what I understand now is that because of the Wi-Fi connection, the laptop is on a different broadcast domain, as wireless clients are isolated by default and can't directly reach wired devices unless my router explicitly bridges the traffic between the two.
At this point (from looking it up) I had three options:
1. I could plug the laptop up to ethernet.
2. Run the Windows endpoint as a Proxmox VM
3. Check if my router truly bridges Wi-Fi and LAN
The easy way is clearly to just plug the Laptop up to Ethernet and call it a day, but I imagine in a production environment that's not always possible or the case, so my only out  is to try and figure this bridging error out (or even if this is the problem I'm having).