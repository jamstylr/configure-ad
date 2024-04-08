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

![image](https://github.com/jamstylr/configure-ad/assets/159660523/34a2b5b3-080a-410e-a035-09014684bb10)
![image](https://github.com/jamstylr/configure-ad/assets/159660523/2d089568-d299-42b2-9404-32727254decf)
![image](https://github.com/jamstylr/configure-ad/assets/159660523/e40bc1d5-38be-4f0e-9e9e-01cdcf3194dc)
<p>
Now that we have installed Active Directory on DC-1, we will create new Organizational Units and Users. Organizational Units function as containers resembling folders, storing user information, privileges, and login access within the directory structure. From the Server Manager dashboard, go to the Tools tab and open Active Directory Users and Computers. Right-click on “mydomain.com" -> New -> Organizational Unit. Create two new OUs; name one “_EMPLOYEES” and name the second one “_ADMINS”. Right-click on mydomain.com and click Refresh.
</p>
<br />

![image](https://github.com/jamstylr/configure-ad/assets/159660523/2887414c-bd89-4879-82bc-ccb729cea583)
![image](https://github.com/jamstylr/configure-ad/assets/159660523/24c0cc0b-1517-4727-9a7d-4e40297ac309)
![image](https://github.com/jamstylr/configure-ad/assets/159660523/151dcf28-4835-4776-890c-168414ee5da2)
![image](https://github.com/jamstylr/configure-ad/assets/159660523/29d4f809-e229-46e7-baff-4cb5d4987731)
![image](https://github.com/jamstylr/configure-ad/assets/159660523/8031d74f-3a64-4604-b83c-f8090830e818)
![image](https://github.com/jamstylr/configure-ad/assets/159660523/4fe4e4b4-0073-403a-aff2-c3a4bfe77e74)
<p>
Right-click on _ADMINS -> New -> User. You can give this user any name that you would like, but for the purposes of this tutorial, I will create a user named Jane Doe with the logon name of jane_admin. When creating a password for this user, uncheck the box “User must change password at next logon” and check the box “Password never expires”. Next, we want to give administrative privileges to this newly created user (Jane Doe). To grant admin privileges, right-click the user and go to Properties -> Member Of -> Add. Type in Domain Admins and click Check Names -> OK -> Apply -> OK. Log off DC-1 and log back in as the new user to whom we gave admin privileges (mydomain.com\jane_admin).
</p>
<br />

![image](https://github.com/jamstylr/configure-ad/assets/159660523/1dda0deb-4886-49d4-82c5-45f62e1c794f)
![image](https://github.com/jamstylr/configure-ad/assets/159660523/a6e121b9-1232-4d98-ad2b-024281330fae)
![image](https://github.com/jamstylr/configure-ad/assets/159660523/3994054d-e7d8-45eb-8b3a-37e69e239c75)
![image](https://github.com/jamstylr/configure-ad/assets/159660523/4eee0056-5fc9-42e8-a2da-f2137d855d7c)
![image](https://github.com/jamstylr/configure-ad/assets/159660523/b513a606-a91c-4a51-9491-cc7b656e6c18)
<p>
To ensure successful domain integration for the client, the DNS server should be set to the private IP address of the domain controller. First, go to the Azure Portal and obtain the private IP address of DC-1. Next, navigate to Client-1 in the Azure Portal: Network Settings -> Network Interface -> DNS Servers -> Custom: (DC-1’s private IP address) -> Save. Return to Client-1 in the Azure Portal and click restart. Reconnect to the Client-1 VM, right-click Start -> System -> Rename this PC (Advanced) -> Change. Click on Domain, type mydomain.com, and click OK. To enable the client to join the domain, enter the domain and credentials (jane_admin), for example, mydomain.com\jane_admin. The Client-1 VM will now restart.
</p>
<br />

![image](https://github.com/jamstylr/configure-ad/assets/159660523/1f4f8cfd-d441-45a7-9b9b-00333fee0d3e)
<p>
To help keep track of which VM you are using and which user you are logged in with, you can open Command Prompt and use the the "whoami" command for user information and the "hostname" command to identify the active VM.
</p>
<br />

![image](https://github.com/jamstylr/configure-ad/assets/159660523/0deeab5a-c3e8-4539-b3a5-b364fa35df5e)
![image](https://github.com/jamstylr/configure-ad/assets/159660523/2981277d-4d8b-47de-837e-a74f87f64520)
![image](https://github.com/jamstylr/configure-ad/assets/159660523/9937612b-e847-4108-aae9-ff1de309e6c1)
![image](https://github.com/jamstylr/configure-ad/assets/159660523/58ecf7b3-cbd2-4f34-b906-c70cea1c1bea)
<p>
To grant users in the domain access to the client computer, Remote Desktop must first be enabled for non-administrative users. First, reconnect to Client-1 as mydomain.com\jane_admin. Right-click Start -> System -> Remote Desktop -> User Accounts -> select "Select users who can remotely access this PC". Click on Add -> type Domain Users -> Check Names -> OK -> OK. Now, all domain users (non-administrative users) can log in to Client-1.
</p>
<br />
