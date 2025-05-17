<div align=center>
  <img src="Active-directory.png" alt="Microsoft Active Directory Logo" width="600">
</div>

<br>

# Active Directory Lab on Azure

This project showcases the setup of a Windows Server Active Directory environment on Microsoft Azure. It involves deploying a Domain Controller (DC) and a Client (PC), joining the client to the domain, creating user accounts, and ensuring the user accounts can log on successfully with proper connectivity and remote access.

## Technologies Used
- Microsoft Azure
- Windows Server 2022
- Windows 10 Pro
- Active Directory Domain Services
- PowerShell
- RDP

## Architecture
- **Virtual Network**: 10.0.0.0/16
- **DC-1**: Windows Server 2019 (Domain Controller)
- **Client-1**: Windows 10 Pro (Domain Joined)
<br>
<div align=center>
  <img src="Architecture.png" alt="DC Architecture" width="600">
</div>

---

## Setting Up Resources in Azure

1. **Create Domain Controller VM**
   - Name: `DC-1`
   - OS: Windows Server 2022
   - Create a new or use existing Resource Group and Virtual Network (VNet)
   - Ensure RDP (port 3389) is allowed in the NSG

2. **Set DC-1’s Private IP to Static**
   - Go to Azure Portal > DC-1 VM > Networking > Network Interface > IP Configurations
   - Set Private IP address assignment to **Static**
   - Save the configuration

3. **Create Client VM**
   - Name: `Client-1`
   - OS: Windows 10
   - Use the same Resource Group and VNet as DC-1
   - Confirm both VMs are in the same VNet (verify using Network Watcher)

---

## Verify Network Connectivity 
  - Enable ICMP on Both VMs by opening PowerShell as Administrator and running:
     ```powershell
     Enable-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)"
     ```
  - On DC-1, open Command Prompt and run: `ping <Client-1 IP>`
  - Verify that the ping replies are now successful, confirming network connectivity.

---

## Set Up Active Directory

1. **Install Active Directory Domain Services (AD DS)**
   - Confirm that DC-1’s private IP is configured as **Static**. (If not, refer back to **Step 2** in the **Setting Up Resources in Azure** section.)
   - Open PowerShell as Administrator on DC-1 and run:
     ```powershell
     Install-WindowsFeature -Name AD-Domain-Services -IncludeManagementTools
     ```

3. **Promote DC-1 to Domain Controller**
   - Run the following command to create a new forest:
     ```powershell
     Install-ADDSForest 
     ```
   - Specify a domain name of your choice (e.g., yourdomain.com).
   - When prompted, enter and confirm the password.
   - Type Y and press Enter to begin the promotion.

> 💡 *It's highly recommended to use a valid, registered domain name with a subdomain prefix (e.g., lab.yourdomain.com) for internal domain setups to avoid conflicts and ensure proper management.*

---

## Create a Domain Admin User

1. **Create Organizational Units (OUs)**
   - Open **Active Directory Users and Computers** (ADUC)
   - Create `_EMPLOYEES` OU
   - Create `_ADMINS` OU

2. **Create a Domain Admin User**
   - Right-click `_ADMINS` > New > User
   - Create a user (e.g., `labadmin`)
   - Set a secure password and check “Password never expires”

3. **Assign Administrative Rights**
   - Add the new user to **Domain Admins** group
   - Log out of the current session
   - Log in as the new domain admin user

---

## Joining Client-1 to the Domain

1. **DNS Configuration**
   - In Azure Portal > Client-1 VM > Networking > Network Interface > DNS servers
   - Set to **Custom DNS** and enter the static IP of DC-1

2. **Restart Client-1**
   - Go to Azure Portal > Client-1 VM > Restart

3. **Verify DNS Settings**
   - Open **Command Prompt** on Client-1
   - Run: `ipconfig /all`
   - Confirm DNS points to DC-1

4. **Join Client-1 to Domain**
   - Right-click Start > System > Rename this PC (Advanced)
   - Click “Change” and join the domain (e.g., `lab.yourdomain.com`)
   - Use domain admin credentials when prompted
   - Restart the machine

5. **Verify Domain Membership**
   - Login as the domain admin
   - Open **System Information** to confirm domain membership

---

## Configuring Remote Desktop Access

1. **Enable RDP Access for Standard Users**
   - On Client-1, right-click Start > System > Remote Desktop settings
   - Click “Select users that can remotely access this PC”
   - Add standard users to the list

---

## Creating and Testing Standard User Accounts

1. **Create Users via PowerShell**
   - Open PowerShell as domain admin on DC-1 and run:
     ```powershell
     Import-Module ActiveDirectory
     New-ADUser -Name "John Doe" -GivenName John -Surname Doe -SamAccountName jdoe -UserPrincipalName jdoe@lab.yourdomain.com -Path "OU=_EMPLOYEES,DC=lab,DC=yourdomain,DC=com" -AccountPassword (ConvertTo-SecureString "P@ssw0rd123" -AsPlainText -Force) -Enabled $true
     ```

2. **Verify Logon via Remote Desktop**
   - Use Remote Desktop Connection (RDP) to access Client-1
   - Login as the standard user (e.g., `jdoe@lab.yourdomain.com`)
   - Ensure successful login

---


## Screenshots

| Domain Join Success | GPO Result | Shared Folder Access |
|---------------------|------------|-----------------------|
| ![](images/domain-join-success.png) | ![](images/gpo-applied.png) | ![](images/shared-folder-access.png) |

---

## Lessons Learned

- Importance of DNS resolution in domain joining
- Troubleshooting Group Policy using `gpresult`
- Secure access and delegation using OUs and GPOs
- Differences between local and domain permissions

---

## Next Steps / Ideas for Improvement

- Add a second Domain Controller for redundancy
- Integrate Azure AD or hybrid configuration
- Automate entire setup using Terraform or ARM templates
- Explore user login scripts and software deployment via GPO

---

## Acknowledgments

This lab was inspired by real-world scenarios and Microsoft documentation on Active Directory and Azure VM networking.

---

## License

This project is open source and available under the [MIT License](LICENSE).

