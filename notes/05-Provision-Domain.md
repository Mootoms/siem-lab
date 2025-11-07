10/31/25

	~Creating actual Domain

Now it's time to turn this blank Ubuntu VM into the central authority of my lab network - or a domain controller that handles:
	- Authentication (Kerberos)
	- Directory data (LDAP)
	- Name resolution (DNS)
And that is why I installed the samba tool as it is something that does all of this. I had to have this explained to me as even though I have a rough understanding of what's going on I needed a detailed description. The goal is to create a domain controller on Ubuntu, which I understood. Here, Samba can act as a Windows AD replacement, and the commands I'll be using initialize the AD database and Kerberos realm; follow up commands copy configuration and start the service. The outcome will be the Ubuntu VM now authenticates accounts for LAB.LOCAL.
I'm also just understanding that Samba 4 is the equivalent of Windows Active Directory Domain Services for Linux; running a LDAP directory (stores accounts, groups, policies), runs a Kerberos Key Distribution Center (issues logon tickets), and runs DNS to tell clients where domain services live.

	The core command:
sudo samba-tool domain provision \
  --use-rfc2307 \
  --realm=LAB.LOCAL \
  --domain=LAB \
  --server-role=dc \
  --dns-backend=SAMBA_INTERNAL

And this is all of the above syntax explained:

|`sudo`| Run as root; provisioning needs to write to protected directories.

|`samba-tool domain provision`| Creates a brand-new AD database structure.

|`--use-rfc2307`| Adds UNIX UID/GID fields so Linux systems can use the same accounts.

|`--realm=LAB.LOCAL`| Defines the Kerberos realm (uppercase, like a DNS zone).

|`--domain=LAB`| Short NetBIOS name shown in Windows logon screens.

|`--server-role=dc`| Declares this host as a domain controller (not a member).

|`--dns-backend=SAMBA_INTERNAL`| Enables Samba’s built-in DNS server instead of external BIND.

However, when I first ran this since it's a VM I had to manually type out the commands (I know I could SSH but I'm trying to force learn when I can), and I had a typo somewhere in the syntax. And either that, or when Samba was initially installed, a default config was created meant for simple file sharing. And since the domain provision process must generate it's own new one, it posted an error for me.
Trying to resolve it I went down a rabbit hole, and got hung up running: "systemctl --nopager --full status samba-ad-dc", showing "Active: Failed". After looking it up, the most likely issue is that Samba's internal DNS tried to bind to port 53, but something else is already using that port. The AD DC can't start until it owns DNS.
After a few failures I ran "echo "127.0.0.1   dc1.lab.local dc1" | sudo tee -a /etc/hosts" to tell Ubuntu that dc1.lab.local refers to itself (127.0.0.1) and stops sudo and other programs from trying to ask the DNS server that isn't ready yet.
Hilariously after resolving that, after I ran Samba again it still failed, but when I ran "sudo ss -tulpn | grep :53" I got nothing, meaning nothing is listening on port 53, so Samba tried to start but stopped for another reason, not a DNS conflict (I solved a problem that wasn't there).
I resorted to running "sudo journalctl -u samba-ad-dc --no-pager | tail -40" to see the last 40 logs and hope to spot the issue. And from first look the error "ERROR: smbd is already running. File /run/samba/smbd.pid exists and process id 2809 is running." So right now, going off of that the file-server process is still active in "standalone" mode from before the AD controller started. The AD DC also launches it's own internal smdb, so the conflict makes the service fail. 
So:
I'll disable the regular Samba daemons with:
sudo systemctl stop smbd nmbd winbind
sudo systemctl disable smbd nmbd winbind

Clean up any leftover PID files with:
sudo rm -f /run/samba/*.pid

And hopefully be able to start only the AD DC service. And it did, active and running. Now going back and having an LLM explain it back to me, my issue was that when Ubuntu installed Samba, it automatically started the regular file-sharing services (smbd, nmbd, and winbind). These are meant for a stand-alone file server, not a domain controller.
Later, when I ran samba-ad-dc, it tried to start it's own all-in-one "samba" process, which includes it's own internal smbd. But the old smbd was still running, already using the same ports and PID file. And if I was to condense all the troubleshooting (I didn't know exactly what the issue was initially, first assuming it DNS issue), I had to stop and disable the old daemons so only the domain-controller service runs. And now the AD controller owns all Samba functions in one clean process. Essentially a port and PID conflict overexamined.

