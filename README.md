![](https://camo.githubusercontent.com/e38ed1b3f8f0cff62ee2117afc871396adea4a1266dbebb482b5080db92184af/68747470733a2f2f692e696d6775722e636f6d2f705535413538532e706e67)

## Configuring On-premises Active Directory In The Cloud (Azure)

### Introduction:
This step-by-step guide will help you set up a Domain Controller (DC) and a client machine in Azure. By the end, you'll have a working Active Directory environment and understand key administrative tasks. Belowe is what we will be creating,the client machine will use the DC as its DNS server.

![](https://camo.githubusercontent.com/f302b37525772f8c8c9c19d4f36eb35200dc3ea2e4c0016f0b4466b7ad70bf32/68747470733a2f2f692e696d6775722e636f6d2f6432324648496d2e706e67)

###  Environments and Technologies Used
-  Microsoft Azure (Virtual Machines/Compute)
-  Remote Desktop
-  Active Directory Domain Services
-  PowerShell

###  Operating Systems Used
-  Windows Server 2022
-  Windows 11 (21H2)

###  Setting Up a Domain Controller in Azure
-  Setting Up the Environment
  -  Create a Resource Group
    -  Go to the Azure Portal
    -  Click on Resource Groups → Create New
    -  name it "activedc-rg"
    -  choose a region
    -  Click Review + Create → Create

###  Create a Virtual Network & Subnet
In the Azure Portal, go to Virtual Networks → Create
-  Name it "activedc-venet"
-  Choose the "activedc-rg" resource group
-  Set the region to match your Resource Group
-  Under IP Addressing, add a subnet:
  -  Name: "activedc-subnet"
-  Address Range: 10.0.0.0/24
-  Click Review + Create → Create

### Create The Domain Controller (DC-1) VM
In Azure Portal Go to Virtual Machines → Create New
-  Choose Windows Server 2022
-  Name it DC-1
Set credentials:
-  Username: adminuser
-  Password: P@ssword1234
Attach it to the created Vnet and Subnet
Click Review + Create → Create

### Configure DC-1 VM
Set its Private IP to Static
-  (Go to Networking → Network Interface → ipconfig → Change to Static)
-  Log into DC-1 and Disable Windows Firewall
-  Find "Core Networking Diagnostics" and "ICMPv4", enable these two inbound rules
   (for easier connectivity testing)

![](https://github.com/user-attachments/assets/6956f11b-ac5e-4da9-a903-62154d147b77)
![](https://i.imgur.com/kSILLXr.png)

### Create The Client VM (Client-1)
In Azure Portal Go to Virtual Machines → Create New
-  Choose Windows 11
-  Name it Client-1
Set credentials:
-  Username: adminuser
-  Password: P@ssword1234
-  Attach it to the created Vnet and Subnet
-  Click Review + Create → Create

 ### Configure Client-1 VM
-  Change its DNS settings to DC-1's Private IP
-  Restart Client-1
-  Login to Client-1 and ping DC-1’s Private IP to confirm connectivity
-  Run ipconfig /all in PowerShell to check if DC-1’s IP is set as DNS
-  Ping DC-1's private IP Address (for example, 10.0.1.4)
  -  Type "ping -t 10.0.1.4" into the command-line interface

l![image](https://github.com/user-attachments/assets/347f65aa-c647-41ce-ae14-45e0a2db7372)
![](https://github.com/user-attachments/assets/65c1352c-11e5-4951-8057-f1d11dbf9e71)
![image](https://github.com/user-attachments/assets/b406efab-9544-4726-b19a-d61ed7f3f689)

### Installing And Configuring Active Directory
Install Active Directory on DC-1
-  Login to DC-1
-  Open Server Manager → Add Roles & Features
-  Install Active Directory Domain Services (AD DS)

![](https://i.imgur.com/7hu3dEz.png)
![](https://i.imgur.com/PGidx4m.png)

### Promote DC-1 as a Domain Controller
At the top right of the Server Manager Dashboard
-  click on the flag
-  Set up a new forest: mydomain.com
-  Select "Add a New Forest"
-  Root domain name: mydomain.com
-  Select Next
-  Create a password
-  Select Next and follow the prompts, and do not check DNS, 
-   Select Install to complete the installation
DC-1 Restarts by itself

![](https://i.imgur.com/q4wnlkN.png)
![](https://i.imgur.com/H6L7zQv.png)

Log in as: mydomain.com\adminuser

### Create a Domain Admin User
Go to ADUC (Active Directory Users & Computers)
Create an Organizational Unit (OU) called _EMPLOYEES and _ADMINS
-  On DC-1, open Server Manager
-  Click Tools at the top-right of the screen
-  Select Active Directory Users and Computers

![](https://i.imgur.com/ZQDyfxS.png)
![](https://i.imgur.com/5WxSTz2.png)

-  Right-click mydomain.com > New > Select Oranizational Unit (OU)
-  Create two OUs
-  Name the first "_EMPLOYEES"
-  Name the second "_ADMINS"
![](https://i.imgur.com/UoHoLTP.png)

### Create A New User Named 
-  Name: "Kane Ben" 
-  with username Kane_admin
-  Add Kane_admin to "Domain Admins" group
  -  Go to the _ADMINS OU
  -  Right-click the name of the OU > New > User
  -  fill the details above
  -  Select Next
  -  Create a password
  -  Uncheck all boxes
Select Next and then select Finish

![](https://i.imgur.com/ThuFtoj.png)

### Give Kane Admine Priviliages
-  Go to the _ADMINS OU
-  Right-click Kane Ben > select Properties
-  Click the tab named "Member of" > select Add
-  Type in the names of your Domain Admins
-  Select "Check Names" > OK > Apply

![](https://i.imgur.com/uCqf6ig.png)
![](https://i.imgur.com/N4Acn6A.png)

Log out of DC-1 as  and log back in as “mydomain.com\kane_admin”
Kane_admin has administrator priviliages
![](https://i.imgur.com/vKW6Dob.png)
![](https://i.imgur.com/NcDx10U.png)

### Join Client-1 to the Domain
Login as admainuser and go to:
-  System → Advanced System Settings → Computer Name → Change
-  Enter Domain: mydomain.com
-  When prompted, enter:
-  Username: mydomain.com\kane_admin
-  Password: P@ssword1
-  Restart Client-1 and log in as kane_admin
-  Verify in ADUC that Client-1 appears under "Computers"
![image](https://github.com/user-attachments/assets/a7f841de-f33f-4e01-843a-32fa60a9f72a)
![image](https://github.com/user-attachments/assets/4f6b7106-b6c0-4284-b07d-4e05384d3378)
![image](https://github.com/user-attachments/assets/79967588-3467-4c7e-b122-8563e0218dc4)

###  Configuring Remote Desktop And  Group Policies
Enable Remote Desktop for Domain Users
-  Log into Client-1 as mydomain.com\Kane_admin
-  Go to System → Remote Desktop (Right-click the Start menu and select System, select Remote Desktop)
-  Under User Accounts, click "Select Users That Can Remotely Access This PC > select Add
-  Type in the name of your domain users
-  Select "Check Names" > OK > OK
![image](https://github.com/user-attachments/assets/11a483db-0f94-43f7-a83b-0d7a7ac14399)

Now any domain user can log into Client-1 via RDP

### Create Multiple Users Via PowerShell
Login to DC-1 as Kane_admin
-  Open PowerShell ISE (as Admin)
-  Copy & Paste this script [here](https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1)
-  Make sure the OU path matches your AD structure
-  Run the script and watch users being created!
-  Try logging into Client-1 with one of the new users

![](https://i.imgur.com/Q5s976v.png)
![](https://github.com/user-attachments/assets/fec2dcff-6282-4988-b1b1-27811ecfa9b4)
![image](https://github.com/user-attachments/assets/99382a2b-cdd9-4794-a1ff-7c79bfd98446)

#### Implementing Security And Account Lockouts

Configure Account Lockout Policy
On DC-1, open Group Policy Management (w + R gpmc.msc)
-  Expand forest > expand domain > expand your system domain (mydoman.com) > eidit dafault domain policies atterch to your domain
Navigate to:
-  Computer Configuration → Policies → Windows Settings → Security Settings → Account Lockout Policy
-  Set these values:
  -  Lockout Duration: 30 minutes
  -  Lockout Threshold: 5 failed logins
  -  Reset Counter After: 10 minutes
  -  Apply the GPO to the domain
  -  Run gpupdate /force to apply changes

![](https://i.imgur.com/TzRO6H1.png)
![](https://i.imgur.com/a2cTkAA.png)
![](https://i.imgur.com/F9IZkPL.png)
![image](https://github.com/user-attachments/assets/6727c2c9-f22d-4c70-ac36-2a6b08d145f6)


###  Test Account Lockout
Try logging in 6 times with a wrong password
-  Check ADUC → Find the locked user (right click mydomin.com, find the usesr)
-  double-click the usesr → Account > Check the unlock
-  Try logging in again

![image](https://github.com/user-attachments/assets/0be13336-1be8-4a37-af4c-56f5f653e171)
![](https://i.imgur.com/MRXFfSP.png)

###  Monitor Security Logs
-  Open Event Viewer (eventvwr.msc)
-  Go to Windows Logs → Security you can right click to search for what you looking for 
-  Look for login failures and account lockouts
-   You’ve now configured security policies and tested account lockouts!

![](https://i.imgur.com/tlpilmU.png)

Congratulations! You have implementated on-premises Active Directory and created users within an Azure virtual machine! and Hace also set login permission



