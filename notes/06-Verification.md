11/1/25

	~Authentication Checks

Now it's time to fact check, I need to run lookup and authentication checks to prove the controller actually serves LDAP, DNS, Kerberos.
Starting with:

host -t SRV _ldap._tcp.lab.local
host -t SRV _kerberos._udp.lab.local

Hoping it would respond with lines showing my dc1.lab.local and port numbers, that's not what happened however. Both connections timed out and the servers could not be reached. Not something I could identify myself that quick but LLM quoted it as the Ubuntu VM still using the system stub resolver instead if Samba's DNS service.
Basically meaning even though Samba AD DC is running, the system is still asking a different DNS process for lookups.
Firstly using "sudo ss -tulpn | grep :53" to see if samba is successfully binding to port 53, which it is. Then I removed any old DNS symlink and created a new file that points to Samba with: "sudo rm -f /etc/resolv.conf
echo "nameserver 127.0.0.1" | sudo tee /etc/resolv.conf"
Then just verified with "cat" to make sure I see "nameserver 127.0.0.1". And lastly, restarted Samba's AD DC, checked if it's active and tested the DNS again. And all good, LDAP is reachable on port 389 at dc1.lab.local, and Kerberos on port 88.

Small hiccup, but when I went to finish it up and run "knit administrator | klist" I required a password, although I don't remember setting one. But after looking it up I could just set one now with "sudo samba-tool user setpassword administrator" (there's a password set for me at some point I wasn't aware of).
But now "klist" shows that everything is working perfectly. My Kerberos authentication system is now operational, and klist shows a valid TGT (Ticket Granting Ticket) issued by my new domain LAB.LOCAL.