# Windows-Server-Active-Directory-Installation
Today I'll be demonstrating the setup for Windows Server 2022 and Active Directory



<h2>Windows Server 2022 Installation In Proxmox VM Environment</h2>

 1. Download an evaluation copy of Windows Server 2022 from Microsoft's website.
 2. We have to upload our ISO into our Proxmox VM Environment to do this we have to locate our local storage
 3. After you located the local storage on your Proxmox VM Environment select the section "ISO Images" In this section this is where we are going to upload our Windows Server 2022 ISO 
 5. Once it is uploaded we can start to create a new virtual machine, we do this by clicking on "Create VM" located in the top right corner of the screen
 6. Under the General tab name your VM, it can be anything you want.
 7. Under the OS, On the ISO image tab we are going to select the ISO that we just uploaded into our Proxmox, we want to change the type of OS we have from Linux to Windows and make sure we have the latest version selected.
 8. Under System the only thing you want to change under "SCSI Controller" from "VirtIO SCSI single" to "VirtIO SCSI"
 9. Under Disks, all we want to change is "Bus/Device" from "IDE" to "SCSI"
    - In the other sections it depends on your computer so adjust as you need, If necessary, there are plenty of YouTube videos out there that go through the installation process in-depth

image 

 7. Once setup is complete, open the VM. You can do this by going to the console section in your new VM and clicking on "start" or on the top right of the screen you can see a button that says "start" right below the "documentation".
 8. Click next on the first prompt and press "Install now".

<img src="https://i.imgur.com/CfP8Y7V.png" height="60%" width="60%" alt="OS Installation 1"/>

 9. I am installing Windows Server 2022 Standard Evaluation (Desktop Experience). This is not an upgrade so I went through the Custom installation option and selected my drive to install the system.

<img src="https://i.imgur.com/jmbxuG0.png" height="60%" width="60%" alt="OS Installation 2">>

 10. Set up the administrator user.

<img src="https://i.imgur.com/w91rO9I.png" height="60%" width="60%" alt="OS Installation 3">

 11. Press Ctrl+Alt+Del to unlock the OS. In Proxmox, you will have to go through a separate tab to press Ctrl+Alt+Del as shown in the picture below. 
 12. Sign in and Server Manager should automatically open. If it doesn't, open the start menu and search for Server Manager. Click the first app that appears.

<img src="https://i.imgur.com/8wlMQcL.png" height="60%" width="60%" alt="OS Installation 3">

<h2>Server Machine Setup</h2>
<h3>Workgroup vs. Domain</h3>

A workgroup is a logical network more suited for the home environment. Each device is independently configured to a sole user and cannot be seamlessly transferred from one device to the next. Each device's only connection in the workgroup is the network they are all connected to. This is ideal for the home environment where the family has individual devices such as computers that are uniquely configured to a different person in that household.

A domain is a logical network that is suited for companies that have plenty of employees onboard. This allows admins who maintain the domain controller to register users and computers to the server. Once a user has their login credentials and permissions set, they will be able to log into any computer within that domain that is within their privilege to log in to. Those users and computers can then be further organized into organizational units(OU). For example, there can be an OU for different offices named "California" and "Nevada". Within those OUs there can be sub-OUs for the Finance and Sales teams for California and Washington respectively and so on. I can then apply group policies to these OUs and set them up in a way where the Sales team only has access to Sales data and the Finance team can only access Finance data. 
This is a very broad description of what a domain is as what an admin can do within a domain it can be very complex. Domains essentially give admins an easier time when trying to organize users and devices for large companies.

<h3>Server Preparation</h3>

A domain controller is essentially the domain. Furthermore can be/are servers. These terms may be used synonymously within this demo and in the real world as well.

<h4>First things first. Set a Computer Name!</h4>

This is important because computers come with a default name that may not be too user-friendly. I want to set my server with an easy-to-remember name that also refers to the company name. To do so, follow these steps: (Naming conventions and organization practices vary throughout the professional landscape. The name I use may not be professionally accurate but will work for this demo.)
 1. Navigate to the Settings App.
 2. Select the System path.
 3. Navigate to the About Tab.
 4. Press "Rename this PC" and enter a new name. Many of these configuration updates will require a restart and this is one of those configurations. 



 <img src="https://i.imgur.com/f85kjgm.png" height="60%" width="60%" alt="PC Rename 1">
 
 5. The Server Name is now updated:

<img src="https://i.imgur.com/bgeQd9w.png" height="60%" width="60%" alt="PC Rename 2">

<h4>Next. IP and DNS Setup!</h4>

Since this domain is the server that my company is going to use for Active Directory Domain Services (AD DS), I will need to set a static IP address and a preferred DNS address. The reason why we have to set up the DNS address is because AD DS utilizes DNS to perform various functions such as authentication. When we download the DNS role/tools onto the server, plenty of records will be written to the server for the server's DNS functions.
To set this up, begin by following these steps:

1. Navigate to the Control Panel.
2. Press Network and Internet.
3. Press Network and Sharing Center.
4. Press the connection you see on the right-hand side of the window or Press Change adapter settings. From there, I can select my current connection which is "ethernet".
5. Click Properties.
6. Double-click Internet Protocol Version 4 (TCP/IPv4).
7. To manually add addresses, I have to select "Use the following..." flags.
8. I can now give my server a static IP address, subnet mask, and a DNS server address.
   - The DNS server address will be the server so I used my server's IP address as the DNS server address. A loopback address can also be used.
   - Note: the computers that will be set up in my company will need to utilize the correct DNS server address.

<img src="https://i.imgur.com/axxrJAH.png" height="60%" width="60%" alt="IPv4 Settings Page">

I am pretty done with the data setup side of things for this computer. Next, I get into installing AD DS. 

<h3>Server Manager: AD DS Installation</h3>
Now, I am going to install AD DS onto my server, change my server from a Workgroup configuration to a Domain Configuration, and finally dive into AD DS itself and utilize Organizational Units.

1. Open Server Manager.
   - A few things you will notice is that the server is set up as a workgroup. I will change this in a second.
   - The computer name is how I set it up to be.
2. Click Manage at the top right.
3. Select Add Roles and Features.

<img src="https://i.imgur.com/5k1qfVL.png" height="60%" width="60%" alt="Server Manager Roles and Features">

4. Before we begin tab is an informative tab that explains the Add Roles and Features Wizard. Click Next.
5. Under the "Installation Type" tab will allow me to select the type of installation I want. Since this will be the first domain for my company, I will be choosing a role-based installation.
6. Under the "Server Selection" tab will let me choose my server to install roles and features to. I highlighted my computer and hit next.
7. Under the "Server Roles" tab shows all the roles you can install to the server. I will flag the Active Directory Domain Services option. Flagging the role will action a pop-up that will show me all the required features/tools that will be installed with AD DS. Press add features and next.
8. I will be ignoring the "Features tab" for now since I am solely demonstrating Active Directory.
9. An AD DS tab will pop up to show best practice information.
10. The Confirmation tab will show the roles and features that will be installed. Press Install to begin the installation. and let the process finish. I will have to restart my VM once the installation is complete.
11. Once my system is booted up, it is now time to promote the server to a domain controller. Previously it was set as a workgroup. There is a task within my notifications that will display a wizard to help me set this up.

<img src="https://i.imgur.com/MnBYycM.png" height="60%" width="60%" alt="Post-Deploy Config">

12. Now that the Deployment Configuration is displayed, I am going to flag the option to add a new forest. This will allow me to set up our domain controller in a new domain.  There are two other options:
- Add a domain controller to an existing domain. This will make the forest fault-tolerant.
- Add a new domain to an existing forest.
- Note: both these options were not chosen since I am setting everything up from scratch.
13. I am now able to name the Root Domain. For this, I named it "johnny.local".

<img src="https://i.imgur.com/JX54PKZ.png" height="60%" width="60%" alt="Deployment Configuration">

14. I pretty much keeping everything default tabbing through the different sections. Everything was kept to default cause I did not need to change anything besides setting a DSRM password and a NetBIOS domain name (JOHNNY). A DSRM password is what is used to log into the Restore Mode in case needed for emergency fixes such as some system malfunction and needing to restore for a backup.
15. Once again, the system will reboot. I am now up and running. We now have the AD DS role as well as my local server as Domain: "Johnny.local" instead of "Workgroup: workgroup".

<img src="https://i.imgur.com/CCwCKyC.png" height="60%" width="60%" alt="Post Configuration">

<h4> There you have it! We have installed Windows Server 2022 OS into a virtual machine and successfully set up Active Directory. There will be a deeper dive into the organization units and group policy realm in my next portfolio entire. If there is anything I can improve on, please let me know. I am always open to feedback. Thank you!!!!</h4>
