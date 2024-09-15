## Pivoting with SSHUTTEL

> - Target
> - 10.129.202.64
> - ubuntu
> - HTB_@cademy_stdnt!

[Sshuttle](https://github.com/sshuttle/sshuttle) is another tool written in Python which removes the need to configure proxychains. However, this tool only works for pivoting over SSH and does not provide other options for pivoting over TOR or HTTPS proxy servers. Sshuttle can be extremely useful for automating the execution of iptables and adding pivot rules for the remote host. We can configure the Ubuntu server as a pivot point and route all of Nmap's network traffic with **sshuttle** using the example later in this section. 

One interesting usage of **sshuttle is that we don't need to use proxychains to connect to the remote hosts**. Let's install sshuttle via our Ubuntu pivot host and configure it to connect to the Windows host via RDP.

### Installing sshuttle

	$ sudo apt install sshuttle


To use sshuttle, we specify the option **-r** to connect to the remote machine with a username and password. Then we need to include the network or IP we want to route through the pivot host, in our case, is the network **172.16.5.0/23**. 


### Running sshuttle


![sshuttle](/Pivoting-with-sshuttle/images/sshuttle.png) 

With this command, sshuttle creates an entry in our **iptables** to redirect all traffic to the 172.16.5.0/23 network through the pivot host.

	 s: Running server on remote host with /usr/bin/python3 (version 3.8.10)
	 s: latency control setting = True
	 s: auto-nets:False
	c : Connected to server.
	fw: setting up.
	fw: ip6tables -w -t nat -N sshuttle-12300
	fw: ip6tables -w -t nat -F sshuttle-12300
	fw: ip6tables -w -t nat -I OUTPUT 1 -j sshuttle-12300
	fw: ip6tables -w -t nat -I PREROUTING 1 -j sshuttle-12300
	fw: ip6tables -w -t nat -A sshuttle-12300 -j RETURN --dest ::1/128 -p tcp
	fw: ip6tables -w -t nat -A sshuttle-12300 -j RETURN -m addrtype --dst-type LOCAL
	fw: iptables -w -t nat -N sshuttle-12300
	fw: iptables -w -t nat -F sshuttle-12300
	fw: iptables -w -t nat -I OUTPUT 1 -j sshuttle-12300
	fw: iptables -w -t nat -I PREROUTING 1 -j sshuttle-12300
	fw: iptables -w -t nat -A sshuttle-12300 -j RETURN --dest 127.0.0.1/32 -p tcp
	fw: iptables -w -t nat -A sshuttle-12300 -j REDIRECT --dest 172.16.5.0/23 -p tcp --to-ports 12300
	fw: iptables -w -t nat -A sshuttle-12300 -j RETURN -m addrtype --dst-type LOCAL


### Traffic Routing through iptables Routes

We can now use any tool directly without using proxychains. I can RDP internal hosts without setting up a proxy with **sshuttle**. 


![RDP](/Pivoting-with-sshuttle/images/rdp.png) 
