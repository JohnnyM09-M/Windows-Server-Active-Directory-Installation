# Windows-Server-Active-Directory-Installation
Today Ill be demonstrating the setup for Windows Server 2022 and Active Directory



<h2>Windows Server 2022 Installation In VM VirtualBOX Manager</h2>

 1. Download Oracle VM VirtualBox Manager.
 2. Download an evaluation copy of Windows Server 2022 from Microsoft's website.
 3. Once installed, open program.
 4. Create a new virtual machine.
 5. Select the Windows Server ISO file and flag "Skip Unattended Installation"
    - This will allow user to install the Desktop Experience Version of the OS. If not, the unattended installation will install the server core OS which runs exclusively in the Windows CLI, unless there is a script provided to specify the correct evaluation copy.

<img src="https://i.imgur.com/SJaHKyY.png" height="60%" width="60%" alt="VM Setup 1"/>

 6. Run through the entire setup process to complete the Virtual Machine set up. If necessary, there are plenty of YouTube videos out there that go through the installation process in-depth.
 7. Once setup is complete, open the VM.
 8. Click next on the first prompt and press "Install now".

<img src="https://i.imgur.com/CfP8Y7V.png" height="60%" width="60%" alt="OS Installation 1"/>

 9. I am installing Windows Server 2022 Standard Evaluation (Desktop Experience). This is not an upgrade so I went through the Custom installation option and selected my drive to install the system to.

<img src="https://i.imgur.com/jmbxuG0.png" height="60%" width="60%" alt="OS Installation 2">>

 10. Set up the administrator user.

<img src="https://i.imgur.com/w91rO9I.png" height="60%" width="60%" alt="OS Installation 3">

 11. Press Ctrl+Alt+Del to unlock the OS. In VirtualBox, press Ctrl+Del to simulate the required keys.
 12. Sign in and Server Manager should automatically open. If it doesn't, open the start menu and search for Server Manager. Click the first app that appears.

<img src="https://i.imgur.com/8wlMQcL.png" height="60%" width="60%" alt="OS Installation 3">

<h2>Server Machine Setup</h2>
<h3>Workgroup vs. Domain</h3>

A workgroup is a logical network more suited for the home environment. Each device is independently configured to a sole user and cannot be seemlessly transfered from one device to the next. The only connection each device has in the workgroup is the network all of them are connected too. This is ideal for the home environment where the family has individual devices such as computers that are uniquely configured to a different person in that household.

A domain is a logical network that is suited for companies that have plenty of employees onboard. This allows for admins who maintain the domain controller to register users and computers to the server. Once a user has there login credentials and permissions set, they will be able to log into any computer within that domain that is within their privilege to log in to. Those users and computers can then be further organized into what are called organizational units(OU). For example, there can be an OU for different offices named "California" and "Washington". Within those OUs can be sub-OUs for the Finance team and the Sales team for both California and Washington respectively and so on. I can then apply group policies to these OUs and set them up in a way where the Sales team only has access to Sales data and the Finance team can only access Finance data. 
This is a very broad description of what a domain is as what an admin can do within domain can be very complex. Domains essentially give admins an easier time when trying to organize users and devices for large companies.

<h3>Server Preparation</h3>

To quickly brief, a domain controller is  essentially the domain. And furthermore can be/are servers. These terms may be used synonymously within this demo and in the real world as well.

<h4>First things first. Set a Computer Name!</h4>

This is important because computers come with a default name that may not be too user-freindly. I want to set my server with an easy-to-remember name that also refers to the company name. To do so, follow these steps: (Naming conventions and organization practices vary throughout the professional landscape. The name I use may not be professionaly accurate but will work for this demo.)
 1. Navigate to the Settings App.
 2. Select the System path.
 3. Navigate to the About Tab.
 4. Press "Rename this PC" and enter a new name. Many of these configuration updates will require a restart and this is one of those configurations. 



 <img src="https://i.imgur.com/f85kjgm.png" height="60%" width="60%" alt="PC Rename 1">
 
 5. The Server Name is now updated:

<img src="https://i.imgur.com/bgeQd9w.png" height="60%" width="60%" alt="PC Rename 2">

<h4>Next. IP and DNS Setup!</h4>

Since this domain is the server that my company is going to use for Active Directory Domain Services (AD DS), I will need to set a static IP address and a preferred DNS address. The reason why we have to set up the DNS address is because AD DS utilizes DNS in order to perform various functions such as authentication. When we download the DNS role/tools onto the server, plenty of records will be written to the server for the server's DNS functions.
To set this up, follow these steps:

1. Navigate to the Control Panel.
2. Press Network and Internet.
3. Press Network and Sharing Center.
4. Press the connection you see on the right-hand side of the window or Press Change adapter settings. From there, I can select my current connection which is "ethernet".
5. Click Properties.
6. Double-click Internet Protocol Version 4 (TCP/IPv4).
7. In order to manually add addresses, I have to select "Use the following..." flags.
8. I can now give my server a static IP address, subnet mask, and a DNS server address.
   - The DNS server address will be the server so I used my server's IP address as the DNS server address. A loopback address can also be used.
   - Note: the computers that will set up in my company will need to utilize the correct DNS server address.

<img src="https://i.imgur.com/axxrJAH.png" height="60%" width="60%" alt="IPv4 Settings Page">

I am pretty done on the data setup side of things for this computer. Next, I get into the Server Manager and install AD DS. 

<h3>Server Manager: AD DS Installation</h3>
Now, I am going to install AD DS onto my server, change my server from a Workgroup configuration to a Domain Configuration, and finally dive into AD DS itself and utilize Organizational Units.

1. Open Server Manager.
   - A few things you will notice is that the server is est up as a workgroup. I will change this in a second.
   - The computer name is how I set it up to be.
2. Click Manage at the top right.
3. Select Add Roles and Features.

<img src="https://i.imgur.com/5k1qfVL.png" height="60%" width="60%" alt="Server Manager Roles and Features">

4. The Before you begin tab is an informative tab that explains the Add Roles and Features Wizard. Click Next.
5. The Installation Type tab will allow me to select the type of installation I want. Since, this will be the first domain for my company, I will be choosing a role-based installation.
6. The Server Selection tab will let me choose my server to install roles and features to. I highlighted my computer and hit next.
7. The Server Roles tab shows all the roles you can install to the server. I will flag the Active Directory Domain Services option. Flagging the role will action a pop-up that will show me all the required features/tools that will be install with AD DS. Press add features and next.
8. I am ignoring the Features tab for now since I am solely demonstrating Active Directory. Press next.
9. An AD DS tab will pop up to show best practice information. Press next.
10. The Confirmation tab will show the roles and features that will be installed. Press install to begin the installation. and let the process finish. I will have to restart my VM once installation is complete.
11. Once my system was booted up, it is not time to promote the server to a domain controller. Previously it was set as a workgroup. There is a task within my notifications that will display a wizard to help me set this up.

<img src="https://i.imgur.com/MnBYycM.png" height="60%" width="60%" alt="Post-Deploy Config">

12. Now that the Deployment Configuration is displayed, I am going to flag the option to addd a new forest. This will allow me to set up a our domain controller in a new domain.  There are two other options:
- Add a domain controller to an existing domain. This will make the forest fault tolerant.
- Add a new domain to an existing forest.
- Note: both these options were not chosen due to the fact that I am setting everything up from scratch. Ground zero!
13. I am now able to name the Root Domain. For this, I coined the name to be rock.local.

<img src="https://i.imgur.com/JX54PKZ.png" height="60%" width="60%" alt="Deployment Configuration">

14. I pretty much keep everything default tabbing through the different sections. Everything was kept to default cause I did not need to change anything besides setting a DSRM password and a NetBIOS domain name(ROCK). A DSRM password is what is used to log into the Restore Mode in case the need for emergency fixes such as some system malfunction and needing to restore for a backup.
15. Once again, the system will reboot. I am now up and running. We now have the AD DS role as well as my local server now says "Domain: rock.local" instead of "Workgroup: workgroup".

<img src="https://i.imgur.com/CCwCKyC.png" height="60%" width="60%" alt="Post Configuration">

<h4>And there you have it! We have installed Windows Server 2022 OS into a virtual machine and successfully set up Active Directory. I will be cutting this section of the Active Directory lab short. I will dive deeper into the organization units and group policy realm in the next lab. That will be documented in a separate portfolio item! If there is anything I can improve on, please let me know. I am always open to feedback. Thank you!!!!</h4>
