# cabal_homelab
On-premises Active Directory environment built via VirtualBox simulating a corporate enterprise network.

# On-Premises Active Directory Home Lab

## Project Overview
This project demonstrates the deployment, configuration, and management of a localized enterprise network using virtualization software. The lab simulates a realistic corporate environment featuring a dedicated Windows Server Domain Controller managing localized endpoints and user access.

## Skills Demonstrated
* Virtual Network Architecture & Isolation
* Windows Server 2022 Installation & Configuration
* Active Directory Domain Services (AD DS) Deployment
* DNS & DHCP Network Core Services Administration
* Enterprise Endpoint Domain Join Management

## Tools & Infrastructure Used
* **Hypervisor**: Microsoft Hyper-V
* **Directory Server**: Windows Server 2022 (Evaluation ISO)
* **Client Endpoint**: Windows 11 Pro
* **Virtual Networking**: VirtualBox Isolated Internal Network (`CasLabNetwork`)

## Step-by-Step Implementation

### Step 1: Hypervisor & Network Isolation
* Enabled Hyper-V on the host machine.
* Created an isolated internal virtual network named `CasLabNetwork` to safely conduct network management without interfering with the home router. <img width="613" height="160" alt="setup_internal_network" src="https://github.com/user-attachments/assets/38802c32-7cbb-48cd-8cda-748f1430f9a6" />


### Step 2: Domain Controller Installation & Deployment
* Provisioned a virtual machine utilizing the Windows Server 2022 ISO.
* Renamed the server to AD-DC-TXHQ.
 <img width="748" height="581" alt="ServerName_IPadd" src="https://github.com/user-attachments/assets/d2a4f28b-90df-44a3-8bd5-b91b00803f37" />

 
* Configured a static IP address (`192.168.10.10`) and configured DNS to point to itself to prepare it for the role as primary DNS.
  <img width="400" height="457" alt="image" src="https://github.com/user-attachments/assets/4c9a5352-db8b-4694-87b7-d19b6868bbd0" />

  
* Promoted the server to a Domain Controller, establishing the root domain: `TXHQ\Administrator`.
  <img width="1014" height="830" alt="image" src="https://github.com/user-attachments/assets/f14244f8-7bc7-41ee-a824-e108f55d6732" />
<img width="470" height="357" alt="image" src="https://github.com/user-attachments/assets/f2fc9959-f4a4-448b-bf68-bc4c2e790e91" />

 
* Created first enterprise user.
<img width="556" height="269" alt="image" src="https://github.com/user-attachments/assets/9cca5c5b-99d0-48f5-a868-87ed355962fd" />


* Installed and configured **DHCP** (to assign dynamic IPs to clients) and **DNS** (for domain name resolution). Setup ip address pool to hand out to client computers.
  <img width="544" height="298" alt="image" src="https://github.com/user-attachments/assets/f241c1db-125f-4f27-82ff-eb6f480e81d2" />

  <img width="371" height="264" alt="image" src="https://github.com/user-attachments/assets/7d03bb58-253e-4e03-9564-bd9380f8f6ab" />

### Step 3: User Provisioning and Organization
* Created specific Organizational Units (OUs) namely Management, IT-Staff, and Sales. Populated the OUs with test employee accounts and configured for users to change password at next logon.
  <img width="660" height="702" alt="image" src="https://github.com/user-attachments/assets/8f4992c5-5328-4990-96e3-161bbb341ccf" />

### Step 4: Endpoint Management & Domain Join
* Provisioned a secondary VM running Windows 11 Enterprise.
  <img width="536" height="106" alt="image" src="https://github.com/user-attachments/assets/21e6fcf1-bd57-4208-bfb0-f7e82a9045f0" />

* Modified the client's network adapter to look at `CasLabNetwork` and pointed its primary DNS to the Domain Controller IP. DHCP server also assigned ip address to client computer within the ip address pool(192.168.10.50 to 192.168.10.200) I set up.
  <img width="760" height="614" alt="ipAddress_assigned_desktop" src="https://github.com/user-attachments/assets/2e2406c2-b794-4e6c-8240-cdd7eb84be07" />

* Successfully joined the client `ccabal` to the `txhq.local` domain.
  <img width="696" height="541" alt="image" src="https://github.com/user-attachments/assets/84d0603b-963a-4041-89a1-f58cb55fd7e8" />
  <img width="640" height="485" alt="confirmed_domain_client" src="https://github.com/user-attachments/assets/c1a368ec-5fca-46a8-8220-7946c9b93758" />



  ## Help Desk Ticket Simulations (Hands-On Lab Tasks)

  ### 1. User Onboarding Workflow
* **Objective**: Processed a standard HR hiring ticket to provision a new network identity.
* **Action**: Logged into the workstation using the restricted `adm-ccabal` account. Provisioned a new user profile for employee "Jane Doe" with a standardized corporate UPN (`jdoe@txhq.local`). 

















  

  







