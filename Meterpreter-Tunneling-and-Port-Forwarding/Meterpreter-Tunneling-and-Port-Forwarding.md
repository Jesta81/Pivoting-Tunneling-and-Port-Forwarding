## Meterpreter Tunneling and Port Forwarding

Now let us consider a scenario where we have our Meterpreter shell access on the Ubuntu server (the pivot host), and we want to perform enumeration scans through the pivot host, but we would like to take advantage of the conveniences that Meterpreter sessions bring us. In such cases, we can still create a pivot with our Meterpreter session without relying on SSH port forwarding. We can create a Meterpreter shell for the Ubuntu server with the below command, which will return a shell on our attack host on port **8080.**

### Pivot Host

IP: 10.129.163.230
Username: ubuntu
Password: HTB_@cademy_stdnt!

### Creating Payload for Ubuntu Pivot Host

![Payload](/Meterpreter-Tunneling-and-Port-Forwarding/images/met-payload.png) 

Before copying the payload over, we can start a [multi/handler](https://www.rapid7.com/db/modules/exploit/multi/handler/), also known as a Generic Payload Handler.


### Configuring and Starting the multi / handler

![Listener](/Pivoting-Tunneling-Port-Forwarding/Meterpreter-Tunneling-and-Port-Forwarding/images/listener.png) 

We can copy the backupjob binary file to the Ubuntu pivot host over SSH and execute it to gain a Meterpreter session.

![SCP](/Pivoting-Tunneling-Port-Forwarding/Meterpreter-Tunneling-and-Port-Forwarding/images/scp.png) 


### Executing the Payload on the Pivot Host

![Execute](/Pivoting-Tunneling-Port-Forwarding/Meterpreter-Tunneling-and-Port-Forwarding/images/execute.png) 

We need to make sure the Meterpreter session is successfully established upon executing the payload.

### Meterpreter Session Establishment

![Session](/Pivoting-Tunneling-Port-Forwarding/Meterpreter-Tunneling-and-Port-Forwarding/images/session.png) 

We know that the Windows target is on the 172.16.5.0/23 network. So assuming that the firewall on the Windows target is allowing ICMP requests, we would want to perform a ping sweep on this network. We can do that using Meterpreter with the ping_sweep module, which will generate the ICMP traffic from the Ubuntu host to the network 172.16.5.0/23.


### Ping Sweep

	[*] Performing ping sweep for IP range 172.16.5.0/23
	[+]     172.16.5.19 host found
	[+]     172.16.5.129 host found
	[*] Post module execution completed

![Ping Sweep](/Pivoting-Tunneling-Port-Forwarding/Meterpreter-Tunneling-and-Port-Forwarding/images/ping-sweep.png) 


### Ping Sweep For Loop on Linux Pivot Hosts

![Bash ps](/Pivoting-Tunneling-Port-Forwarding/Meterpreter-Tunneling-and-Port-Forwarding/images/bash-ping-sweep.png) 


There could be scenarios when a host's firewall blocks ping (ICMP), and the ping won't get us successful replies. In these cases, we can perform a TCP scan on the 172.16.5.0/23 network with Nmap. Instead of using SSH for port forwarding, we can also use Metasploit's post-exploitation routing module socks_proxy to configure a local proxy on our attack host. We will configure the SOCKS proxy for SOCKS version 4a. This SOCKS configuration will start a listener on port 9050 and route all the traffic received via our Meterpreter session.


### Configuring MSF's SOCKS proxy

![SOCKS](/Pivoting-Tunneling-Port-Forwarding/Meterpreter-Tunneling-and-Port-Forwarding/images/socks.png) 


Finally, we need to tell our socks_proxy module to route all the traffic via our Meterpreter session. We can use the post/multi/manage/autoroute module from Metasploit to add routes for the 172.16.5.0 subnet and then route all our proxychains traffic.

### Creating Routes with AutoRoute

![Route](/Pivoting-Tunneling-Port-Forwarding/Meterpreter-Tunneling-and-Port-Forwarding/images/route.png) 

	meterpreter > run autoroute -s 172.16.5.0/23

	[!] Meterpreter scripts are deprecated. Try post/multi/manage/autoroute.
	[!] Example: run post/multi/manage/autoroute OPTION=value [...]
	[*] Adding a route to 172.16.5.0/255.255.254.0...
	[+] Added route to 172.16.5.0/255.255.254.0 via 10.129.163.230
	[*] Use the -p option to list all active routes


After adding the necessary route(s) we can use the -p option to list the active routes to make sure our configuration is applied as expected.

![Routes](/Pivoting-Tunneling-Port-Forwarding/Meterpreter-Tunneling-and-Port-Forwarding/images/routes.png) 


As you can see from the output above, the route has been added to the 172.16.5.0/23 network. We will now be able to use proxychains to route our Nmap traffic via our Meterpreter session.


### Testing Proxy & Routing Functionality

![Proxy](/Pivoting-Tunneling-Port-Forwarding/Meterpreter-Tunneling-and-Port-Forwarding/images/proxy.png) 


### Port Forwarding

Port forwarding can also be accomplished using Meterpreter's portfwd module. We can enable a listener on our attack host and request Meterpreter to forward all the packets received on this port via our Meterpreter session to a remote host on the 172.16.5.0/23 network.

	meterpreter > help portfwd
	Usage: portfwd [-h] [add | delete | list | flush] [args]


	OPTIONS:

	    -h   Help banner.
	    -i   Index of the port forward entry to interact with (see the "list" command).
	    -l   Forward: local port to listen on. Reverse: local port to connect to.
	    -L   Forward: local host to listen on (optional). Reverse: local host to connect to.
	    -p   Forward: remote port to connect to. Reverse: remote port to listen on.
	    -r   Forward: remote host to connect to.
	    -R   Indicates a reverse port forward.


#### Creating Local TCP Relay

	meterpreter > portfwd add -l 3300 -p 3389 -r 172.16.5.19
	[*] Forward TCP relay created: (local) :3300 -> (remote) 172.16.5.19:3389

![Local Forward](/Pivoting-Tunneling-Port-Forwarding/Meterpreter-Tunneling-and-Port-Forwarding/images/local-fwd.png) 

### Meterpreter Reverse Port Forwarding

Similar to local port forwards, Metasploit can also perform reverse port forwarding with the below command, where you might want to listen on a specific port on the compromised server and forward all incoming shells from the Ubuntu server to our attack host. We will start a listener on a new port on our attack host for Windows and request the Ubuntu server to forward all requests received to the Ubuntu server on port 1234 to our listener on port 8081.

We can create a reverse port forward on our existing shell from the previous scenario using the below command. This command forwards all connections on port 1234 running on the Ubuntu server to our attack host on local port (-l) 8081. We will also configure our listener to listen on port 8081 for a Windows shell.

![Rev Listener](/Pivoting-Tunneling-Port-Forwarding/Meterpreter-Tunneling-and-Port-Forwarding/images/rev-listener.png) 

We can now create a reverse shell payload that will send a connection back to our Ubuntu server on 172.16.5.129:1234 when executed on our Windows host. Once our Ubuntu server receives this connection, it will forward that to attack host's ip:8081 that we configured.

### Generating the Windows Payload


![Windows Payload](/Pivoting-Tunneling-Port-Forwarding/Meterpreter-Tunneling-and-Port-Forwarding/images/win-payload.png) 


Finally, if we execute our payload on the Windows host, we should be able to receive a shell from Windows pivoted via the Ubuntu server.

### Establishing the Meterpreter session


![Root](/Pivoting-Tunneling-Port-Forwarding/Meterpreter-Tunneling-and-Port-Forwarding/images/root.png) 
