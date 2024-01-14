# Sentinal-Lab
Tutorial on how to run a Honey Pot VM and aggregate failed RDP logs into the Azure Sentinal SIEM, and being able to visualize attacks on a map.

<h1>Failed RDP to IP Geolocation Information</h1>



<h2>Description</h2>
<b>The Powershell script in this repository is responsible for parsing out Windows Event Log information for failed RDP attacks and using a third party API to collect geographic information about the attackers location.
</b>
<br />
<br />
The script is used in this demo where I setup Azure Sentinel (SIEM) and connect it to a live virtual machine acting as a honey pot.
We will observe live attacks (RDP Brute Force) from all around the world. I will use a custom PowerShell script to
look up the attackers Geolocation information and plot it on an Azure Sentinel Map!
<br />
<br />


<h2>Setting up a Virtual Machine in Microsoft Azure</h2>
<br />
The first thing you are going to want to do is set up a Microsoft Azure account. This is pretty simple. Google Microsft Azure, then sign up with them using a microsoft email. Once you have created the account, type into the search bar "Virtual Machine" and click on the Virtual Machine Icon and create a new Azure Virtual Machine. Go with the default or cheapest settings that it has available. When you get to the "Network Security Group" this is where you want to make some changes. A Network Security Group acts as an Access Control List or ACL and this is what we need to allow attackers access to the VM. Create a new "Inbound Security Rule" that will allow any in. Make sure to type a "0 in the Destination Port Ranges and not leave it blank.

<h3>Network Security Group Settings </h2>

<br />

<p align="center">
<img src="https://imgur.com/ldn4cKT.png" height="85%" width="85%" alt="Network Security Group Settings"/>
</p>

<br />

<h2>Setting up Log Analytics Workspace</h2>
<br />
Once the VM is set up, search "Log Analytics Workspace" and create a new one. Add it to your current resource group and create it. Next, search "Azure Sentinal" and enable it to allow the SIEM to collect logs and analyze them. It will ask you if you want what you want to log, and you only want "Servers" to be on, turn off "SQL Servers". Once that's done, navigate back the "Log Analytics Workspace" and select the one you created. Go to Virtual Machines within that LAW and select your VM and Connect it to your LAW. 

<h2>Accessing the VM</h2>
<br />
Open up RDP and type in the public IP Address that has been assigned to your VM and authenticate into it. Navigate to "Windows Defender Firewall" and go to advanced settings. Turn off the Firewall for your Domain Profile, Private Profile, and Public Profile. 
<br />
<br />


<p align="center">
<img src="https://imgur.com/X3qzsXS.png" height="85%" width="85%" alt="Firewall Settings"/>
</p>
<br />

*This will allow attackers to remote into your VM and attempt to authenticate. Also, go ahead and disconnect from the VM and attempt to authenticate with bogus credentials so that the VM can build up some failed login attempts for Event Viewer to log.
<br />



<h2>Languages Used</h2>

<p align="center">
<img src="https://imgur.com/iZjOmRr.png" height="85%" width="85%" alt="RDP event fail logs to iP Geographic information"/>
</p>
<h2>Languages Used</h2>

- <b>PowerShell:</b> Extract RDP failed logon logs from Windows Event Viewer 

<h2>Utilities Used</h2>

- <b>ipgeolocation.io:</b> IP Address to Geolocation API. Create an account in [IP Geolocation](https://ipgeolocation.io/). This will allow you to have an API key that you will need to swap out for mine in the [Powershell Script]() in the repository.

<h2>Custom logs being output with geodata</h2>

<p align="center">
<img src="https://imgur.com/jrf3M2u.png" height="85%" width="85%" alt="Image Analysis Dataflow"/>
</p>

<br />
<br />



<h2>World map of incoming attacks after 24 hours (built custom logs including geodata)</h2>

<p align="center">
<img src="https://imgur.com/xn9KXwd.png" height="85%" width="85%" alt="Image Analysis Dataflow"/>
</p>
