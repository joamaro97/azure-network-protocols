<p align="center">
<img src="https://i.imgur.com/Ua7udoS.png" alt="Traffic Examination"/>
</p>

<h1>Inspecting Traffic Between Azure Virtual Machines</h1>
In this lab, I will be observing different kinds of network traffic to and from Azure virtual machines using WireShark which will be install in one of the VMs. Also, I will be experimenting with Network security groups. <br />

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop Connection
- Command-Line Tools
- Various Network Protocols (SSH, RDH, DNS, HTTP/S, ICMP)
- Wireshark (Protocol Analyzer)

<h2>Operating Systems Used </h2>

- Windows 10 Pro (21H2)
- Ubuntu Server 20.04

<h2>The Set-Up</h2> 
Within Azure, I created two virtual machines (VMs) within the same virtual network (Vnet) to ensure that they are able to communicate with each other. One VM will have Windows 10 Pro while the other uses Ubuntu each with 1vcpu and 8GiBM memory. The Windows VM will connect to the other via the command line/PowerShell. 

<h3>Note:</h3>  

I will not be showing how to create the VMs or Resource Groups because that would only "inflate" the lab.
 
<h2>Actions and Observations</h2>

<p>
<img src="https://i.imgur.com/1EY95x6.png" height="80%" width="80%" alt="Azure Networking Steps"/>
</p>

First, I opened Remote Desktop Connection (RPC) and I use the credentials from the virtual machine I created before (user, password and VM's public IP address) 

<p>
<img src="https://i.imgur.com/lzyrkI2.png" height="80%" width="80%" alt="Azure Networking Steps"/>
</p>

<p>
Once I connected to the virtual machine, I went to the browser to download Wireshark which is going help me to see what is going on in the network. 
</p>
<br />

<p>
<img src="https://i.imgur.com/8IcSRqK.png" height="80%" width="80%" alt="Azure Networking Steps"/>
</p>
<p>
Within Wireshark, I filtered for ICMP (Internet Control Message Protocol) traffic and opened PowerShell to execute a command called ping. Ping utilizes ICMP, which is used by devices in a network to communicate problems within data transmition. I used ping to see if I can communicate with the Ubuntu VM using its private IP address. Afterwards, I used a perpetual ping to the Ubuntu VM in order to see how network security groups work. I executed the perpetual ping with the command: ping -t (ip address).
</p>
<br />

<p>
<img src="https://i.imgur.com/y7dCDR5.png" height="80%" width="80%" alt="Azure Networking Steps"/>
</p>
<p>
Now, I am going to block the ICMP traffic and watch wireshark to see what happened in that connection. In Azure,  I opened the networking settings for the Ubuntu VM and added an inbound security rule to block ICMP traffic. I make sure to have the priority higher than SSH (310) to ensure the rule applies first. 
</p>
<br />

<p>
<img src="https://i.imgur.com/p8GKYp8.png" height="80%" width="80%" alt="Azure Networking Steps"/>
</p>
<p>
Coming back to the windows VM, I noticed the connection have been interrupted it or as shown in powershell "request timed out". After changing the rule to allow traffic again, the perpetual ping resolves without timing out. 
</p>
<br />

<p>
<img src="https://i.imgur.com/hC6K1uG.png" height="80%" width="80%" alt="Azure Networking Steps"/>
</p>
<p>
Next, I chose to examine SSH traffic. I logged in to the Ubuntu server via PowerShell with the ssh command. With Wireshark, I filtered the traffic with SSH (I could also use tcp.port == 22 to filter it. While logged into the Ubuntu server, my session is logged in Wireshark with each command I use.
</p>
<br />

<p>
<img src="https://i.imgur.com/2f4O9ZX.png" height="80%" width="80%" alt="Azure Networking Steps"/>
</p>
<p>
After examining SSH traffic, I exited the Ubuntu server in order to filter for DHCP traffic. To see it in action, I decided to attempt to issue a new IP address from my VM. The command ipconfig /renew will attempt to issue the new IP address and will temporarily disconnect me for a few seconds. After reconnecting, the resulting traffic is shown in Wireshark.
</p>
<br />

<p>
<img src="https://i.imgur.com/5rO7PIn.png" height="80%" width="80%" alt="Azure Networking Steps"/>
</p>
<p>
Then I use the filter DNS to observe its traffic (again I could also use a different filter like udp.port == 53) and the command "nslookup". I use the command to see which result I would get from looking up google.com. 
</p>
<br />

<p>
<img src="https://i.imgur.com/JSuSAO8.png" height="80%" width="80%" alt="Azure Networking Steps"/>
</p>
<p>
To finish my lab, I decided to observe RDP traffic. The filter for Wireshark is tcp.port == 3389. There is non-stop traffic because RDP is constantly showing me a live stream from one computer to another (in my case, my computer accessing the VM that is hosted on Azure) and thus traffic is always transmitted. 
</p>
<br />

<h2>Lessons Learned </h2>
While this lab did not allow me to do any troubleshooting, it still serves the purpose to gather information throught the network. With this lab, I can get familiar in how different protocols and ports are utilized in a network between devices. I know that getting familiar in how these systems and commands work I figure it out solutions for any issue a client might have.
