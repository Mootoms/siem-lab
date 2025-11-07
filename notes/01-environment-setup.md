10/21/25

I want to get into the world of IT, and through "accidental" homelab experiments I've learned that my interest leans more towards the cybersecurity side. Even though I'm interested in coding, I know I can apply it and learn later on as I go, through automations and small scrips that make sense or happen on accident through projects.

I've been experimenting with proxmox and different OSs, but now I want to narrow down and create something that I can use in a resume sense. From research it looks like I should be looking to create a SIEM, and the process of doing so/ managing it will teach me the basics.

I have a rough understanding of it, 
A company has a network and it needs to be protected. Open ports can lead to someone getting in and getting a hold of sensitive information. Phising emails etc. And because servers are constantly up, and need to be maintained and changed up, there'd a constant need for supervision. Then there's obviously the aspect of things breaking.

But because of the way I learned about IT, or rather got into it was due to ADs plaguing my home network, and me wanting to get rid of them. I got into hosting a DNS server, which meant I had to dedicate a physical server, as I wanted to also host Home Assistant and maybe some other apps that if I ran on VM id start to bottle neck on.
Through that I learned some basics, but then I discovered that my network isn't actually that safe. There's many services I'm using that I never monitored, or even knew where they're sending information to. 

Few things I learned from setting up the DNS:
 - I should back up a server whenever I can, because when I had something corrupt or break, I was left with the only option to start from scratch.
 - There's services like YouTube or Netflix that actually send the ADs directly baked into the content, so even if I block the domain the AD comes from I still wouldn't be able to avoid it.
 - When I wanted to mane Pihole my DNS my router didn't support port forwarding, and because of that I had no fallback for when Pihole failed. The only way I could have Pihole filter the IP addresses was if all oft hem were directed to it, but that meant if my server goes down, then so does my internet. And I never found a solution for that, apart from getting a new router that would support port forwarding, in which case I believe if Pihole were to fail I'd be able to automatically be able to fall back on the router for internet.
 - When I was setting up Proxmox one issue I ran into a lot was the hardware. I used a Lenovo m720q as my first attempt, and even though I updated the ram and HDD I had issues with performance. For example, I put in a 2tb HDD I initially thought id use for storing video from the cameras and doorbell I installed. But before I was able to run it I ran into the issue of the HDD unmounting on boot. Because the server itself is a host instead of something like a local OS, when I booted up the server and made a VM for the Home Assistant I could mount the HDD but it would unmount if I restarted or something went wrong. I know I could probably find a way, maybe with a script of some sort or a function I missed but I wasn't able to solve it at the time. Another thing is that if I wanted to use an AI plugin for the camera footage monitoring I was quickly bottlenecked by the CPU and GPU. The onboard graphics couldn't preform the transcoding needed for the video, and with only like two cores I could barely run more than a few plugins for Home Assistant let alone multiple VM's in Proxmox.
 - I also found a lot of ports in my home network I didn't know existed. When I was looking for everything in the house to connect to Home Assistant I found many devices ( that I now know almost all internet connected devices have their own IP assigned ) like my roombas, fridge, washing/ drying machines etc. have an IP, and send information outside my network. The way I figured that is when I wanted to connect my roomba to Home Assistant, but the app its registered by was not giving me the password I needed to connect it. And after reading some forums I found that it should be detectable in the handshake the device makes when it makes a request for internet access. I wasn't able to find it, but through my short experimentation with nmap I saw it was doing a whole lot more than just vacuuming .
 - Not something that relates too much, but I did order some cheaper GPUs that were used for mining; repadded and repasted them, and the reflashed them so I could use them for the server.
 - Apart from what I listed, the rest was really just general troubleshooting and stumbling my way though functions I've never touched before.

 For "week 1" "minimum viable outcome" the goal will be a Proxmox host cleaned, baseline VM created successfully, and network plan defined. Not necessarily perfect, but just get it running and if there is some obvious mistakes or easy adjustments I can make I'll do that.
 
 1. Proxmox clean install
 2. Storage layout decided
 3. One Ubuntu VM successfully created and accessible via SSH
 4. Network planning written
 
 This all can only follow once I go through all the hardware I can use for this project and establish a base. I have a few laptops that I need to open up and go through. And also need to wipe and reinstall the Lenovo for a fresh slate. Once I have my hardware reset and fresh I'll move to step one and document in capture log.
    
