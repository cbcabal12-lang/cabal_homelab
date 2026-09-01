# Casimelito Cabal homelab
Technologies shown in this lab using Microsoft Hyper-V virtualization simulating a enterprise network:
* On-premises Windows server 2022 Active Directory Domain Services (DHCP, DNS, Users&Computers)
* Firewall & VPN (pfsense)
* Linux server (Ubuntu server)
* Windows 11 client domain joined
* ServiceNow Helpdesk ticketing system
* Small Office Home Office(SOHO) Network

## On-Premises Active Directory Home Lab

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
* **Virtual Networking**: Hyper-V Isolated Internal Network (`CasLabNetwork`)

## Step-by-Step Implementation

### Step 1: Hypervisor & Network Isolation
* Enabled Hyper-V on the host machine.
* Created an isolated internal virtual network named `CasLabNetwork` to safely conduct network management without interfering with the home router.
* <img width="613" height="160" alt="setup_internal_network" src="https://github.com/user-attachments/assets/38802c32-7cbb-48cd-8cda-748f1430f9a6" />


### Step 2: Domain Controller Installation & Deployment
* Provisioned a virtual machine utilizing the Windows Server 2022 ISO.
* Renamed the server to AD-DC-TXHQ.
* <img width="748" height="581" alt="ServerName_IPadd" src="https://github.com/user-attachments/assets/d2a4f28b-90df-44a3-8bd5-b91b00803f37" />

 
* Configured a static IP address (`192.168.10.10`) and configured DNS to point to itself to prepare it for the role as primary DNS.
* <img width="400" height="457" alt="image" src="https://github.com/user-attachments/assets/4c9a5352-db8b-4694-87b7-d19b6868bbd0" />

  
* Promoted the server to a Domain Controller, establishing the root domain: `TXHQ\Administrator`.
* <img width="1014" height="830" alt="image" src="https://github.com/user-attachments/assets/f14244f8-7bc7-41ee-a824-e108f55d6732" /> <img width="470" height="357" alt="image" src="https://github.com/user-attachments/assets/f2fc9959-f4a4-448b-bf68-bc4c2e790e91" />

 
* Created first enterprise user.
* <img width="556" height="269" alt="image" src="https://github.com/user-attachments/assets/9cca5c5b-99d0-48f5-a868-87ed355962fd" />


* Installed and configured **DHCP** (to assign dynamic IPs to clients) and **DNS** (for domain name resolution). Setup ip address pool to hand out to client computers.
* <img width="544" height="298" alt="image" src="https://github.com/user-attachments/assets/f241c1db-125f-4f27-82ff-eb6f480e81d2" /> <img width="371" height="264" alt="image" src="https://github.com/user-attachments/assets/7d03bb58-253e-4e03-9564-bd9380f8f6ab" />

### Step 3: User Provisioning and Organization
* Created specific Organizational Units (OUs) namely Management, IT-Staff, and Sales. Populated the OUs with test employee accounts and configured for users to change password at next logon.
* <img width="660" height="702" alt="image" src="https://github.com/user-attachments/assets/8f4992c5-5328-4990-96e3-161bbb341ccf" />

### Step 4: Endpoint Management & Domain Join
* Provisioned a secondary VM running Windows 11 Enterprise.
* <img width="536" height="106" alt="image" src="https://github.com/user-attachments/assets/21e6fcf1-bd57-4208-bfb0-f7e82a9045f0" />

* Modified the client's network adapter to look at `CasLabNetwork` and pointed its primary DNS to the Domain Controller IP. DHCP server also assigned ip address to client computer within the ip address pool(192.168.10.50 to 192.168.10.200) I set up.
* <img width="760" height="614" alt="ipAddress_assigned_desktop" src="https://github.com/user-attachments/assets/2e2406c2-b794-4e6c-8240-cdd7eb84be07" />

* Successfully joined the client `ccabal` to the `txhq.local` domain.
* <img width="696" height="541" alt="image" src="https://github.com/user-attachments/assets/84d0603b-963a-4041-89a1-f58cb55fd7e8" /> <img width="640" height="485" alt="confirmed_domain_client" src="https://github.com/user-attachments/assets/c1a368ec-5fca-46a8-8220-7946c9b93758" />

* ServiceNow ticket created
* <img width="2467" height="818" alt="image" src="https://github.com/user-attachments/assets/3616aa2a-4e6a-4eeb-838f-5d0e2538f993" />


  ## Security Infrastructure & Least Privilege Verification

### 1. Tier-1 Help Desk Role Customization
* **Objective**: Restrict Help Desk capabilities to match industry-standard entry-level permissions, preventing unauthorized directory modifications.
* **Implementation**: Modified the **Delegation of Control Wizard** permissions on the `HQ-Users` parent Organizational Unit. Granted the `HelpDesk-Team` group explicit authority **only** to execute password resets and force password changes at next logon, while completely stripping account creation and deletion capabilities.
* <img width="903" height="634" alt="image" src="https://github.com/user-attachments/assets/19c87f10-7705-433d-83cb-adac1a13099f" />


### 2. RSAT(Remote Server Administration Tools) Deployment Workaround (The Jump Box Method)
* **Technical Hurdle**: Due to the secure, air-gapped isolation of the laboratory network from the public internet, local native installation of RSAT on the Windows 11 Enterprise client installation failed.
* **Enterprise Solution**: Pivoted to an industry-standard secure engineering workflow by transforming the Windows Server Domain Controller into a restricted administrative **Management Jump Box**. 
* **Access Configuration**:
  1. Configured the server's local security properties to accept incoming connections via **Remote Desktop Protocol (RDP)** explicitly from the `HelpDesk-Team` security group.
  2. Modified the **Default Domain Controllers Group Policy Object (GPO)** under `User Rights Assignment`, granting the help desk group the specific right to *Allow log on through Remote Desktop Services*.
  3. Executed `gpupdate /force` via command-line utilities to instantly commit network-wide policy changes.

### 3. Verification & Access Control Audit
* **Test Path**: Initiated an RDP connection from the Windows 11 Client endpoint to the server host destination (`192.168.10.10`) authenticating as the restricted help desk technician profile.
* **Results**: 
  * The technician successfully initiated a remote administrative session and accessed the directory management console.
  * <img width="1000" height="794" alt="remoteDesktoptoServer" src="https://github.com/user-attachments/assets/24b2aaed-6515-4cab-8bcb-46908a1cfe72" />

  * The technician successfully reset the password of user `Mike Jackson`
  * <img width="876" height="650" alt="passwordReset" src="https://github.com/user-attachments/assets/8985d843-1cc5-4812-9582-7b6a16e977b6" />

  * **Security Enforcement Test**: Attempting to execute an unauthorized administrative action (such as deleting an asset folder or user account) triggers an instant directory-level blocking event.
  * <img width="878" height="666" alt="accessDenied" src="https://github.com/user-attachments/assets/170f5d60-5fc0-458b-8384-d93491b1b590" />

  * ServiceNow Ticket
  * <img width="1915" height="879" alt="image" src="https://github.com/user-attachments/assets/3b7cb62d-bd83-4a26-8d4f-ea49ff2ed453" />


## Enterprise Hybrid Network & Remote Access VPN Lab

## Project Overview
This project simulates a secure corporate infrastructure allowing remote employees (e.g., traveling on insecure hotel Wi-Fi) to establish an encrypted tunnel back to an internal corporate network using an enterprise gateway firewall and Active Directory authentication.

## Network Topology & Architecture
* **Corporate LAN Subnet**: `192.168.10.1`
* **Simulated Internet Transit (DMZ)**: `CoffeeNetwork`
* **Edge Firewall/Gateway Appliance**: pfSense CE 2.7.2 (FreeBSD 14.0 Core)
* **Directory Services/Identity Provider**: Windows Server 2022 Active Directory

## Phase 1: Firewall Implementation
* Virtualized a Generation 2 UEFI virtual machine with 1 vCPU and 1024MB Static RAM.
* Disabled Microsoft Secure Boot signatures to allow clean execution of the FreeBSD kernel bootloader.
* Mapped physical Hyper-V hardware interrupts sequentially to match the OS networking stack interface mapping logic (`hn0` mapped to WAN, `hn1` mapped to LAN).
*
* <img width="1014" height="864" alt="image" src="https://github.com/user-attachments/assets/969b4063-ffdd-46e3-96c7-69200633a071" />

* Screenshot of pfsense/firewall web gui configured successfully.
* <img width="1012" height="837" alt="pfsenseWebgui" src="https://github.com/user-attachments/assets/043bae5b-2485-4f80-85f4-1e98cac170e3" />


## 🔹 Phase 2: Perimeter Security & Remote Access VPN
*   **Objective**: Secure the corporate perimeter and provide a hardened, encrypted tunnel for traveling employees using an enterprise gateway.
*   **Firewall Appliance**: pfSense CE 2.7.2 (FreeBSD 14.0 platform).
*   **Network Isolation**: Engineered two independent Hyper-V virtual networks (`labnetwork` and `CoffeeNetwork`) to simulate real-world internet isolation.
* Implemented Role-Based Access Control (RBAC) by creating a centralized VPN-Users Global Security Group within Active Directory to govern remote network entry permissions. Added user `Mike Jackson` as VPN user.
* <img width="654" height="511" alt="vpnUsersGroup" src="https://github.com/user-attachments/assets/5d10b29d-839d-48d1-b045-8bda84aafcfb" />

* Pfsense successfully authenticated user `Mike Jackson`.
* <img width="595" height="587" alt="pfsenseAuthSuccess" src="https://github.com/user-attachments/assets/7f72a6e6-bceb-48a7-b749-7b301b421b75" />

## 🧪 Phase 2.5: Operational Baseline & Negative Testing
Before establishing the cryptographic tunnel framework, a negative validation test was executed to confirm network isolation boundaries.

* **Client State**: Workstation relocated to `CoffeeNetwork` transit environment (`203.0.113.50`).
* **Isolation Verification**: Direct packet routing to the internal subnet (`192.168.10.10`) was dropped.
* **Perimeter Hardening Verification**: ICMP Echo Requests targeting the edge WAN gateway interface (`203.0.113.1`) were cleanly dropped by default pfSense block policies.
* <img width="597" height="543" alt="pfsenseBlockPing" src="https://github.com/user-attachments/assets/a1e997ca-1ea9-4b30-8965-22ee0469ac36" />

## Phase 3: Setting up OpenVPN
### ⚙️ Objective
Deploy a highly secure, production-grade OpenVPN Gateway server directly on the edge firewall. This architecture establishes an encrypted transport tunnel across untrusted, isolated public address spaces, leveraging the centralized Active Directory database for live user validation.

### 🛠️ Tunnel & Cryptographic Specifications
* **VPN Daemon Service**: OpenVPN Community Engine (Bound to WAN interface `203.0.113.1`)
* **Transport Protocol & Socket**: UDP over Port 1194 (Industry baseline for optimized low-latency data transit)
* **Cryptographic Cipher Suite**: AES-256-GCM data encryption with SHA256 integrity hashing
* **Key Exchange Matrix**: Diffie-Hellman Group 14 (2048-bit prime modular exponentiation)
* **Virtual Tunnel Subnet Allocation**: `10.0.8.0/24` (Isolated routing scope reserved strictly for tunneled remote network endpoints)
* **Internal Routing Access Layer**: Explicit injection rules pointing to the Corporate LAN backend (`192.168.10.0/24`)
* OpenVPN connection successfull
* <img width="586" height="568" alt="image" src="https://github.com/user-attachments/assets/db327200-baed-4c1f-adc3-57580e44ae3f" />

* Showing client ip address and submit. Showing successful ping to `192.168.10.10` Domain Controller. 
* <img width="629" height="735" alt="image" src="https://github.com/user-attachments/assets/cfb6095d-9a72-45f2-ab36-419ac60f7f3a" />

+ ServiceNow ticket for this project
+ <img width="1907" height="934" alt="image" src="https://github.com/user-attachments/assets/f9d416cb-23c7-4309-bf70-f55725c4d6d7" />


## Small Office Home Office(SOHO) Network
* Scenario: Client wants a secure network for their new business. They have 4 desktop, 1 laptop, 1 Smartphone, 1 Wireless IP camera, 1 Smart TV, 1 Wireless AI Assistant, 1 Wireless Doorbell, 1 network printer, and they also want a Wi-Fi for the guest.
* <img width="1141" height="944" alt="image" src="https://github.com/user-attachments/assets/ac03bd5e-a93b-4a5d-9025-3846c299e884" />


  
























































  

  







