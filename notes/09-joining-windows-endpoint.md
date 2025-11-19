    11/18/25

	HP Laptop

After the Network + DNS Verification I need to join the windows endpoint to the lab.local domain so it can authenticate against the Ubuntu domain controller (dc1.lab.local). I get what I'm trying to do, but to define it deeper I had to break down the process.
I need the Windows host to trust the DC for authentication, register the computer account in Active Directory and that would allow future logins and group policies to flow through that trust.

First I set the preferred DNS to my DC's IP through control panel on the HP laptop. And immediately after running nslookup it cannot find dc1.lab.local. The issues are immediate but it's expected at this point. So at this point the DNS didn't apply, there's a secondary DNS or its using IPv6 DNS. 
Now I did only change IPv4, and after looking back IPv6 is enabled. Since IPv6 always overrides IPv4. Unchecking it worked.
However this is where I ran into a major issue; I'm running Windows 10, and the build I have installed does not support joining a domain (My next step, the domain option was grayed out). Learning this there is no workaround that's efficient, and even if I was to find a workaround I suspect it would just constantly cause issues going forward. So I'll just bite the bullet while I'm still early into setting this up and reinstall the OS.