<h1>Active Directory & PowerShell Automation Lab</h1>

 ### [YouTube Demonstration](https://www.youtube.com/watch?v=ZfVfk0U6_Fs)

<h2>Description</h2>
This project will consist of setting up Active Directory using Oracle Virtual Box. We'll be using Windows Server 2019 to host ADDS and after configuring our admin account, DHCP, FQDN, RAS, and NAT services on our server we'll be running a PowerShell script to automate user account creation for the AD database. Following this, testing this server by creating a Windows 10 Pro client to connect to our domain and communicate with the internet through the AD services we've configured.
<br />


<h2>Languages and Utilities Used</h2>

- <b>PowerShell</b> 
- <b>Active Directory</b>
- <b>RAS</b>
- <b>NAT</b>
- <b>DHCP</b>
- <b>FQDN</b>
- <b>Windows 10 Pro</b>
- <b>Oracle Virtual Box</b>
- <b>Windows Server 2019</b>

<h2>Phase by phase walk-through:</h2>

<p align="center">
Download Oracle Virtual box (first) + Extension pack (second)
https://www.virtualbox.org/wiki/Downloads

Download Windows 10 ISO (May require you to fill out basic info)
https://www.microsoft.com/en-us/software-download/windows10

Download Windows Server 2019 (May require you to fill out info)
https://www.microsoft.com/en-us/evalcenter/download-windows-server-2019

Phase 2 Virtual Box Setup & VM Creation

Install & setup Virtual box using the default settings in the installation wizard. 

First VM we'll create is the Windows server, on the main screen of Virtual box click "create". Give it a simple name like DC (Domain Controller) or Main Node. Also choose the windows server 2019 ISO from your downloads folder or wherever you saved it to and enable "Skip Unattended Installation" to skip username and password creation. 

From there you can select the default options on the next few screens and your VM will be ready to boot.

Before booting up, for quality-of-life purposes, right click on your new VM and choose settings -> advanced and enable Bidirectional for both "Shared Clipboard" & "Drag n' Drop". This allows for copy and paste and file sharing from your desktop to the VM!

From there navigate to the Network tab and enable "Adapter 2" and on the "attach to:" setting select "Internal network" to create a 2nd NIC for the windows server.

Optional: Depending on your computers power you can add more cores inside the System settings tab and add more cores + memory however 4 cores and 6gb of memory are recommended for most Home-labs :) 

Phase 3 Windows Server 2019 Config

Once you bootup your Windows Server VM you'll be asked the language you want displayed on your OS and which version of Windows Server you want installed. Choose the 2nd option Windows Server 2019 Standard Evaluation (Desktop Experience). From there your VM will restart multiple times which downloading.

Eventually you will boot into Windows Server where you'll create a user & pass. You just need to create a password that you'll remember for this lab. 
In this case it can be something simple but in the real world you will need a more complicated user and pass.

At the Windows Server login screen, you'll be asked to press CTRL+ALT+DEL, on a VM usually involve unique keystrokes so to make this easy at the top of your VM screen you'll see an input -> keyboard tab and you can select (CTRL+ALT+DEL) to login.

Log in with your password and you're in.

*At this part follow along with my video tutorial closely to improve the VM performance for quality-of-life improvements. *

Next type in "Ethernet settings" in your windows search bar and open -> "Change adapter options" and you'll notice two NIC's (Make sure you added your internal NIC in the VM Network settings on Virtual Box). Change the 1st NIC to "_Internet_" and the second "_Internal Network_" and add an IP to this one by right-clicking and navigate to Properties->Click Internet Protocol IPV4->Properties and select "Use the following IP Adress" and fill the rows with the following IP: 172.16.0.1, Mask: 255.255.255.0, Gateway <empty>, 
DNS: 127.0.0.1 -> click OK to save and close the window.

Next step for this phase is to rename your PC by right-clicking the windows icon and go System->Rename this PC and change it DC or Main Node and restart your PC for the next steps.

Phase 4 - Post Deployment Configuration

From there you'll want to navigate to the "Server Manager" app on windows and click "Add Roles & Features" on the dashboard and you can select all the default settings provided until you get to the Server Roles page and enable "Active Directory Domain Services". After that you can click next and choose all the default options and click "Install" at the last page.

After the Server Role was installed, you can close that page and on the Server Manager dashboard you'll notice a flag with a yellow warning icon. Click "Promote this server to a domain controller" on this flag to add a FQDN to our server.

From there you'll see an option in the Deployment configuration "Add a new forest" and you can assign any domain name you want. For me I named my domain opsecwithbryant.com and on the next page; Domain Controller Options setup a password. You can use the same password but don't do this in a professional work setting. For the other pages you can choose the default options and click "Install" on the last page and wait for it to finalize.

Phase 5 - Domain Admin Account Creation

After your installation is complete your VM will restart, and you'll need to log in again. On the windows home screen open up the search bar and find "Active Directory Users and Computers" *Make sure you found the right app*

From there locate your custom FQDN you created, you'll see it on the left panel of the app under "Saved Queries". Then right click your FQDN to create an organizational unit named _ADMINS to place your admin accounts going forward. New->Organizational Unit. After naming the folder _ADMINS fill out the rest of the fields with your name and username the click "Next".

*In most organizations the admin usernames will be first initial followed by last name, ex- a-bgonzalez@domain.com*

On the next page you'll assign a password, (Repeat this password but don't do this in the real world). Then disable "User must change password on next logon", enable "Password never expires" (This option applies to this HomeLab environment but should be enabled in the real world for security reasons. Click "Next" then "Finish" to create this account.

To officially turn this account into an admin right click on the account; Bryant Gonzalez->Properties->Member Of->Add->Enter the object names to select.
In this field type "domain admins" and click "Check Names". Click "OK" then "Apply" to finish this step.

To use this admin account, sign out of your current Windows account on the VM and login using your recently created admin username and password.

Phase 6 - Installing RAS/NAT

To install this feature, go back to the Server Manager dashboard click "Add roles and features" again. On the following screens click Next->Next-> (Select your server) Next->Select "Remote Access"->Next->Next->Select "Routing", Next->Next->Next->Install.

After the installation is done navigate to Server Manager Dashboard->Tools
->Routing and Remote Access->Right-click DC (local) (This name will vary based on your PC's name)->Configure and enable routing and remote access->Next-> Select Network address translation (NAT) option->Use this public interface to connect to the internet and select *_Internet_* interface->Finish.

Phase 7 - Setup A DHCP Server

Now we'll create DHCP server that allows computers on our private network browse and access the internet.

While on the Server Manager dashboard select Add Roles and Features->Next->Select your Server->Select "DHCP Server" and "Add Features"-> Next-> Next-> Next ->Install.

Now go to your Windows search bar and search "DHCP" and open the app for the next steps.

On the left side panel, you'll see your PC and when you expand it you'll also see IPV4 & IPV6 tabs with red X's on them. We'll add a DHCP scope option to IPV4. First right-click IPV4->Next->Assign a scope name of 172.16.0.100-200->Assign Start IP Address as 172.16.0.100->Also Assign End IP Adress 172.16.0.200->Lastly assign length to 24->Next->Next->Next
->Assign IP Adress 172.16.0.1->Click "ADD" *Important*->Next->Next->Next->Finish.

Now we need to authorize the DHCP scope by right-clicking your domain controller->Authorize->Refresh. Then you'll see your IPV4 & IPV6 icons turn green.

One quick setting config we should disable for this homelab is on the Server manager dashboard. Navigate to dashboard->local server (left side of the panel)->IE Security Configurations-> Select "off" for both Administrators and users. This should only be done in this lab because it allows for more online browsing flexibility.

Next is to go to the "Server Manager" go to Tools->DHCP->IPV4->Server options->(Right-Click) Configure Options-> Enable 003 Router-> At the bottom in the IP Address field add 172.16.0.1-> Click Add-> Click Apply.

Right click the server and restart the server one more time to see your newly configure settings.

Phase 8 - Automating User Creation with PowerShell

First log out of your Windows Server VM and log back in as the recently created admin account. Do this by clicking "Other User" on the bottom left of the login screen and enter your credentials.

Next, we'll be downloading a ZIP folder containing a power shell script.
https://github.com/joshmadakor1/AD_PS/archive/master.zip
Copy and paste this link into your VM's Internet Explorer and save the download to the desktop. 

Unzip the AD_PS-master zip folder and extract this folder with the same name onto your desktop.

Open this new folder and open the "names" file and add your name to the top and save the file. 

Exit and run the "Windows PowerShell ISE" app as an administrator 

Select open file at the top of Powershell ISE and open the ps1 script named "Create_Users" and type out this command to unrestrict security features ->
Set-ExecutionPolicy Unrestricted. *Only run a command like this for the lab environment it's not recommended to do this in the real world*.

Then run this script from inside your Administrator directory 
->cd C:\users\a-bgonzalez\Desktop\AD_PS-master. Once you're in this directory in PowerShell you can press play at the top of window to run the script to create the users for the directory.

After the script is done creating the new users you can leave this VM running because we need the DHCP service running for the Windows 10 VM setup to work

Phase 9 - Creating a Windows 10 Pro VM

Head back to Virtual Box and create another VM using the Windows 10 ISO and name it something basic like "Client1". You can check "Skip Unattended Installation" and choose the default memory, cores, and storage to create the VM.

Launch the VM and you can choose default settings for everything except for the version type and choose Windows 10 Pro and do a custom install where you'll use your virtual hard drive.

When you're asked for a connection just click "I dont have internet"

In the next few windows you'll be asked to create an account. You should create a limited local account. You shouldn't be creating a Microsoft account.  Then go next and then you'll be logged into your Windows 10 VM.

First you should open up the command line program and use the "ipconfig" command to see if your Windows 10 VM was assigned a default gateway.

You may have to type the "ipconfig /renew" command to see your default gateway. 

Phase 10 - Change Windows 10 Device Name

Right click your Windows icon on the bottom left and go to System->Rename this PC(Advanced) (*scroll down to find this)->Change->Change computer name to Client1 or whatever name you originally chose for this VM

Next important step is to join this device to the DC domain. Under "Member of" on the same window select "Domain" and add your domain that you created on your DC. Mine is opsecwithbryant.com and click OK. 

You'll be asked to enter your Domain admin account that you created. Ex- bgonzalez + yourpassword.

From there you've assigned this device to the domain and you'll be asked to reset your PC. This will complete the lab, congrats!

</p>

<!--
 ```diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@
```
--!>
