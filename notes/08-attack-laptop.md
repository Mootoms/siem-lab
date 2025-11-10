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

After a good amount of troubleshooting it looks like I'm settling with just plugging up ethernet to the laptop and here's why:
I need this whole lab to be on the same network for it to work, the issue is, the router provided by my ISP isolates Wi-Fi from wired LAN for security by default. Like I previously mentioned, using Wi-Fi on the laptop both devices were on the same subnet, but pings and DNS queries to DC failed. So I logged into my router to see if I could disable something (like AP isolation, Guest) that would be causing this. However, the router silently isolates preventing direct communication. So my only options were to set up my own router (which I’d previously done for Pi-hole but later repurposed for Proxmox hardware), or get a managed switch (not practical right now). Since there was nothing I could do to replicate more of a realistic environment I settled with connecting the Windows laptop via Ethernet to the same switch (unmanaged) as the Proxmox host. This place both on the same broadcast domain, restoring full connectivity.

Of course issues didn't end there, after provisioning the Samba AD domain controller, Windows clients couldn't resolve DNS queries. Testing with: "nslookup lab.local 192.168.1.109" - it timed out. Running "sudo ss -tulpn | grep :53" - showed systemd-resolved occupying port 53 (the DNS port), preventing Samba's internal DNS from binding.
After some research I learned that Ubuntu's default DNS service, systemd-resolved, listens on 127.0.0.53 for local name resolution. Samba's AD DC also need to run it's own DNS server on port 53 to handle domain queries. Seems simple but at the time I didn't know that both can't use the same port simultaneously.
Now I didn't solve this all on my own, and it didn't get solved on the first attempt but what finally worked was:
1. Disabling and masking systemd-resolved so it wouldn't start automatically.
2. Removing the symlinked /etc/resolv.conf and replacing it with: "nameserver 127.0.0.1" (so the server points DNS back to itself).
3. Restarting the Samba AD DC service.
After that "ss -tulpn | grep :53" showed: dns[master] ... *:53
confirming Samba now owned the DNS port.

Dig and nslookup now worked.

