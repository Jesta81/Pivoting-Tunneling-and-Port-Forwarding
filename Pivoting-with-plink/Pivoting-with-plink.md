## SSH for Windows: plink.exe

### Ubuntu Host (172.16.5.129)

> - Username: ubuntu
> - Password: HTB_@cademy_stdnt!



[Plink](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html), short for PuTTY Link, is a Windows command-line SSH tool that comes as a part of the PuTTY package when installed. Similar to SSH, Plink can also be used to create dynamic port forwards and SOCKS proxies. Before the Fall of [2018](https://docs.microsoft.com/en-us/windows-server/administration/openssh/openssh_overview), Windows did not have a native ssh client included, so users would have to install their own. The tool of choice for many a sysadmin who needed to connect to other hosts was [PuTTY](https://www.putty.org/).

> - Imagine that we are on a pentest and gain access to a Windows machine. We quickly enumerate the host and its security posture and determine that it is moderately locked down. We need to use this host as a pivot point, but it is unlikely that we will be able to pull our own tools onto the host without being exposed. Instead, we can live off the land and use what is already there. If the host is older and PuTTY is present (or we can find a copy on a file share), Plink can be our path to victory. We can use it to create our pivot and potentially avoid detection a little longer.

That is just one potential scenario where Plink could be beneficial. We could also use Plink if we use a Windows system as our primary attack host instead of a Linux-based system.


### Getting to Know Plink

In the below image, we have a Windows-based attack host.


![Plink Diagram](/Pivoting-with-plink/images/plink-diagram.png) 


The Windows attack host starts a plink.exe process with the below command-line arguments to start a dynamic port forward over the Ubuntu server. This starts an SSH session between the Windows attack host and the Ubuntu server, and then plink starts listening on port 9050.


### Using Plink.exe

![]![Plink](/Pivoting-with-plink/images/plink.png) 

	plink.exe
	Plink: command-line connection utility
	Release 0.78
	Usage: plink [options] [user@]host [command]
		  ("host" can also be a PuTTY saved session name)
	Options:
	  -V        print version information and exit
	  -pgpfp    print PGP key fingerprints and exit
	  -v        show verbose messages
	  -load sessname  Load settings from saved session
	  -ssh -telnet -rlogin -raw -serial
		       force use of a particular protocol
	  -ssh-connection
		       force use of the bare ssh-connection protocol
	  -P port   connect to specified port
	  -l user   connect with specified username
	  -batch    disable all interactive prompts
	  -proxycmd command
		       use 'command' as local proxy
	  -sercfg configuration-string (e.g. 19200,8,n,1,X)
		       Specify the serial configuration (serial only)
	The following options only apply to SSH connections:
	  -pwfile file   login with password read from specified file
	  -D [listen-IP:]listen-port
		       Dynamic SOCKS-based port forwarding
	  -L [listen-IP:]listen-port:host:port
		       Forward local port to remote address
	  -R [listen-IP:]listen-port:host:port
		       Forward remote port to local address
	  -X -x     enable / disable X11 forwarding
	  -A -a     enable / disable agent forwarding
	  -t -T     enable / disable pty allocation
	  -1 -2     force use of particular SSH protocol version
	  -4 -6     force use of IPv4 or IPv6
	  -C        enable compression
	  -i key    private key file for user authentication
	  -noagent  disable use of Pageant
	  -agent    enable use of Pageant
	  -no-trivial-auth
		       disconnect if SSH authentication succeeds trivially
	  -noshare  disable use of connection sharing
	  -share    enable use of connection sharing
	  -hostkey keyid
		       manually specify a host key (may be repeated)
	  -sanitise-stderr, -sanitise-stdout, -no-sanitise-stderr, -no-sanitise-stdout
		       do/don't strip control chars from standard output/error
	  -no-antispoof   omit anti-spoofing prompt after authentication
	  -m file   read remote command(s) from file
	  -s        remote command is an SSH subsystem (SSH-2 only)
	  -N        don't start a shell/command (SSH-2 only)
	  -nc host:port
		       open tunnel in place of session (SSH-2 only)
	  -sshlog file
	  -sshrawlog file
		       log protocol details to a file
	  -logoverwrite
	  -logappend
		       control what happens when a log file already exists
	  -shareexists
		       test whether a connection-sharing upstream exists



### Using Proxifier


Another Windows-based tool called [Proxifier](https://www.proxifier.com/) can be used to start a SOCKS tunnel via the SSH session we created. Proxifier is a Windows tool that creates a tunneled network for desktop client applications and allows it to operate through a SOCKS or HTTPS proxy and allows for proxy chaining. It is possible to create a profile where we can provide the configuration for our SOCKS server started by Plink on port 9050.


![Proxifier](/Pivoting-with-plink/images/proxifier.png) 


After configuring the SOCKS server for **127.0.0.1** and port **9050**, we can directly start mstsc.exe to start an RDP session with a Windows target that allows RDP connections.

> - Note: We can attempt this technique in any interactive section of this module from a personal Windows-based attack host. Once you've completed this module from a Linux-based attack host feel free to try to go back through it from a personal Windows-based attack host. Also, when spawning your target we ask you to wait for 3 - 5 minutes until the whole lab with all the configurations is set up so that the connection to your target works flawlessly. 
