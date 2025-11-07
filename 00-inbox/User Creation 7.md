11/2/25

	~Adding User to AD

Started by adding a normal user to the AD domain with: "sudo samba-tool user create socuser". First feature to run without issues. AD DC running, functional DNS and Kerberos authentication and my first standard user account stored in LDAP.

	~DNS Testing

To confirm that the domain's internal DNS resolves properly, I run: "dig @127.0.0.1 lab.local" and that shows that my DNS, LDAP and Kerberos stack are all operational.

	~Buffer + Documentation

At this point is where I set my first Snapshot in Proxmox, as I've had things break to the point messing around with other VM's and services on the host to the point where one thing breaking kind of broke everything else, and I did not have a backup. That will also serve as a stopping point for "Week 2":

- **Static IP:** 192.168.1.109

- **Hostname:** dc1.lab.local

- **Services running:** DNS (Domain Name System), LDAP (Lightweight Directory Access Protocol), Kerberos (authentication)

- **Users:** Administrator + socuser