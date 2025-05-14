# 🖥️ Active Directory Lab on Azure

This project demonstrates how to set up a Windows Server Active Directory environment using Microsoft Azure. The lab involves provisioning a Domain Controller (DC), joining a client PC to the domain, configuring DNS, applying Group Policies, and testing folder sharing and security features.

---

## 🌐 Lab Overview

- **Cloud Platform:** Microsoft Azure  
- **Operating Systems:**
  - Windows Server 2019 (Domain Controller)
  - Windows 10 Pro (Client PC)
- **Domain Name:** `af.midroam.com`
- **Region:** South Africa North
- **Architecture:**  
  - Virtual Network: `10.0.0.0/16`
  - DC-1 Private IP: `10.0.0.X`
  - Client-1 Private IP: `10.0.0.Y`

---

## 🛠️ Features Implemented

- Created and promoted a Windows Server VM as a Domain Controller
- Installed and configured Active Directory Domain Services (AD DS)
- Configured DNS to point to DC-1
- Joined a Windows 10 client to the domain
- Created Organizational Units (OUs) and users
- Applied and verified Group Policies (GPOs)
  - Disabled “Add or Remove Programs” for employees
  - Enabled screen saver with lock
- Configured folder sharing with permissions
- Verified access using domain credentials via RDP and shared folders

---

## 🗂️ Folder Structure

