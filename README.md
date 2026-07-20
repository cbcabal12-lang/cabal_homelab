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





