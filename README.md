<div align=center>
  <img src="Active-directory.png" alt="Microsoft Active Directory Logo" width="600">
</div>

<br>

# Active Directory Lab on Azure

This project showcases the setup of a Windows Server Active Directory environment on Microsoft Azure. It involves deploying a Domain Controller (DC) and a Client (PC), joining the client to the domain, creating user accounts, and ensuring the user accounts can log on successfully with proper connectivity and remote access.

## Technologies Used
- Microsoft Azure
- Windows Server 2019
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

## Setup Steps

### Setting Up Resources in Azure

#### Create Domain Controller VM
- **Name**: `DC-1`
- **OS**: Windows Server 2022
- **Resource Group**: Create or use an existing one
- **Virtual Network (VNet)**: Create a VNet (e.g., `10.0.0.0/16`)

#### Configure Network Settings
- **Set Static IP for DC-1**:
  1. Go to Azure Portal > Virtual Machines > DC-1 > Networking > Network Interface.
  2. Click the network interface name under **Network Interface** settings.
  3. Navigate to **IP configurations** and select the IP configuration.
  4. Change **Assignment** to Static and save.

#### Create Client VM
- **Name**: `Client-1`
- **OS**: Windows 10
- **Use same Resource Group and VNet** as DC-1
- **Verify Network**:
  - Use **Azure Network Watcher** to ensure both VMs are in the same subnet and can communicate.

---

### Verifying Network Connectivity

- Enable ICMP (ping) for both hosts:
  ```powershell
  New-NetFirewallRule -DisplayName "Allow ICMPv4-In" -Protocol ICMPv4 -IcmpType 8 -Direction Inbound -Action Allow
  ```
- Test connectivity:
  - From DC-1, ping Client-1.
  - From Client-1, ping DC-1.

---

### Set Up Active Directory

#### Install Active Directory Domain Services (AD DS)
```powershell
Install-WindowsFeature -Name AD-Domain-Services -IncludeManagementTools
```

#### Promote DC-1 to a Domain Controller
```powershell
Install-ADDSForest -DomainName "lab.yourdomain.com"
```
- Replace `lab.yourdomain.com` with a subdomain of your registered domain.

> 📝 **Note**: It's best practice to create subdomains (e.g., `lab.yourdomain.com`) from your registered domain for internal use, as recommended by Microsoft and industry standards.

#### Create a Domain Admin User
```powershell
New-ADUser -Name "LabAdmin" -SamAccountName "LabAdmin" -AccountPassword (ConvertTo-SecureString "YourSecurePassword123" -AsPlainText -Force) -Enabled $true
Add-ADGroupMember -Identity "Domain Admins" -Members "LabAdmin"
```

---

### Joining Client-1 to the Domain

#### DNS Configuration
- **Update Client-1’s DNS Settings in Azure Portal**:
  1. Go to Azure Portal > Client-1 > Networking > Network Interface.
  2. Select **IP configurations**, click on the IP config.
  3. Under **DNS servers**, choose **Custom**.
  4. Enter DC-1’s private IP (e.g., `10.0.0.4`), then save.

- **Verify DNS Setting on Client-1**:
  ```cmd
  nslookup lab.yourdomain.com
  ipconfig /all
  ```

#### Join Domain Using Admin Account
1. Log in as local admin.
2. Navigate to **System > About > Rename this PC (advanced)**.
3. Click **Change**, select **Domain**, enter domain (e.g., `lab.yourdomain.com`).
4. Provide credentials of `LabAdmin` account.
5. Reboot.

#### Verify Domain Membership
- After reboot, login as `LabAdmin` from domain.
- Confirm successful domain membership via:
  ```cmd
  systeminfo | findstr /B /C:"Domain"
  ```

---

### Configuring Remote Desktop Access

- Enable Remote Desktop on Client-1.
- Allow connections from any version of RDP.
- Add standard users to **Remote Desktop Users** group:
  ```powershell
  Add-LocalGroupMember -Group "Remote Desktop Users" -Member "Domain\\username"
  ```
- Open required RDP ports in Azure NSG (e.g., port 3389).

---

### Creating and Testing Standard User Accounts

#### Create Standard Users with PowerShell
```powershell
New-ADUser -Name "John Doe" -SamAccountName "jdoe" -AccountPassword (ConvertTo-SecureString "UserPassword123" -AsPlainText -Force) -Enabled $true
```

#### Test Login from Remote
- Login to `Client-1` using RDP as `jdoe@lab.yourdomain.com`.
- Verify login is successful and user environment is loaded.

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

