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

<br />
<br />
<br />

<h2>Custom logs being output with geodata</h2>

<p align="center">
<img src="https://imgur.com/jrf3M2u.png" height="85%" width="85%" alt="Image Analysis Dataflow"/>
</p>

<br />
<br />


<h2>Languages Used</h2>

- <b>PowerShell:</b> Extract RDP failed logon logs from Windows Event Viewer 

<h2>Utilities Used</h2>

- <b>ipgeolocation.io:</b> IP Address to Geolocation API. Create an account in [IP Geolocation](https://ipgeolocation.io/). This will allow you to have an API key that you will need to swap out for mine in the [Powershell Script](Security_Log_Exporter.ps1) in my repository. Open up Power Shell in the VM (make sure you are in the VM) and run that script. What this will do is extract all of the failed login attempts and store them in explorer. If you have trouble locating it. Win + R and type in "C:\programdata\failed_rdp.log" and that will bring you to the file.

<h2>How do we get these logs to move over to Azure LAWs?</h2>
<br />

Copy the data that is located in the "failed_rdp_log" and copy that data to a new notepad or equivalent and save it (make sure you set the view to "status bar"). So next we are going to go back to Azure and navigate to the LAW that was created. Once you are there, go to "Tables" located on the left hand side and create a new "MMA-based" table. Follow the screenshots for how you are going to want to input the data. In the "Record Delimiter" part you should see the data that you copied over to that file you just created. Keep in mind that if you used a different file name then use that instead of what mine is.

<br /"
<br /"

<p align="center">
<img src="https://imgur.com/HajCAHQ.png" height="85%" width="85%" alt="Custom Log Examle File"/>
</p>

<br />
<br />

<p align="center">
<img src="https://imgur.com/lHCfzUa.png" height="85%" width="85%" alt="Custom Log file path"/>
</p>

<br />
<br />

Name it something like "FAILED_RDP_LOG_CR" and then go ahead and create it when you get to the end.

<br />
<br />

<h2>Actually seeing some results start to happen</h2>

<br />
<br />

Now go ahead and go to "Logs" within your LAW once you created the custom log. I'll attach a screenshot of what the query will be in KQL but here is the [script](https://github.com/Lars-Berggren/Sentinal-Lab/blob/main/KQL%20Script) or just go find it in the repository named "KQL Script". Once you run that you will see something like this:

<br />
<br />

<p align="center">
<img src="https://imgur.com/etuG0Ti.png" height="85%" width="85%" alt="KQL Script"/>
</p>

<br />
<br />

See now we're getting somewhere. Notice the highlighted portion has pulled out the longitude, latitude, country, etc. What this did is teach Sentinal what to look for when we go to create a "Worksheet" in Sentinal that will give us our data points on a map. Machine learning is kinda cool, right? 

<br />
<br />

<H2>Final Steps in Sentinal</H2>

<br />
<br />

Okay now that we've done all that, go ahead and go back to Azure Sentinal and select your LAW. Click on "Workbooks" and add a new workbook. Click "edit" and then "edit" again on the new work book. Use this [script]()

<br />
<br />



<br />
<br />

<h2>World map of incoming attacks after 24 hours (built custom logs including geodata)</h2>

<p align="center">
<img src="https://imgur.com/xn9KXwd.png" height="85%" width="85%" alt="Image Analysis Dataflow"/>
</p>
