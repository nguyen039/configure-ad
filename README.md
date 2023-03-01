<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>On-premises Active Directory Deployed in the Cloud (Azure)</h1>
This tutorial outlines the implementation of on-premises Active Directory within Azure Virtual Machines.<br />


<h2>Video Demonstration</h2>

- ### [YouTube: How to Deploy on-premises Active Directory within Azure Compute](https://www.youtube.com)

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (21H2)

<h2>List of Prerequisites</h2>

- Created a resource group on Azure
- Created a Windows virtual machine
- Logged into virtual machine with remote desktop

<h2>Deployment and Configuration Steps</h2>
<p>
Create Resource Group & Virtual Machines

- Make a resource group on Azure portal
- After that, create a "domain controller" virtual machine (called DC-1)
- Search "virtual machines" on Azure -> click on your domain controller -> "Networking" (beneath "Settings") -> click what's on the right of "Network Interface"
- Toggle to the "IP forwarding" to "Disabled", so the NIC ip address wouldn't change.
- Create a second virtual machine that represents the client (called Client-1)

</p>
<p>
<img src="https://i.imgur.com/EJg94wV.png" height="80%" width="80%" alt="domain controller"/>
</p>

<hr>

<p>
Connectivity between Client & Domain Controller

- Log into your DC-1 virtual machine through "Remote Desktop" as well log into your Client-1
- On the DC-1 remote desktop, open "Windows Defender Firewall" from the search bar -> "Advanced Settings" -> "Inbound Rules" -> "Protocol"
- Enable ICMPv4 rules
- Switch to your Client-1 VM and open up the command line
- Type "ping [DC-1's private ip address]" on the command line to check connectivity

</p>
<p>
<img src="https://i.imgur.com/McEbebu.png" height="80%" width="80%" alt="icmpv4 rules"/>
</p>

<hr>

<p>
Install Active Directory

- Switch back to your DC-1 VM
- Open your "Service Manager" 
- Click on "Add roles and features" located next to "2" and keep clicking on "Next"
- Click on "Active Directory Domain Services" -> "Add Features" -> keep clicking on "Next" and then "Install"

</p>
<p>
<img src="https://i.imgur.com/h0N1671.png" height="80%" width="80%" alt="active directory"/>
</p>

<hr>

<p>
Setup Active Directory

- Click on the flag that's on the left of "Manage"
- Select: "Add a new forest", type in a "Root domain name", and set a password
- Click a bunch of "Next", then "Install"
- Restart the remote desktop and log in 

</p>
<p>
<img src="https://i.imgur.com/0Yt8ROI.png" height="80%" width="80%" alt="setup active directory"/>
</p>

<hr>

<p>
Create Active Directory User Accounts

- Open "Active Directory Users and Computers" from your Windows search bar
- Right click on the domain that's located on the left panel -> Click "New" -> "Organizational Unit" -> name it "_EMPLOYEES"
- Create another organizational unit called "_ADMINS”
- Create a new employee, input necessary details, and mark "Password never expires" (practice purposes)

</p>
<p>
<img src="https://i.imgur.com/2eTxq9E.png" height="80%" width="80%" alt="ad user accounts"/>
</p>

<hr>

<p>
Add User to _ADMINS

- Click on "_ADMINS" then on your create user -> right click on the user's name
- Select "Member Of" -> Add Domain Users -> enter "Domain" and click "Check Names" -> click "OK"
- Select "Domain Admins" -> keep clicking "OK" and "Apply"
- Log out of the virtual machine and log back in

</p>
<p>
<img src="https://i.imgur.com/R7IUQhj.png" height="80%" width="80%" alt="admin"/>
</p>

<hr>

<p>
Join Client-1 to Domain on Azure

- Find the domain controller's private ip address on the Azure portal.
- Open Client-1's virtual machine on Azure
- Click "Networking" -> "Network Interface" -> "DNS Servers"
- Select "Custom" DNS Servers
- Enter the DC's Private IP address as the DNS Server
- Restart Client-1's vm on the Azure portal

</p>

<p>
Complete Joining of Client-1 to Domain on Remote Desktop

- Log into Client-1's vm through remote desktop
- Right click the start menu and select "System"
- From there, click "Rename this PC (advanced)" -> "Change"
- On "Domain", type your domain you made earlier and click "OK"
- On "Computer Name/Domain Changes", type "[domain name].com\[admin user]" and the password
- Click "OK", restart and login the remote desktop
- Search "Active Directory Users and Computers" on the search bar
- Check if Client-1 is in the “Computers” container within the root of the domain

</p>

<hr>

<p>
Setup Remote Desktop for Users on Client-1

- Log out and log into Client-1 as [domain name].com\[admin user] and open system properties.
- Click “Remote Desktop" and allow "domain users" access to remote desktop

</p>
<p>
<img src="https://i.imgur.com/b6bbL2r.png" height="80%" width="80%" alt="setup active directory"/>
</p>

<hr>

<p>
Make More Users

- Switch to the DC-1 VM
- Type "PowerShell ISE" on the search bar and right click to click "Run as Administrator"
- Create a new file within Powershell
- Paste the code from [here](https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1) into the new file, which create random users with the password of "Password1" 
- Open the "Active Directory" and then click on _EMPLOYEES to see the list of new users being added

</p>
<p>
<img src="https://i.imgur.com/vxdT237.png" height="80%" width="80%" alt="setup active directory"/>
</p>

<hr>

<p>
Login with a New User's Account

- Choose a random new user's username
- Log off of Client-1
- Log into Client-1 with the new user's account and the password is Password1

</p>

<hr>

<p>
Fix Passwords

- Switch into DC-1
- Go to the Active Directory and click on _EMPLOYEES
- Choose a name and right click to click on "Properties"
- Click "Account" -> check the box to the left of "Unlock Account"
- Or right click on a name to click "Reset Password"

</p>

<hr>

<p>
Close the virtual machines from remote desktop and delete the resource groups/virtual machines on the Azure Portal
</p>
