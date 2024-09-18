## Port Forwarding with Windows Netsh

[Netsh](https://docs.microsoft.com/en-us/windows-server/networking/technologies/netsh/netsh-contexts) is a Windows command-line tool that can help with the network configuration of a particular Windows system. Here are just some of the networking related tasks we can use **Netsh** for:

1. Finding routes
2. Viewing the firewall configuration
3. Adding proxies
4. Creating port forwarding rules


Let's take an example of the below scenario where our compromised host is a Windows 10-based IT admin's workstation **(10.129.15.150,172.16.5.25).** Keep in mind that it is possible on an engagement that we may gain access to an employee's workstation through methods such as social engineering and phishing. This would allow us to pivot further from within the network the workstation is in. 


![Diagram](/Port-Forwarding-with-Windows-Netsh/images/diagram.png) 


### Target host access via RDP 3389 (10.129.42.189)

Username: htb-student
Password: HTB_@cademy_stdnt!


#### Lab objective

Using the concepts covered in this section, take control of the DC (172.16.5.19) using xfreerdp by pivoting through the Windows 10 target host. Submit the approved contact's name found inside the "VendorContacts.txt" file located in the "Approved Vendors" folder on Victor's desktop (victor's credentials: victor:pass@123) . (Format: 1 space, not case-sensitive) 

1. 172.16.5.19 is a DC
2. xfreerdp is available for pivoting
3. Look in folder "Approved Vendors" in victors desktop
4. Victor credentials: Username: victor Password: pass@123


We can use **netsh.exe** to forward all data received on a specific port (say 8080) to a remote host on a remote port. This can be performed using the below command. 

### Using netsh.exe to Port Forward

	C:\> netsh.exe interface portproxy add v4tov4 listenport=8080 listenaddress=10.129.15.189 connectport=3389 connectaddress=172.16.5.25


![Port Forward](/Port-Forwarding-with-Windows-Netsh/images/port-forward.png) 



### Tip to transfer files from Attacker to Target with rdesktop

	$ rdesktop 10.129.41.222 -u htb-student -p HTB_@cademy_stdnt! -r disk:tmp=/home/kali/tools 

### netsh.exe help

	netsh.exe /?

	Usage: netsh.exe [-a AliasFile] [-c Context] [-r RemoteMachine] [-u [DomainName\]UserName] [-p Password | *]
		        [Command | -f ScriptFile]

	The following commands are available:

	Commands in this context:
	?              - Displays a list of commands.
	add            - Adds a configuration entry to a list of entries.
	advfirewall    - Changes to the `netsh advfirewall' context.
	branchcache    - Changes to the `netsh branchcache' context.
	bridge         - Changes to the `netsh bridge' context.
	delete         - Deletes a configuration entry from a list of entries.
	dhcpclient     - Changes to the `netsh dhcpclient' context.
	dnsclient      - Changes to the `netsh dnsclient' context.
	dump           - Displays a configuration script.
	exec           - Runs a script file.
	firewall       - Changes to the `netsh firewall' context.
	help           - Displays a list of commands.
	http           - Changes to the `netsh http' context.
	interface      - Changes to the `netsh interface' context.
	ipsec          - Changes to the `netsh ipsec' context.
	lan            - Changes to the `netsh lan' context.
	mbn            - Changes to the `netsh mbn' context.
	namespace      - Changes to the `netsh namespace' context.
	netio          - Changes to the `netsh netio' context.
	p2p            - Changes to the `netsh p2p' context.
	ras            - Changes to the `netsh ras' context.
	rpc            - Changes to the `netsh rpc' context.
	set            - Updates configuration settings.
	show           - Displays information.
	trace          - Changes to the `netsh trace' context.
	wcn            - Changes to the `netsh wcn' context.
	wfp            - Changes to the `netsh wfp' context.
	winhttp        - Changes to the `netsh winhttp' context.
	winsock        - Changes to the `netsh winsock' context.
	wlan           - Changes to the `netsh wlan' context.

	The following sub-contexts are available:
	 advfirewall branchcache bridge dhcpclient dnsclient firewall http interface ipsec lan mbn namespace netio p2p ras rpc trace wcn wfp winhttp winsock wlan

	To view help for a command, type the command, followed by a space, and then


### Verifying Port Forward


![Verify](/Port-Forwarding-with-Windows-Netsh/images/verify.png) 


After configuring the **portproxy** on our Windows-based pivot host, we will try to connect to the **8080** port of this host from our attack host using **xfreerdp.** Once a request is sent from our attack host, the Windows host will route our traffic according to the proxy settings configured by **netsh.exe.**

![Config](/Port-Forwarding-with-Windows-Netsh/images/config.png) 


![DC](/Port-Forwarding-with-Windows-Netsh/images/dc.png) 


Flag on victor Desktop DC 172.16.5.19
N1c3Piv0t

