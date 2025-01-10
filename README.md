<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

# <h1>On-premises Active Directory Deployed in the Cloud (Azure)</h1>
This tutorial outlines the implementation of on-premises Active Directory within Azure Virtual Machines.<br />



## <h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

## <h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (21H2)

## <h2>High-Level Deployment and Configuration Steps</h2>

- Setup environment in Azure
- Deply Active Directory

## <h2>Deployment and Configuration Steps</h2>

###<h3>Setting Up the Environment in MS Azure</h3>

- Create a Resource Group (RG) called AD-Lab5
  - Click Review+Create then Create

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />

- Create a Virtual Network and Subnet
  - Go to All Services>Virtual Networks
    - Name: AD-Vnet
  - Click Review+Create then Create

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />

- Create Domain Controller VM
  - Go to Vitrual Machines
    - Create a VM named DC-1
      - Assign it to the RG AD-Lab5
      - Ensure it is assigned the same ragion as th RG
      - Image= WinServer 2022 with minimum of 2vCPUs
      - Set an Admin UN and password
      - Click Next>Disk>Next>Networking
        - Select Virtual Network = AD-Vnet
        - Click Review+Create then Create

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />

- Create a Client VM named Client-1
  - Assign it to the RG AD-Lab5 and ensure it is in the same region
  - Select the image as Win10 Pro
  - Set an Admin UN and password
  - Click Next>Disk>Next>Networking
    - Select Virtual Network = AD-Vnet
    - Click Review+Create then Create

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />

- Set DC-1's private IP to static
  - Go to DC-1>Network Settings
    - Click Network Interface/IPconfig
      - Set to Static then Save

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />

- Disable DC-1's firewall (For connectivity testing in the lab environment only)
  - Login to DC-1 with Remote Desktop (RDP)
    - In Windows Search type: wf.msc
    - Click Windows Defender Firewall Properties
    - Set the Domain, Provate, and Public Profiles to Off
    - Click Apply and Ok

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />

- Set Client-1 DNS to DC-1's private IP
  - Get the DC-1 Private IP from Azure
    - Go to Client-1>Network Settings>DNS Servers in Azure
      - Check the Custom box and type or paste the private IP then click Save
  - Still in Azure, go to Virtual Machines
    - Select Client-1 and Restart

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />

  - RDP to Client-1
    - Open Powershell and ping DC-1' private IP
    - Observe ping results
    - Run ipcofig /all in powershell
      - Note the DNs Server IP under ethernet adapter ethernet

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />

### <h3>Deploying Active Directory (AD)</h3>

- RDP to DC-1
  - Start Menu>Server Manager
    - Click "Add Roles and Features"
      - Click Next twice
      - Verify DC-1 as server
      - Click Next
      - Under Server Roles check the box for Active Directory Domain Services.
      - Under Features click Add Features
      - Click Next and then Next again until confirmation
        - Check "Restart if required"
        - Click "Yes" on popup then click Install
    - Click the notification at the top of the Server Manager Dashboard
      - Click "Promote Server to Domain Controller"
        - In the Wizard that comes up
          - Click "Add New Forest"
            - Enter: mydomain.com
          - When prompted, create a password. This won't be used but is required. Keep it simple and make a note, just in case.
          - Click Next and uncheck the DNS Delegation box then click Next again
          - Click Next for the Prerequisites Check
          - Click Install when the check is finished (Install button no longer grayed out)
          - After installation the VM will restart and you will have to relogin
          - After restart and relogin, go to Start>User>Change Settings
            - Verify mydomain\username as login
- Create Domain Admin User in DC-1
  - Go to Start>Active Directory Users and Computers (ADUC)
    - Right Click mydomain.com
      - From the flyout select New>Orgainizational Unit (OU)
        - Name: _EMPLOYEES (Do not forget the underscore)
        - Repeat and add a second named: _ADMINS
          - Right click _ADMINS>New>User
            - Name: Jane Doe
            - username: jane_admin
            - password: Cyberlab123!
            - uncheck "change password at login"
            - Click "Finish"
            - Right click Jane Doe>Properties
              - On the "Member Of" Tab click "Add"
              - Type "domain admins" in the object name field.
              - Click the "Check Names" button to find the established group.
              - Click Ok>Apply>Ok
              - The account is now elevated to Domain Admin.
                - RDP to DC-1 with the new credentials to test.
                  - Format will be: mydomain\jane_admin
- Join Client-1 to domain
  - RDP to Client-1 with credentials created in Azure
    - Right click Start>System
      - On the right side of windows choose "Rename this PC (Advanced)
        - Click Change under Computer Name Tab.
          -  Check the Domain checkbox and type "mydomain.com" in the formfield.
          -  Click then restart the VM in Azure.
-  In DC-1 open the ADUC
  - Expand mydomain.com
  - Select computers and verify client-1 is there.
  - Create new OU named _CLIENTS
  - Drag Client-1 from Computers to _CLIENTS
- Setup RDP for non-admin users in Client-1
  - Login to Client-1 as jane_admin
  - Right click Start>Sysytem>RDP (on right side)
  - Under User Acoounts click "Select users that can remotely access this PC"
  - Click Add in the new window and type "domain users" in the textfield.
  - Check name and click ok twice.
  - Non-admin users can now login to client-1
    - Note: Normally, this is done with group policy which allows multiple systems to be configured at once.
       
<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />
<h3>Creating Users with a Script</h3>

- In DC-1 login as jane_admin
- Open Powershell ISE as an administrator.
- Go to the [script link](https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1)
  - Click copy raw file
- In Powershell ISE click File>New
  - Paste the copied raw file into the new window in Powershell
    - Note: The script creates 10,000 users but can be edited for less. Remove one zero and it's more manageable. Also take note of the user assigned password at the top of the script.
    - The script relies on the _EMPLOYEES folder so ensure it was named correctly.
  - Save the script as "creat-users" to desktop then click the "Run Script" button in Powershell ISE
- In ADUC, check the _EMPLOYEES folder for the generated users
- Choose a random account and login to Client-1 with it
<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />
<h3>Group Policy and Managing Accounts</h3>

- Login to DC-1 as jane_admin
- Open the Group Policy Manager
  - Start>Search>gpmc.msc
  - Expand mydomain.com
    - Expand the Group Policy Objects folder
      - Right click Default Domain Policy and choose Edit to open the Group Policy Management Editor (GPME)
        - Configure the Account Lockout Policy by double clicking
          - Check the box "Define this policy setting"
          - Set duration to 30 minutes and click Apply>Ok
            - Suggested value changes will popup for other attributes including threshold for invalid logins and reset lockout timer
              - Accept these values or set custom ones.
- The new policy will propagate on its own but can be forced so as not to wait
  - Open Powershell and type gpupdate /force and hit enter
  - Verify the update by closing an reopening gpmc.msc and checking the Default Domain Policy settings
- Pick a random user and attempt to login to Client-1 6 times to trigger the new policy
  - Note the message at the 6th bad attempt
- Right click on the user's name in AD
  - Go to the Account Tab and note that the account is lock
  - Check the unlock box and login to Client-1 with the correct password
  - Logout of Client-1
  - Right click>Disable the same account in AD
  - Attempt to login (note the error message for a disabled account)
  - Reenable tha account in AD and verify that you can successfully login

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />
