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

### 1. Setting Up Resources in Azure

#### Create Domain Controller VM
- **Name**: DC-1  
- **OS**: Windows Server 2022  
- Note the **Resource Group** and **Virtual Network (VNet)** created automatically.

#### Configure Network Settings
- Set **DC-1’s NIC** Private IP address to **static**.

#### Create Client VM
- **Name**: Client-1  
- **OS**: Windows 10  
- Use the **same Resource Group and VNet** as DC-1.

#### Verify Network Placement
- Confirm both VMs are in the **same VNet** using **Azure Network Watcher**.

---

### 2. Verifying Connectivity
- Connect to **DC-1** via RDP.
- Ping **Client-1** from DC-1 and vice versa to confirm **network connectivity**.
- Disable **Windows Defender Firewall** temporarily if ping fails.

---

### 3. Installing Active Directory
- Rename DC-1’s hostname (if needed).
- Install **Active Directory Domain Services (AD DS)** role via Server Manager.
- Promote the server to a **Domain Controller**.
- Set up a **new forest** and root domain (e.g., `af.midroam.com`).
- Restart as prompted.

---

### 4. Creating an Additional Admin User
- Create a new user (e.g., `canu.xawid`) via **Active Directory Users and Computers (ADUC)**.
- Add this user to the **Domain Admins** group.
- Log in to DC-1 using the new admin user to verify administrative privileges.

---

### 5. Joining Client-1 to the Domain
- Log in to **Client-1** as local admin.
- Set the DNS address to point to **DC-1’s private IP**.
- Join the domain (`af.midroam.com`) via **System Properties > Domain**.
- Restart Client-1.

---

### 6. Configuring Remote Desktop Access
- Enable **Remote Desktop** on Client-1.
- Allow domain users to connect via **System Properties > Remote Settings**.
- Add domain users to **Remote Desktop Users** group if needed.

---

### 7. Creating Non-Administrative Users
- In ADUC, create additional users (e.g., `student1`, `student2`).
- Assign them to **Domain Users** group (default).
- Log in to Client-1 via RDP using each user to confirm successful logon.

---

> 💡 *Note: It's best practice to create subdomains (e.g., `lab.yourdomain.com`) from your registered domain for internal use, as recommended by Microsoft and industry standards.*

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

