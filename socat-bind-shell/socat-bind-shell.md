## Socat Redirection with a Bind Shell

Similar to our socat's reverse shell redirector, we can also create a socat bind shell redirector. This is different from reverse shells that connect back from the Windows server to the Ubuntu server and get redirected to our attack host. In the case of bind shells, the Windows server will start a listener and bind to a particular port. We can create a bind shell payload for Windows and execute it on the Windows host. At the same time, we can create a socat redirector on the Ubuntu server, which will listen for incoming connections from a Metasploit bind handler and forward that to a bind shell payload on a Windows target. The below figure should explain the pivot in a much better way.


![Diagram](/socat-bind-shell/images/bind-shell-diagram.png) 


We can create a bind shell using msfvenom with the below command. 


### Creating the Windows Payload


![Payload](/socat-bind-shell/images/creating-payload.png) 


We can start a socat bind shell listener, which listens on port **8080** and forwards packets to Windows server **8443**.

### Starting Socat Bind Shell Listener

![Socat](/socat-bind-shell/images/socat.png) 

Finally, we can start a Metasploit bind handler. This bind handler can be configured to connect to our socat's listener on port 8080 (Ubuntu server)


### Configuring & Starting the Bind multi / hander

![Bind Listener](/socat-bind-shell/images/bind-listener.png) 


We can see a bind handler connected to a stage request pivoted via a socat listener upon executing the payload on a Windows target.

### Establishing Meterpreter Session

![Root](/socat-bind-shell/images/root.png) 
