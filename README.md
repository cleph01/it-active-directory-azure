<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>On-premises Active Directory Deployed in the Cloud (Azure)</h1>
This project demonstrates the implementation of on-premises Active Directory within Azure Virtual Machines.<br />

<!---
<h2>Video Demonstration</h2>

- ### [YouTube: How to Deploy on-premises Active Directory within Azure Compute](https://www.youtube.com)

--->

<h2>Environments and Technologies Used</h2>

- Microsoft Azure 
    - Virtual Machines/Compute
    - Virtual Network
    - Network Security Groups (Firewall)
- Active Directory Domain Services (AD DS)
- Remote Desktop
- DNS
- PowerShell
  
<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (21H2)

<h2>High-Level Deployment and Configuration Steps</h2>

1. Setup Infrastructure:
    - Azure Resource Group, Virtual Network, and Network Security Group configuration
    - Deploy two Azure Virtual Machines: Windows Server 2019 (Domain Controller) and Windows 10 (Client)

2. Configure Active Directory:
    - Install and configure Active Directory Domain Services (AD DS) on the Windows Server 2019 VM
    - Create and promote the server to a domain controller
    - Modify DNS settings to static instead of dynamic

3. Integrate Client Machine and User Management:
    - Join Windows 10 client VM to Domain
    - Configure IPV4 settings to point to Domain Controller for DNS instead of Azure's DNS
    - Create users, groups, and organizational units (OUs) within Active Directory
    - Verify user authentication and group policy.

<h2>Deployment and Configuration Steps</h2>


### 1. Create a Resource Group:
<ul>
    <li>
        Create a new Azure Resource Group to contain all resources
    </li>
</ul>

<p>
<img width="1118" alt="image" src="https://github.com/user-attachments/assets/29b297df-862f-4a0c-a9da-35ef2f72dd14" />
</p>

<p>
Azure Resource Groups are logical containers (like folders) that hold related Azure resources in one place. Resource Groups help us to organize our VMs, virtual network, and other components, simplifying deployment and management. This helps us manage everything together, like deploy, update, or delete all resources as a single unit. Using a Resource Group also makes it easier to keep track of costs and manage access control, streamlining our Azure environment.

</p>

<br />

### 2. Establish Virtual Network (VNet) and Subnet:
<ul>
<li>
    Create a VNet with a suitable address space
</li>
    <li>
        Create a subnet within the VNet for the VM
    </li>
</ul>

<p>
<img width="854" alt="image" src="https://github.com/user-attachments/assets/2c17c14a-34c0-4a34-acda-6df84b2bb28b" />
</p>

<p>
We're choosing to manually create our virtual network in-lieau of the auto-creation that comes from creating a virtual machine.
</p>
<p>
A Virtual Network (VNet) is like creating your own private network in the cloud. We're setting up a VNet to allow our virtual machines to communicate with each other securely. We'll use the address space 10.0.0.0/16, which means we have 65,536 possible IP addresses for our network. Within this VNet, we'll create a subnet, 10.0.0.0/24, which is a smaller part of the VNet, providing 256 addresses. This subnet will house our VMs and help manage network traffic efficiently.
</p>


<br />

### 3. Virtual Machine (VM) Deployment (Server - Windows Server 2019):
<ul>
<li>
    Deploy a Windows Server 2019 VM
</li>
    <li>
        Place it within the created VNet and subnet
    </li>
    <li>
        NSG (Firewall) is automatically created for the resource
    </li>
</ul>
   
<p>
<img width="970" alt="image" src="https://github.com/user-attachments/assets/e488a121-8630-4517-a71f-c7ee4bbc5df7" />
</p>

<p>
We're now creating our first virtual machine, a Windows Server 2019 instance, which will act as our domain controller. When creating a VM, Azure automatically assigns a Network Security Group (NSG) to it. Think of an NSG as a virtual firewall, controlling network traffic to and from the VM. This is not a dedicated firewall appliance, but a rule based access control list (ACL) assigned to the Network Interface Card of the virtual machine. For testing purposes, we've enabled RDP (port 3389) access during VM creation. Azure warns that by default, VMs are only accessible within their virtual network, but we've chosen to allow RDP traffic from the public internet. This allows us to remotely connect to the server and configure it.
</p>

<p>
<img width="1054" alt="image" src="https://github.com/user-attachments/assets/47bba33a-7885-4e69-a24b-bb91996ee669" />

</p>

<br />

### 4. Virtual Machine (VM) Deployment (Client - Windows 10):
<ul>
<li>
    Deploy a Windows 10 VM
</li>
    <li>
        Place it within the same VNet and subnet
    </li>
    <li>
        NSG (Firewall) is automatically created for the resource
    </li>
</ul>

<p>
<img width="1369" alt="image" src="https://github.com/user-attachments/assets/ae58c8d8-f25a-468b-b93a-d9ee15446a16" />
</p>

<p>
Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.
</p>

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>


- Assign a static private IP address
- Configure the Client VM's primary DNS server to be the private IP address of the server VM

  
4. Define Network Security Group (NSG) / Firewall:
    - Create an NSG to control inbound and outbound traffic
    - Allow RDP (port 3389) for management -> Default is exclude all incoming
    - Allow DNS traffic

<img width="1036" alt="image" src="https://github.com/user-attachments/assets/79b77678-8fc7-43bf-8b92-031614547e6c" />

<p>
Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.
</p>



<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>



5. Active Directory Domain Services (AD DS) Configuration (Server):
    - Install AD DS and DNS roles
    - Promote the server to a domain controller
    - Create a new forest and domain
    - Configure DNS to point to the server's private IP


<p>
Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.
</p>

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>



7. Client Domain Join:
    - Join the Windows 10 VM to the created domain
    - Disable Microsoft Defender to allow Domain Controller network communication


<p>
Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.
</p>

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>

8. Active Directory User and Group Creation:
    - Create Organizational Units (OUs) for organization
    - Create user accounts with varying permissions
    - Create security groups, and add users to them
    - Test user logins, and group policy


<p>
Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.
</p>

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>

9. Verification:
    - Verify DNS resolution
    - Verify domain join and user authentication
    - Verify group policy application
<p>
Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.
</p>

<br />
