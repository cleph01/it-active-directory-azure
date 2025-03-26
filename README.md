# Azure Active Directory Lab: Implementing On-Premises AD in the Cloud (Azure)

<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

## Project Overview

This project demonstrates the implementation of Active Directory (AD) within Azure Virtual Machines, showcasing a fundamental component of enterprise network management.

### What is Active Directory?

* Active Directory is a directory service developed by Microsoft.
* It centrally manages users, computers, and other network resources.
* It provides authentication, authorization, and policy management.
* It enables secure and efficient control over a network environment.

### Why is it Important?

* Centralized Management: Simplifies user and resource administration.
* Enhanced Security: Enforces security policies and controls access.
* Improved Efficiency: Streamlines network operations and resource sharing.

### Deployment in Azure

* Simulates a typical on-premises setup in a cloud environment.
* Highlights how organizations can leverage Azure's infrastructure for domain services.
* Allows for exploration of core AD concepts:
    * Domain creation
    * User management
    * Group policies
    * DNS integration

### Project Goal

To provide a practical understanding of how AD facilitates centralized network administration within an Azure environment.
<br />

<h2>Environments and Technologies Used</h2>

- Microsoft Azure
    - Virtual Machines/Compute
    - Virtual Network
    - Network Security Groups (Firewall)
- Active Directory Domain Services (AD DS)
- Remote Desktop
- DNS
- PowerShell

<h2>Operating Systems Used</h2>

- Windows Server 2022
- Windows 10 (21H2)

<h2>High-Level Deployment and Configuration Steps</h2>

1. Setup Infrastructure:
    * **Created an Azure Resource Group** to organize all project resources for simplified management.
    * **Configured a Virtual Network (VNet)** with a defined address space to establish a private network in Azure.
    * **Configured a Network Security Group (NSG)** to control inbound and outbound network traffic for security.
    * **Deployed a Windows Server 2022 Virtual Machine** to serve as the Active Directory Domain Controller.
    * **Deployed a Windows 10 Virtual Machine** to act as a domain-joined client for testing.

2. Configure Active Directory:
    * **Installed Active Directory Domain Services (AD DS) role** on the Windows Server 2022 VM.
    * **Promoted the Windows Server 2022 VM to a Domain Controller**, establishing the Active Directory domain.
    * **Configured the Domain Controller's IP address to be static** for reliable DNS and domain services.

3. Integrate Client Machine and User Management:
    * **Joined the Windows 10 client VM to the newly created Active Directory domain.**
    * **Configured the client VM's IPv4 DNS settings** to point to the Domain Controller's static private IP address, ensuring proper domain resolution.
    * **Created Organizational Units (OUs)** (_ADMINS, _EMPLOYEES, _CLIENT) to organize domain objects.
    * **Created domain user accounts** with varying permissions within the OUs.
    * **Created security groups** to manage user access to resources.
    * **Verified domain user authentication** by logging into the client VM with domain credentials.
    * **Verified basic Group Policy application** by testing Remote Desktop access and Password Lockout configuration.

<h2>Deployment and Configuration Steps</h2>

### 1. Create a Resource Group:

- **Created a new Azure Resource Group** named appropriately to contain all project-related Azure resources.

<p>
<img width="1118" alt="image" src="https://github.com/user-attachments/assets/29b297df-862f-4a0c-a9da-35ef2f72dd14" />
</p>

<p>
Azure Resource Groups are logical containers (like folders) that hold related Azure resources in one place. Resource Groups help us to organize our VMs, virtual network, and other components, simplifying deployment and management. This helps us manage everything together, like deploy, update, or delete all resources as a single unit. Using a Resource Group also makes it easier to keep track of costs and manage access control, streamlining our Azure environment.

</p>

<hr />
<br />

### 2. Establish Virtual Network (VNet) and Subnet:

- **Created a Virtual Network (VNet)** with the address space `10.0.0.0/16` to provide a private network for Azure resources.
- **Created a subnet** within the VNet with the address range `10.0.0.0/24` to segment the network and house the VMs.

<p>
<img width="854" alt="image" src="https://github.com/user-attachments/assets/2c17c14a-34c0-4a34-acda-6df84b2bb28b" />
</p>

<p>
We're choosing to manually create our virtual network in-lieu of the auto-creation that comes from creating a virtual machine.
</p>
<p>
A Virtual Network (VNet) is like creating your own private network in the cloud. We're setting up a VNet to allow our virtual machines to communicate with each other securely. We'll use the address space 10.0.0.0/16, which means we have 65,536 possible IP addresses for our network. Within this VNet, we'll create a subnet, 10.0.0.0/24, which is a smaller part of the VNet, providing 256 addresses. This subnet will house our VMs and help manage network traffic efficiently.
</p>

<hr />
<br />

### 3. Virtual Machine (VM) Deployment (Server - Windows Server 2022):

- **Deployed a Windows Server 2022 Virtual Machine** named `dc-1` within the created VNet and subnet.
- **Observed that a Network Security Group (NSG)** was automatically created by Azure for this VM to act as a virtual firewall.

<p>
<img width="970" alt="image" src="https://github.com/user-attachments/assets/e488a121-8630-4517-a71f-c7ee4bbc5df7" />
</p>

<p>
We're now creating our first virtual machine, a Windows Server 2022 instance, which will act as our domain controller. When creating a VM, Azure automatically assigns a Network Security Group (NSG) to it. Think of an NSG as a virtual firewall, controlling network traffic to and from the VM. This is not a dedicated firewall appliance, but a rule based access control list (ACL) assigned to the Network Interface Card of the virtual machine. For testing purposes, we've enabled RDP (port 3389) access during VM creation. Azure warns that by default, VMs are only accessible within their virtual network, but we've chosen to allow RDP traffic from the public internet. This allows us to remotely connect to the server and configure it.
</p>

<p>
<img width="1054" alt="image" src="https://github.com/user-attachments/assets/47bba33a-7885-4e69-a24b-bb91996ee669" />

</p>

<hr />
<br />

### 4. Virtual Machine (VM) Deployment (Client - Windows 10):

- **Deployed a Windows 10 Virtual Machine** named `client-1` within the same VNet and subnet.
- **Observed that a Network Security Group (NSG)** was automatically created by Azure for this VM.

<p>
<img width="1369" alt="image" src="https://github.com/user-attachments/assets/ae58c8d8-f25a-468b-b93a-d9ee15446a16" />
</p>

<br />
<p>
Next, we're deploying a Windows 10 virtual machine, which will be our domain-joined client. Similar to the server, Azure automatically creates a Network Security Group (NSG) for this VM. This NSG acts as a virtual firewall, managing network traffic. Again, we've encountered the Azure warning regarding default network restrictions, emphasizing that VMs are only reachable within their own virtual network. For initial testing, we've also enabled RDP (port 3389) access to this client VM from the public internet. This allows us to connect and configure the client before joining it to our domain.
</p>

<hr />
<br />

### 5. Set Server's (Private) IP address to "static" vs. "dynamic"

- **Selected the Server VM (`dc-1`).**
- **Navigated to the Network settings** for the VM.
- **Edited the IP configuration** of the Network Interface Card (NIC) to change the assignment from "Dynamic" to "**Static**".

<p>
    <img width="1375" alt="image" src="https://github.com/user-attachments/assets/6856021f-d0f9-4d7a-8a9a-8f99f7073bb3" />
</p>
<p>
    We're now configuring the Windows Server 2022 VM to use a static private IP address. By default, Azure assigns dynamic IP addresses, which can change after a VM restart. However, our server will function as a Domain Controller (DC) and DNS server. For consistent domain operations, it's crucial that the DC has a fixed, unchanging IP address. This ensures that client machines can reliably locate the server and that DNS resolution remains stable. Setting a static IP address guarantees the server's network location remains constant. Navigate to the virtual Network Interface Card's (NIC) IP Address Configuration and set to "static".
</p>

<hr />
<br />

### 6. Disable Server's Windows Defender Firewall

- **RDP'd into the Server VM (`dc-1`).**
- **Navigated to Windows Defender Firewall Properties.**
- **Set the Firewall state to "Off"** for the Domain, Private, and Public Profiles.

<p>
    <img width="1045" alt="image" src="https://github.com/user-attachments/assets/12726d3f-f568-4efb-9b32-ab79ed90a1db" />
</p>

<p>
    For this lab environment, the Windows Firewall was temporarily disabled on the server VM. This step was taken to simplify the configuration process and eliminate potential connectivity issues during the Active Directory setup. While this action is not recommended for production environments due to security risks, it streamlined the demonstration of core AD functionalities. It goes without saying that in real-world scenarios, properly configured firewalls are essential for network security.
</p>

<p>
    <img width="771" alt="image" src="https://github.com/user-attachments/assets/6b2bece8-338d-410f-99f1-dd6b20603816" />

</p>

<hr />
<br />

### 7. Configure the Client VM's primary DNS server to be the private IP address of the server VM

- **Noted the private IP address** of the Server VM (`dc-1`).
- **Navigated to the Network settings** of the Client VM (`client-1`) in the Azure portal.
- **Edited the DNS servers setting** within the Network Interface (NIC) IP configurations.
- **Selected "Custom DNS"** and entered the private IP address of the Server VM as the primary DNS server.

<p>
    <img width="592" alt="image" src="https://github.com/user-attachments/assets/a3695e86-ad82-44c4-a1cd-f66e19499631" />
</p>
<p>
    <img width="1329" alt="image" src="https://github.com/user-attachments/assets/1376e540-7311-47b1-b547-5de48f31d325" />
</p>
<p>
    <img width="788" alt="image" src="https://github.com/user-attachments/assets/9afbe171-d0e3-4842-afd5-66f7d5641277" />
</p>

<p>
    To ensure the Windows 10 client VM can effectively communicate with the domain controller and resolve domain names, we're configuring its primary DNS server to point to the static private IP address of our Windows Server 2022 VM. By default, Azure assigns its own internal DNS servers to VMs. However, this is inappropriate for our setup, as our server will function as the domain controller and authoritative DNS server for our domain. Directing the client to the domain controller's DNS is essential for proper domain join and authentication. This enables the client to locate domain resources and services, ensuring smooth domain operations within our Azure environment.
</p>
<hr />
<br />

### 8. Preliminary Check of Network Connection between Devices:

As a preliminary network connectivity check, ICMP (Internet Control Message Protocol) echo requests (pings) were initiated between the server (`dc-1`) and the client (`client-1`).

* **Ping from `client-1` to `dc-1` (using the Domain Controller's private IP address): Successful.** This confirms basic IP-level reachability from the client to the server.

* **Ping from `dc-1` to `client-1` (using the client's private IP address): Initially Failed.** This indicates a potential blockage of ICMP traffic from the server to the client.

<p>
    <img width="643" alt="image" src="https://github.com/user-attachments/assets/7e1a9e82-32f2-4072-8767-6f5be86ac1b2" />
</p>

<p>
    <img width="1036" alt="image" src="https://github.com/user-attachments/assets/79b77678-8fc7-43bf-8b92-031614547e6c" />
</p>

#### Troubleshooting this discrepancy, the following observations were made:

* **Ping from `client-1` to `client-1` (using its own private IP address): Successful.** This confirms that ICMP is generally functional on the client machine itself.

<p>
   This led to the initial hypothesis that <strong>ICMP inbound traffic might be blocked by a firewall on the `client-1` machine</strong>.
</p>
<p>
   While the Windows Firewall was intentionally disabled on the server (`dc-1`) for lab simplicity, the firewall status on the client (`client-1`) had not yet been explicitly modified. This could explain why the server couldn't ping the client, but the client could ping the server (outbound ICMP was likely allowed by default).
</p>


<hr />
<br />

### 9. Verify Client Machine DNS Settings to Server Private IP Address

- **RDP'd into `client-1`** and used `ipconfig /all` to inspect the DNS server settings.
- **Observed that the DNS server was still pointing to the old Azure DNS server.**
- **Restarted the `client-1` VM** to ensure the new DNS settings were applied.
- **Confirmed via `ipconfig /all`** that the primary DNS server was now the private IP address of `dc-1`.

<p>
    <img width="808" alt="image" src="https://github.com/user-attachments/assets/5157b705-5c56-47a3-91d1-8ce042665b89" />
</p>
<p>
    Needed to restart machine. Could've done a dns flush, but didn't think of it before restarting machine.
</p>

<hr />
<br />

### 10. Install Active Directory Domain Services (AD DS) on Server:

- **Opened Server Manager** on `dc-1`.
- **Initiated the "Add Roles and Features Wizard".**
- **Selected the "Active Directory Domain Services" and "DNS Server" roles** for installation.
- **Proceeded with the installation process.**
- **After installation, initiated the "Active Directory Domain Services Configuration Wizard".**
- **Selected the option to "Add a new forest".**
- **Specified a root domain name** (e.g., `mydomain.com`).
- **Configured the forest and domain functional levels.**
- **Defined a Directory Services Restore Mode (DSRM) password.**
- **Reviewed the DNS options and accepted the defaults.**
- **Confirmed the NetBIOS domain name.**
- **Reviewed the paths for the AD DS database, log files, and SYSVOL.**
- **Reviewed the prerequisites check and proceeded with the installation.**
- **The server automatically rebooted** after the domain controller promotion.
- **Logged back into the server** using domain administrator credentials, confirming the successful promotion to a domain controller.

#### Installing AD DS
<p>
    <img width="1109" alt="image" src="https://github.com/user-attachments/assets/d57e2876-56e9-4518-9813-49a02ddfa2aa" />
</p>
<p>
    <img width="919" alt="image" src="https://github.com/user-attachments/assets/67d6660c-62f9-4139-8c5f-5c517d20823d" />
</p>

#### Promote Server to Domain Controller
<p>
   <img width="1420" alt="image" src="https://github.com/user-attachments/assets/5a49aad3-e7dc-48f3-b790-efbaece12c62" />
</p>

#### Adding/Creating a New Forrest/Domain
<p>
    <img width="1271" alt="image" src="https://github.com/user-attachments/assets/4f5c808b-89b8-4131-9a66-b2a1e20ab852" />
</p>

#### Decide on NetBIOS Domain Name
- NetBIOS Domain Name is what shows up on login screen, so we have the option of "shrinking" the domain name so it doesn't take up too much real estate on the login screen.
<p>
    <img width="944" alt="image" src="https://github.com/user-attachments/assets/2c2a33cc-5ddd-49d2-b0df-b9ae9353a5df" />
</p>

#### Finale of Install
<p>
    <img width="1382" alt="image" src="https://github.com/user-attachments/assets/ebdc7fae-e396-4d36-8bdf-2e165ca37efb" />
</p>

#### Login Within Domain Context
<p>
    <img width="560" alt="image" src="https://github.com/user-attachments/assets/3e176ee6-7cab-407d-91d5-8decbebbb362" />
</p>

- Now that we've installed Active Directory, all users will exist within the domain vs. locally. This will permit user, permission, and access management from the domain.

<hr />
<br />

### 11. Create a Domain Admin user within the domain

- **Opened "Active Directory Users and Computers"** on `dc-1`.
- **Created two Organizational Units (OUs):** `_ADMINS` and `_EMPLOYEES` to organize user accounts.
- **Created a new user account** named "Jane Doe" with the username "jane_admin" within the `_ADMINS` OU.
- **Added the "jane_admin" user account to the built-in "Domain Admins" security group**, granting administrative privileges over the domain.
- **Logged off the `dc-1` server** and logged back in using the "jane_admin" domain account to verify administrative access.

#### Open Active Directory Users and Computers
<p>
    <img width="1432" alt="image" src="https://github.com/user-attachments/assets/1e56409d-63ea-4816-b65b-52f8083fe059" />
</p>

#### Create the OUs
<p>
    <img width="910" alt="image" src="https://github.com/user-attachments/assets/967efd01-3acc-47aa-8d9b-df9112fffe74" />
</p>
<p>
    <img width="905" alt="image" src="https://github.com/user-attachments/assets/b9b8a60a-7a3e-4921-899a-44a5713d343d" />
</p>

#### Add Jane Doe as a Domain Admin in the _ADMINS OU
<p>
    <img width="883" alt="image" src="https://github.com/user-attachments/assets/ac2d7e7a-79a3-4818-861b-23624f084868" />
</p>

- This account is not an Admin yet by virtue of being part of the _ADMINS OU

<p>
    <img width="927" alt="image" src="https://github.com/user-attachments/assets/7ad8bef8-b29c-45c0-bbb1-964962403e49" />
</p>

#### It needs to be added to the "Domain Admins" (built-in) Security Group

<p>
    <img width="743" alt="image" src="https://github.com/user-attachments/assets/ecf51687-de9b-4648-94ae-12427a2b95af" />
</p>

<p>
    <img width="804" alt="image" src="https://github.com/user-attachments/assets/b946e468-2210-4545-a665-7f0f92d66c0d" />
</p>

<p>
    <img width="830" alt="image" src="https://github.com/user-attachments/assets/3edf148b-255f-4701-a7d1-9410a71bcec2" />
</p>

#### Logoff and Login with "jane_admin"
<p>
    <img width="550" alt="image" src="https://github.com/user-attachments/assets/bd3ee05f-7b7f-46d0-8274-ceb881970fd6" />
</p>
<hr />
<br />

### 12. Client Domain Join:

- **On `client-1`, navigated to System Properties.**
- **Clicked "Change..."** in the Computer Name tab.
- **Selected the "Domain" radio button** and entered the domain name (`mydomain.com`).
- **Provided domain administrator credentials** when prompted to authorize the domain join.
- **Restarted the `client-1` VM** to complete the domain join process.
- **Temporarily disabled Microsoft Defender Firewall** on `client-1` to ensure unrestricted network communication with the Domain Controller for testing purposes.

#### Change from Workgroup to Domain
<p>
   <img width="1028" alt="image" src="https://github.com/user-attachments/assets/17372f04-fe50-455e-af94-287d2648c773" />
</p>

<p>
   System -> Advanced System Setting -> Computer Name ->
</p>

<p>
   <img width="1020" alt="image" src="https://github.com/user-attachments/assets/22f3a9bb-e509-42a6-a557-8779105a5755" />
</p>

#### Domain found because of DNS. Use Admin account to Join
<p>
   <img width="1007" alt="image" src="https://github.com/user-attachments/assets/703cbb5b-2418-4de9-982c-af628fb37a1a" />
</p>

<p>
   <img width="1069" alt="image" src="https://github.com/user-attachments/assets/bca52605-6b03-4b38-b69d-3d6e7919dbc5" />
</p>

### 13. Create Active Directory User Accounts:

- **Used PowerShell ISE on the Server VM (`dc-1`)** to execute a script for bulk creation of new domain user accounts.
- **Created user accounts** with various naming conventions and attributes.
- **Created security groups** (though not explicitly shown in screenshots for this step).
- **Added relevant users to the created security groups** based on their roles or permissions.
- **Prepared for testing user logins and group policy application** in subsequent steps.

#### Use Powershell ISE (on Server VM) to Run Script to Create Users

<p>
   <img width="851" alt="image" src="https://github.com/user-attachments/assets/3945448a-10d8-4a09-8773-91d4503d8bde" />
</p>

<p>
   <img width="704" alt="image" src="https://github.com/user-attachments/assets/815598c4-16ba-4d2f-8f1c-4cab34324398" />
</p>

### 14. Create Group Policy to Allow All Domain Users to Connect to ANY Client Machine in the _CLIENT OU via Remote Desktop

- **Opened Group Policy Management Console (gpmc.msc)** on `dc-1`.
- **Created a new GPO** named "Allow Domain Users RDP".
- **Edited the "Allow Domain Users RDP" GPO.**
- **Navigated to `Computer Configuration -> Policies -> Windows Settings -> Security Settings -> Local Policies -> User Rights Assignment`.**
- **Configured the "Allow log on through Remote Desktop Services" user right** to include the "Domain Users" group.
- **Linked the "Allow Domain Users RDP" GPO to the `_CLIENT` Organizational Unit (OU)** where client machines would reside.
- **Attempted to RDP** into `client-1` with a newly created domain user account (initially failed).
- **Forced a Group Policy update** on `client-1` using `gpupdate /force`.
- **Attempted RDP again** with the same domain user account (still failed).
- **Realized that adding "Domain Users" to the "Allow log on through Remote Desktop Services" user right is insufficient.** The "Domain Users" group also needs to be a member of the local **"Remote Desktop Users" group** on the client machine.
- **Navigated to `Computer Configuration -> Policies -> Windows Settings -> Security Settings -> Restricted Groups` within the "Allow Domain Users RDP" GPO.**
- **Added the local "Remote Desktop Users" group** and specified "Domain Users" as a member of this local group.
- **Forced another Group Policy update** on `client-1` using `gpupdate /force`.
- **Successfully RDP'd** into `client-1` using a standard domain user account, confirming the GPO was applied correctly.

#### Create Group Policy to Allow All Domain Users to Connect to ANY Client Machine in the _CLIENT OU via Remote Desktop

<p>
    <img width="455" alt="image" src="https://github.com/user-attachments/assets/5185c299-4995-4fcf-81bf-3da9695a50bf" />
</p>
<p>
    <img width="927" alt="image" src="https://github.com/user-attachments/assets/31e92fff-ce55-4f77-9eeb-51f9fb5bd084" />
</p>
<p>
    <img width="662" alt="image" src="https://github.com/user-attachments/assets/6818ee45-95b9-46c8-8c96-b57b1e207853" />
</p>

#### Edit Created GPO to allow Remote desktop
<p>
    Computer Configuration -> Policies -> Windows Settings -> Security Settings -> Local Policies1 -> User Rights Assignment
</p>
<p>
    <img width="1043" alt="image" src="https://github.com/user-attachments/assets/ef0ec565-2d92-48bf-83a1-ad6f5f03900c" />
</p>

<p>
    <img width="1043" alt="image" src="https://github.com/user-attachments/assets/590faaf3-088a-4e42-93b3-ea8665672ca9" />
</p>

#### Double click on "Allow log on through Remote Desktop Services."
<p>
    <img width="1013" alt="image" src="https://github.com/user-attachments/assets/02443b9b-09ca-4532-bbbe-77a343b1c6e2" />
</p>

<p>
    <img width="1364" alt="image" src="https://github.com/user-attachments/assets/20f3104f-292b-46ad-b08b-44b0f6c1bb0c" />
</p>

#### Link the GPO to the Client's OU:

<p>
    <img width="1260" alt="image" src="https://github.com/user-attachments/assets/2f164d9a-23ec-4852-a87c-9a6b1e751d17" />
</p>

#### Test RDP Access Prior to Group Policy Update (on the Client VM):
<p>
    <img width="883" alt="image" src="https://github.com/user-attachments/assets/8d6e9bde-4355-47b1-b078-f2f881388fdd" />
</p>
<p>
    <img width="788" alt="image" src="https://github.com/user-attachments/assets/87fae742-d4ee-4581-92da-a1b9436fca3f" />
</p>
<p>
    <img width="618" alt="image" src="https://github.com/user-attachments/assets/d0486b2e-8d35-4568-85ea-f39900f09431" />
</p>

#### Force Group Policy Update (on the Client VM):

<p>
    <img width="591" alt="image" src="https://github.com/user-attachments/assets/1adb89e5-f0b2-42aa-9a35-42271989d813" />
</p>

#### Test RDP Access (with Prev. Attempted User):

<p>
    <img width="819" alt="image" src="https://github.com/user-attachments/assets/c157dab6-8030-46ca-bd55-c1cca9b4468b" />
</p>
<p>
    <img width="618" alt="image" src="https://github.com/user-attachments/assets/d0486b2e-8d35-4568-85ea-f39900f09431" />
</p>

#### Failed. So, restarted/rebooted the machine

### Need to Add Remote Desktop Users to Security group
<p>
   The "Allow log on through Remote Desktop Services" user right grants the ability to initiate an RDP session, but it doesn't automatically grant the permission to connect to the remote computer
</p>

<p>
   Add Domain Users to the Remote Desktop Users Group:
</p>

<p>
   In the Group policy editor, navigate to:
</p>

- Computer Configuration -> Policies -> Windows Settings -> Security Settings -> Restricted Groups.
- Right click, and select "Add Group".
- Type "Remote Desktop Users" and click OK.
- Under "Members of this group" click "Add".
- Type "Domain Users" and click OK.
- Click OK.

<p>
    <img width="807" alt="image" src="https://github.com/user-attachments/assets/8afcbbfd-d86e-4c5f-95be-f5665a476af6" />
</p>

<p>
    <img width="848" alt="image" src="https://github.com/user-attachments/assets/bf48a530-8585-4a39-bd7d-b36c72461d1a" />
</p>

<p>
    <img width="559" alt="image" src="https://github.com/user-attachments/assets/529e4e36-55ed-4b5a-958a-399927e68f8f" />
</p>

#### Force Group Policy Update AGAIN (on the Client VM):

<p>
    <img width="625" alt="image" src="https://github.com/user-attachments/assets/520ea852-0303-44ea-9c9f-055ae652f1f8" />
</p>

#### Test RDP Access AGAIN (with Prev. Attempted User):

#### SUCCESS!
<p>
    <img width="1168" alt="image" src="https://github.com/user-attachments/assets/f123ea29-6b1f-4641-aa1e-a2ac9358db9d" />
</p>

### 15. Create New VM, Join to Domain, and Test RDP GPO Inheritance

- **Deployed a new Windows 10 Virtual Machine** named `client-2`.
- **Configured the DNS server IP address** on `client-2` to the private IP of the Domain Controller (`dc-1`).
- **Observed that the initial DNS setting** on `client-2` still showed the old Azure DNS server.
- **Attempted `ipconfig /flushdns`** on `client-2` (did not resolve the DNS issue).
- **Attempted `ipconfig /release`** on `client-2` (broke the RDP connection).
- **Restarted the `client-2` VM** via the Azure portal, which resolved the DNS server IP to the Domain Controller's private IP.
- **Joined `client-2` to the `mydomain.com` domain.**
- **Initially tested RDP access** to `client-2` with a domain user account *before* moving it to the `_CLIENT` OU (access was denied as expected).
- **Manually moved the `client-2` computer object** from the default "Computers" container to the `_CLIENT` OU in Active Directory Users and Computers.
- **Observed that after a short period (without manual `gpupdate /force` or reboot), the RDP GPO was automatically inherited**, and RDP access with domain users was successful, demonstrating GPO inheritance.

#### Configure new VM's DNS server to be DC server private IP address

<p>
    <img width="781" alt="image" src="https://github.com/user-attachments/assets/4178753c-7313-4afc-8756-14703a6ebf95" />
</p>

#### DNS setting on machine still show old DNS
<p>
    <img width="798" alt="image" src="https://github.com/user-attachments/assets/2d050b1d-840f-4b79-a555-16fbc396c62b" />
</p>

#### Test with DNS Flush -> Didn't Work
<p>
    <img width="771" alt="image" src="https://github.com/user-attachments/assets/29709c64-efd7-4eac-9281-48991ed2fa47" />
</p>

#### Test with release -> renew

- ` ipconfig /release ` broke my RDP connection, so had to restart the VM which flushed the DNS so didn't get a chance to teste ` ipconfig /renew `
- But, new VM DNS Server IP is the Domain Controller's Private IP. now

<p>
    <img width="909" alt="image" src="https://github.com/user-attachments/assets/bd76fee1-aa4f-46b5-ba60-aa9992f72f30" />
</p>

#### Join Domain

<p>
    <img width="854" alt="image" src="https://github.com/user-attachments/assets/e4195fc0-97cd-466c-887d-3a7fdedcf034" />
</p>

#### Domain Join Success -> but not part of OU -> test RDP to check GPO from a non-OU perspective
<p>
    <img width="771" alt="image" src="https://github.com/user-attachments/assets/901a3b4b-9dc4-4ee7-b88f-4ca4f6503d1c" />
</p>

#### Denied

<p>
    <img width="619" alt="image" src="https://github.com/user-attachments/assets/dfa27028-c77f-47e5-80cb-c9970e1fcb14" />
</p>

#### Manually move computer to _CLIENT OU
<p>
    <img width="678" alt="image" src="https://github.com/user-attachments/assets/a452bc19-e10b-4609-aeca-576efb577919" />
</p>

#### Was denied and while researching whether to reboot or gpupdate /force to apply the inheretance -> the GPO flowed down to the new VM

<p>
    <img width="897" alt="image" src="https://github.com/user-attachments/assets/4b4eb316-e866-4052-8e9d-dedbc57b7399" />
</p>

<p>
    <img width="307" alt="image" src="https://github.com/user-attachments/assets/eab99bf7-dd23-421f-95cd-e6f8f1932089" />
</p>

### 16. Create Group Policy to enforce Identity and Access Management (IAM) security policy

- **Opened Group Policy Management Console (gpmc.msc)** on `dc-1`.
- **Edited the "Default Domain Policy"** to apply password lockout settings domain-wide.
- **Navigated to `Computer Configuration -> Policies -> Account Policies -> Password Policy`.**
- **Configured the following settings:**
    - "Enforce password history": Set to a desired number (e.g., 24).
    - "Maximum password age": Set to a desired duration (e.g., 90 days).
    - "Minimum password length": Set to a desired length (e.g., 8 characters).
    - "Password must meet complexity requirements": Enabled.
- **Navigated to `Computer Configuration -> Policies -> Account Policies -> Account Lockout Policy`.**
- **Configured the following settings:**
    - "Account lockout threshold": Set to 5 invalid logon attempts.
    - "Account lockout duration": Set to a desired duration (e.g., 30 minutes).
    - "Reset account lockout counter after": Set to a duration less than the lockout duration (e.g., 30 minutes).
- **Forced a Group Policy update** on `client-2` using `gpupdate /force`.
- **Attempted to log in to `client-2`** with an incorrect password more than the lockout threshold (5 times).
- **Observed that the account was locked out**, preventing further login attempts.
- **On the Domain Controller (`dc-1`), unlocked the test user account** via Active Directory Users and Computers.
- **Successfully logged back into `client-2`** with the correct password.
- **Demonstrated password reset functionality** for a domain user from Active Directory Users and Computers on `dc-1`.
- **Demonstrated the ability to disable and enable a domain user account** from Active Directory Users and Computers on `dc-1`.

#### We can set up a new Group Policy, but instead we're going to edit the Default Domain Policy since the Password lockout should apply to the whole domain
<p>
    <img width="775" alt="image" src="https://github.com/user-attachments/assets/17e38a5e-bf2c-4973-9619-10eb116187fc" />
</p>

#### No lockout
<p>
    <img width="862" alt="image" src="https://github.com/user-attachments/assets/25502274-9776-4604-9781-4aab2a575d73" />
</p>

#### 5 Login attemps
<p>
    <img width="1006" alt="image" src="https://github.com/user-attachments/assets/b3f32194-5a07-4691-9258-ebc2c8efa06f" />
</p>
<p>
    <img width="1146" alt="image" src="https://github.com/user-attachments/assets/5d2d6d09-dd5e-4af9-a06b-7b2b6e5b587c" />
</p>

#### On cliet 2 (new VM) I gpupdate /force
<p>
    <img width="621" alt="image" src="https://github.com/user-attachments/assets/bdafc0cd-3392-422f-a695-7798096163fc" />
</p>

#### Attemp 5+ bad login attempts
<p>
    <img width="604" alt="image" src="https://github.com/user-attachments/assets/907983cb-a093-4518-ad34-c33ac1d7f14b" />
</p>

#### Unlock account on DC
<p>
    <img width="729" alt="image" src="https://github.com/user-attachments/assets/544f1812-9068-4678-997e-e34a2806e394" />
</p>

#### Back in Business
<p>
    <img width="527" alt="image" src="https://github.com/user-attachments/assets/c4b1ec95-be2d-4055-a510-7fb2edbc2b32" />

</p>

#### Resetting Password

- from the domain, right click on user and see

<p>
    <img width="682" alt="image" src="https://github.com/user-attachments/assets/509867fb-6996-4716-aa33-de8120c680f1" />
</p>

#### Disable/Enable Account (Maybe if compromised)

<p>
    <img width="726" alt="image" src="https://github.com/user-attachments/assets/35542e7a-8bff-44a6-b693-18448b211bad" />
</p>
<p>
    <img width="565" alt="image" src="https://github.com/user-attachments/assets/1e63cb88-aeaa-4b97-b42d-642c14a64336" />
</p>

<p>
    <img width="709" alt="image" src="https://github.com/user-attachments/assets/5cb80581-a785-47b6-a1a8-79ec5c3becae" />
</p>

#### Back in Business
<p>
    <img width="537" alt="image" src="https://github.com/user-attachments/assets/1f800038-f0e2-42ef-a192-12749ed6615d" />
</p>

---

## Verification:

- **Verified DNS resolution** on the client machines by successfully pinging the Domain Controller by its hostname and resolving domain names.
- **Verified domain join and user authentication** by successfully logging into the client VMs with various domain user accounts.
- **Verified Group Policy application** by confirming that the RDP access GPO and the account lockout policy were successfully applied to the client machines.

<p>
This project successfully demonstrated the deployment of a basic on-premises Active Directory environment within Azure Virtual Machines. Key aspects of AD, including domain creation, user and group management, DNS integration, and Group Policy application for access control and security policies, were explored and validated. This provides a foundational understanding of how Active Directory can be implemented and managed within a cloud infrastructure.
</p>

<br />
