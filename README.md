<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>On-premises Active Directory Deployed in the Cloud (Azure)</h1>
This tutorial outlines the implementation of on-premises Active Directory within Azure Virtual Machines.<br />


<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (21H2)

<h2>High-Level Deployment and Configuration Steps</h2>

- Create a Domain Controller VM & Client VM
- Install Active Directory
- Establish Admin and User Accounts within Active Directory
- Integrate the Client Virtual Machine with the newly formed Domain
- Enable Remote Desktop access for non-admin users on Client VM
- Conduct testing on user accounts and their configurations

<h2>Deployment and Configuration Steps</h2>

![image](https://github.com/jamstylr/configure-ad/assets/159660523/7f5ac833-16a8-404f-a347-f315ef5ff19b)
![image](https://github.com/jamstylr/configure-ad/assets/159660523/205f6eff-4e08-4c79-ba86-90a1c93ec29c)
![image](https://github.com/jamstylr/configure-ad/assets/159660523/5a4ab74f-add6-4536-9395-c82411fd4fdf)
<p>
First we will go to the Azure Portal and create two Virtual Machines running on the same Virtual Network. The first VM we create will be a Domain Controller and the second VM will be a Client machine. Create the first VM as a Domain Controller and name it DC-1, running Windows Server 2022. Name the other VM Client-1, running on Windows 10. Make sure that both VMs are on the same Network.
</p>
<br />

![image](https://github.com/jamstylr/configure-ad/assets/159660523/611bd5fd-3171-4a7f-9cd5-9270dbde690f)
![image](https://github.com/jamstylr/configure-ad/assets/159660523/f8be014b-78b9-46e7-b27c-cf9cec4ae470)
![image](https://github.com/jamstylr/configure-ad/assets/159660523/6b16f609-f8d9-493d-a444-0b332bc9e8a4)
![image](https://github.com/jamstylr/configure-ad/assets/159660523/adb9b539-c4f1-4428-a3da-71d77a79aae1)
<p>
Now we need to change DC-1’s private IP address from Dynamic to Static. To do this, navigate to DC-1 in the Azure Portal, then go to Network Settings -> Network Interface -> IP Configurations, and click on “Private IP Address - Dynamic”. Change the Assignment to “Static”, then save your changes.
</p>
<br />

![image](https://github.com/jamstylr/configure-ad/assets/159660523/e8d50885-4e61-4ede-bea1-a23786d3b79e)
![image](https://github.com/jamstylr/configure-ad/assets/159660523/0f88868d-07dd-4c61-80a9-0fec64d65e6b)
![image](https://github.com/jamstylr/configure-ad/assets/159660523/e3152a7e-05c0-4f11-a624-a7c4814e07eb)
<p>
Using Remote Desktop, connect to Client-1’s Public IP Address. Once connected, open Command Prompt and enter the following command: ping -t [DC-1 Private IP address]. The connection will time out following the initial ping because of the Firewall Settings on the Domain Controller.
</p>
<br />

![image](https://github.com/jamstylr/configure-ad/assets/159660523/1b241b2e-9c43-433f-80ec-ecd71ee4c38c)
![image](https://github.com/jamstylr/configure-ad/assets/159660523/719bae7d-6b39-4b3c-a56a-fe8ddba322a3)
<p>
Connect to DC-1, then in the search bar, type “Firewall” and open “Windows Defender Firewall with Advanced Security”. Next, click on Inbound Rules and enable both rules named “Core Networking Diagnostics - ICMP Echo Request” by right-clicking on each and selecting enable. Return to Client-1, where you should now see that the ping is successful and receiving a reply. Press Control+C to stop the perpetual ping.
</p>
<br />

![image](https://github.com/jamstylr/configure-ad/assets/159660523/c20035b7-9e60-4631-8315-88ac801e933d)
![image](https://github.com/jamstylr/configure-ad/assets/159660523/e0d8f466-39ca-431d-be13-65a15a3676af)
![image](https://github.com/jamstylr/configure-ad/assets/159660523/e65b5259-585c-423f-96ab-ef626489ff1a)
![image](https://github.com/jamstylr/configure-ad/assets/159660523/6af6dd36-3829-4945-bd9a-c7061ccb8335)
![image](https://github.com/jamstylr/configure-ad/assets/159660523/469c0d7b-7d7d-4b7c-9884-f672c3fca1dc)
<p>
We will now install Active Directory on DC-1. To begin, open Server Manager on DC-1 and click on "Add Roles and Features". During the installation process, choose "Role-Based" as the installation type. On the Select Destination Server screen, select DC-1 with its private IP address. On the Server Roles tab, choose Active Directory Domain Services and click Add Features, then click Next and Install. Once the installation is complete, we need to promote the server to a domain controller. On the Server Manager dashboard, you will see a yellow flag in the top right corner; click this and select "Promote this server to a domain controller". Choose "Add a new forest" and specify a domain name. For this tutorial, we will use the name "mydomain.com". Specify a password for the domain and proceed with the installation. You will be automatically logged out. When logging back into DC-1 through Remote Desktop, remember to log in with the context of the domain. For example: mydomain.com\labuser.
</p>
<br />


<p>
Now that we have installed Active Directory on DC-1, we will create new Organizational Units and Users. Organizational Units function as containers resembling folders, storing user information, privileges, and login access within the directory structure. From the Server Manager dashboard, go to the Tools tab and open Active Directory Users and Computers. Right-click on “mydomain.com" -> New -> Organizational Unit. Create two new OUs; name one “_EMPLOYEES” and name the second one “_ADMINS”. Right-click on mydomain.com and click Refresh.
</p>
<br />
