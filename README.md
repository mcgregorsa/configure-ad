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

- Step 1
- Step 2
- Step 3
- Step 4

<h2>Deployment and Configuration Steps</h2>
<h3>Setting Up the Environment in MS Azure</h3>

- Create a Resource Group (RG) called AD-Lab5
  - Click Review+Create then Create
- Create a Virtual Network and Subnet
  - Go to All Services>Virtual Networks
    - Name: AD-Vnet
  - Click Review+Create then Create
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
- Create a Client VM named Client-1
  - Assign it to the RG AD-Lab5 and ensure it is in the same region
  - Select the image as Win10 Pro
  - Set an Admin UN and password
  - Click Next>Disk>Next>Networking
    - Select Virtual Network = AD-Vnet
    - Click Review+Create then Create
- Set DC-1's private IP to static
  - Go to DC-1>Network Settings
    - Click Network Interface/IPconfig
      - Set to Static then Save
- Disable DC-1's firewall (For connectivity testing in the lab environment only)
  - Login to DC-1 with Remote Desktop (RDP)
    - In Windows Search type: wf.msc
    - Click Windows Defender Firewall Properties
    - Set the Domain, Provate, and Public Profiles to Off
    - Click Apply and Ok
- Set Client-1 DNS to DC-1's private IP
  - Get the DC-1 Private IP from Azure
    - Go to Client-1>Network Settings>DNS Servers in Azure
      - Check the Custom box and type or paste the private IP then click Save
  - Still in Azure, go to Virtual Machines
    - Select Client-1 and Restart
  - RDP to Client-1
    - Open Powershell and ping DC-1' private IP
    - Observe ping results
    - Run ipcofig /all in powershell
      - Note the DNs Server IP under ethernet adapter ethernet
<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />
<h3>Deploying Active Directory (AD)</h3>

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

     
<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />

<p>
Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.
</p>

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />

<p>
Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.
</p>

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />
