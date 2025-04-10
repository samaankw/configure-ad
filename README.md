<img src="https://i.imgur.com/6sH4frV.jpeg" height="80%" width="80%">
<h2> On-Premises Active Directory Deployed in the Cloud (Azure) </h2> 
<p>This tutorial provides a step-by-step guide to implementing on-premises Active Directory using two Azure virtual machines. It walks you through the setup, configuration, and integration process to simulate a real-world AD environment in the cloud. </p>

<H2> Environments and Technologies Used</H2>
-  Microsoft Azure (Virtual Machines/Compute)
-  Remote Desktop
-  Active Directory Services
-  Powershell

<h2>  Operating Sytems Used </h2>
-  Windows Server 2022
-  Windows 10 (21H2)


<H2> Deployment and Configuration Steps </H2>
<p>Begin by creating two virtual machines in Azure.

If you need assistance with this process, refer to my step-by-step tutorial [here].

The first virtual machine will serve as the Domain Controller, and should be configured as follows:

Name: DC-1

Image: Windows Server 2022

During the setup, make sure to take note of the virtual network (vNet) that Azure automatically creates—this will be important when configuring the second virtual machine to ensure they are on the same network. </p>

<h2> Step 1: Set up Resources in Azure</h2>

<P>Go to the Azure Portal

Search for “Virtual Machines” in the top search bar

Click “Create” > “Azure virtual machine”

Fill in the Basics tab:

Subscription and Resource Group

Name your VM (e.g., DC-1)

Region (keep default or choose closest to you)

Image: Select Windows Server 2022

Size: Choose a small size, like B2s for testing

Username and Password for login

Click Next through tabs (you can leave defaults)

Click Review + Create > Create</P>

<img src="https://i.imgur.com/4dinb1N.jpeg" height="80%" width="80%">

-- To ensure DC-1 always has the same internal IP address, set its Virtual Network Interface Card (vNIC) to use a static private IP:

Go to DC-1’s page in the Azure portal

Select the Networking tab from the left menu

Click the link next to Network Interface to open its settings

In the Network Interface settings, go to IP Configurations and select ipconfig1

Change the IP assignment from Dynamic to Static

This change ensures that DC-1 retains the same private IP address, which is important for consistent domain services.

<img src="https://i.imgur.com/6840ZMZ.jpeg" height="80%" width="80%">
<img src="https://i.imgur.com/DRIcAuo.jpeg" height="80%" width="80%">
<img src="https://i.imgur.com/5Ib7Poy.jpeg" height="80%" width="80%">

Create the second virtual machine, which will act as the Client machine:

Name: Client-1

Image: Windows 10 Pro

Make sure to select the same Resource Group and Virtual Network (vNet) as DC-1

Using the same vNet ensures both machines can communicate with each other, which is essential for domain joining and testing Active Directory functionality.

<img src="https://i.imgur.com/VFATMIU.jpeg" height="80%" width="80%">
<img src="https://i.imgur.com/2fiDWVt.jpeg" height="80%" width="80%">

<h2>   Step 2: Ensure Connectivity Between Client and Domain Controller </h2>
<p>Log in to Client-1 using Microsoft Remote Desktop.

Once you're in, open the Command Prompt by searching for it in the Start menu. Try pinging DC-1’s private IP address (for example, 10.1.0.4).

You’ll notice that the ping requests keep timing out. This is happening because ICMP (ping) traffic is blocked by default in Windows Firewall on DC-1.

To allow ping requests, you’ll need to enable ICMPv4 on DC-1’s Windows Firewall settings. </p>

<img src="https://i.imgur.com/rhuM7iF.jpeg" height="80%" width="80%">
<p>Log in to DC-1 using Microsoft Remote Desktop.

Once logged in:

Open the Start Menu, then navigate to Windows Administrative Tools.

Select Windows Defender Firewall with Advanced Security.

In the left-hand menu, click on Inbound Rules.

Sort the list of rules by Protocols to make it easier to find the necessary entries.

Now, locate and enable the following inbound rules:

Core Networking Diagnostics

ICMPv4

Enabling these rules will allow ping requests to pass through the firewall. </p>

<img src="https://i.imgur.com/KB1bZZH.jpeg" heigth="80%" width="80%">
<img src="https://i.imgur.com/MbqlR2t.jpeg" heigth="80%" width="80%">

<p>Log back into Client-1, and you'll notice that the command line will now automatically begin successfully pinging DC-1. This confirms that the firewall settings on DC-1 have been updated to allow ICMPv4 traffic. </p>

<img src="https://i.imgur.com/zccyezh.jpeg" heigth="80%" width="80%">

<h2> Step 3: Install Active Directory </h2>
<P>Log into DC-1 and open Server Manager (you can find it by searching in the Start menu).

Click on Add Roles and Features to start adding a new feature.

Follow the instructions until you get to the section called Server Roles.

Look for Active Directory Domain Services and check the box next to it.

When a pop-up appears saying Add Features, click Add.

Click Next to continue and then click Install to finish.

This will set up the Active Directory service on your DC-1 server. </P>

<img src="https://i.imgur.com/1I3fZNv.jpeg" heigth="80%" width="80%">
<img src= "https://i.imgur.com/TueYbte.jpeg"  heigth="80%" width="80%">
<p>In the top-right corner of the Server Manager Dashboard, click the notification flag and select "Promote this server to a Domain Controller.</p>

<img src="https://i.imgur.com/77uy7Gc.jpeg" heigth="80%" width="80%">

<P>o set up your domain:

After the installation process starts, select "Add a New Forest".

In the Root domain name field, enter mydomain.com (or whatever name you want for your domain).

Click Next.

Create a strong password for the Directory Services Restore Mode (DSRM) and remember it, as you'll need it later.

Click Next and continue following the prompts, making the default selections unless you need something specific.

Once everything looks good, click Install to complete the process.

This will finish setting up the domain on your server. </P>

<img src="https://i.imgur.com/mNBy99j.jpeg" height="80%" width="80%">

<p>After the installation, DC-1 will automatically restart.

Once it’s back up, log in to DC-1 using the username: mydomain.com\labuser.</p>
<img src="https://i.imgur.com/dqc8Bsx.jpeg" height="80%" width="80%">
<h2>   Step 4: Create and Admin and Random User Account in Active Directorty v1.15.8 </h2>
<p>On DC-1, open Server Manager.

In the top-right corner of the screen, click on Tools.

From the dropdown menu, select Active Directory Users and Computers.

This will open the tool where you can manage users and computers in your domain. </p>
<img src="https://i.imgur.com/AXI688N.jpeg" height="80%" width="80%">
<P>n Active Directory Users and Computers:

Right-click on mydomain.com and select New > Organizational Unit (OU).

Create the first OU and name it _EMPLOYEES.

Create the second OU and name it _ADMINS.

These Organizational Units (OUs) will help organize your users by categories, such as employees and admins.</P>

<img src="https://i.imgur.com/b3gj5Pv.jpeg" height="80%" width="80%">

<p>In Active Directory Users and Computers:

Right-click mydomain.com and click Refresh to move the new Organizational Units (_EMPLOYEES and _ADMINS) to the top.

Navigate to the _ADMINS OU.

Right-click the _ADMINS OU, then select New > User.

Enter the First Name as Jane and the Last Name as Doe.

For the User login name, enter jane_admin.

Click Next and create a password for the user.

Uncheck all the boxes for password settings (like "User must change password at next logon").

Click Next, then click Finish to complete the creation of the user.

This adds Jane Doe as a user in the _ADMINS OU with the login name jane_admin. </p>
<img src="https://i.imgur.com/l63FgOW.jpeg" height="80%" width="80%">
<img src="https://i.imgur.com/Fw5SjZ0.jpeg" height="80%" width="80%">

<P>In Active Directory Users and Computers:

Navigate to the _ADMINS OU and right-click on Jane Doe.

Select Properties.

Go to the "Member Of" tab and click Add.

Type the names of the domain administrator groups (e.g., Domain Admins) into the box.

Click Check Names to confirm the groups, then click OK.

Click Apply to save the changes.

Now, log out of DC-1 as labuser and log back in using the username mydomain.com\jane_admin. This will sign you in as an admin with the necessary permissions.</P>
<img src= "https://i.imgur.com/Emlz2ZV.jpeg"   height="80%" width="80%">

<img src="https://i.imgur.com/Q6rolyR.jpeg" height="80%" width="80%">

<H2> Step 5: Join Client-1 to your domain (mydomain.com)</H2>
<p>n the Azure portal:

Go back to the Client-1 Virtual Machine.

On the left-hand menu, select Networking.

Click the link next to the Network Interface Card (NIC).

Under DNS Servers, select Custom.

Enter DC-1’s private IP address (e.g., 10.1.0.4) in the DNS field.

Click Save to apply the changes.

Once the update is complete, select Restart and click Yes to reboot Client-1.

This will set DC-1 as the DNS server for Client-1. </p>

<img src="https://i.imgur.com/FUefJ9m.jpeg"    height="80%" width="80%">

<img src="https://i.imgur.com/w1KQFEb.jpeg"   height="80%" width="80%">
<img src="https://i.imgur.com/pHnz32O.jpeg" height="80%" width="80%">

<p>Log back into Client-1 using Microsoft Remote Desktop as the original local admin (labuser).

Right-click the Start menu and select System.

On the right-hand side of the screen, click Rename this PC (Advanced), then select Change.

Under Member of, select Domain.

In the Domain field, type mydomain.com and click OK.

When prompted, enter the Username as mydomain.com\jane_admin and enter the password.

Click OK, then restart the computer for the changes to take effect.

This will join Client-1 to the domain as part of mydomain.com.</p>

<img src="https://i.imgur.com/grHryu3.jpeg" height="80%" width="80%">

<img src= "https://i.imgur.com/DW23ZHW.jpeg"   height="80%" width="80%">


