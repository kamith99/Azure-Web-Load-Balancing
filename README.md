# Azure Load Balancing Project

## Project Overview
This project demonstrates the setup of load balancing for websites using Azure services. It includes detailed documentation and scripts to automate the deployment of essential Azure resources such as resource groups, virtual networks, virtual machines, load balancers, and private DNS zones.

## Lab Objectives
In this project, we will investigate and construct an architecture using the following Azure services:
- Azure Bastion
- Azure Virtual Machines
- Azure Virtual Networks
- Azure Virtual Network Peering
- Azure Load Balancer
- Azure Private DNS

## Lab Materials
- **Azure Bastion**
  - [What is Azure Bastion?](https://learn.microsoft.com/en-us/azure/bastion/bastion-overview)
  - [Azure Bastion Tutorial](https://learn.microsoft.com/en-us/azure/bastion/tutorial-create-host-portal)
  
- **Azure Virtual Networks**
  - [What is Azure Virtual Network?](https://learn.microsoft.com/en-us/azure/virtual-network/virtual-networks-overview)
  - [Azure Virtual Network Tutorial](https://learn.microsoft.com/en-us/azure/virtual-network/virtual-network-tutorial-create-vnet)
  
- **Azure Virtual Network Peering**
  - [What is Azure Virtual Network Peering?](https://learn.microsoft.com/en-us/azure/virtual-network/virtual-network-peering-overview)
  - [Azure Virtual Network Peering Tutorial](https://learn.microsoft.com/en-us/azure/virtual-network/tutorial-connect-virtual-networks-portal)
  
- **Azure Load Balancer**
  - [What is Azure Load Balancer?](https://learn.microsoft.com/en-us/azure/load-balancer/load-balancer-overview)
  - [Create Internal Load Balancer Tutorial](https://learn.microsoft.com/en-us/azure/load-balancer/tutorial-load-balancer-basic-internal-portal)
  
- **Azure Private DNS**
  - [What is Azure Private DNS?](https://learn.microsoft.com/en-us/azure/dns/private-dns-overview)
  - [Create a Private Zone Tutorial](https://learn.microsoft.com/en-us/azure/dns/private-dns-getstarted-portal)
  
- **Project Topology**
  - [Download Topology](Media/topology-diagram.png) (Provide the link to the topology file)
  

## Project Instructions
We will be creating an internal load balancer which randomly selects which webserver will be serving our private website. A client VM will be accessing this website from another virtual network outside of our webservers’ virtual network. We will demonstrate on the client VM that both webservers can service our private website. The project topology outlines the different components and requirements for our project.

### Project Components
- 1 Resource Group
- 2 Webservers
- 1 Client VM
- 1 Azure Load Balancer
- 2 Virtual Networks
- 3 Subnets in total
- 1 Azure Bastion Host
- 1 Private DNS Zone

### Resource Group Requirements
Create a resource group that will be the logical container that holds all our resources for this project. 
- Resource group name: `project1-rg`

### Virtual Network Requirements
Create 2 virtual networks which will be used to connect our resources. A total of 3 subnets will be created [2 subnets in vnet1 and 1 subnet in vnet2].

- **Virtual Network 1**
  - Use your network address space assigned in Blackboard
  - Name: `project1-vnet1`
  - Create 2 subnets within this Virtual Network
    - Subnet Name: `vnet1-subnet1`
    - Subnet Name: `AzureBastionSubnet`
  - Assign an appropriate address using your network address

- **Virtual Network 2**
  - Name: `project1-vnet2`
  - Create 1 subnet within this Virtual Network
    - Subnet Name: `vnet2-subnet1`
  - Assign an appropriate address for this virtual network
  - *Note: Do not use overlapping network address spaces*

Configure Virtual Network Peering between virtual networks.

### Load Balancer Requirements
Create a load balancer within virtual network 1 in `vnet1-subnet1`.
- Name: `project1-lb`
- Type: Internal
- Virtual network: `project1-vnet1`
- Subnet: `vnet1-subnet1`

**Load Balancer – Backend Pool**
- Name: `project1-BackendPool`

**Load Balancer – Health Probe**
- Name: `project1-HealthProbe`
- Protocol: HTTP
- Port: 80
- Interval: 15
- Unhealthy threshold: 3

**Load Balancer – Load Balancer Rule**
- Name: `project1-HTTPRule`
- Protocol: TCP
- Port: 80
- Idle timeout (minutes): 15
- TCP reset: Enabled

### Azure Bastion Requirements
To access our virtual machines, we will use the Azure Bastion PaaS service. This service allows us to connect to our virtual machines through SSL. By using this service, we do not need a public IP address for our virtual machines.
- Name: `project1-BastionHost`
- Region: Use the same region as your vnets
- Virtual network: `project1-vnet1`
- Subnet: `AzureBastionSubnet`
- Public IP address name: `project1-BastionIP`

### Webserver Requirements
Create 2 virtual machines which will be running IIS roles on both servers. Each server will be hosted on different availability zones. The default pages will be modified to include the name of the servers.

- **Webserver 1**
  - Name: `webserver1-vm`
  - Availability zone: 1
  - Image: Windows Server 2019 Datacenter – Gen2 (*)
  - Size: Standard_B1s
  - Virtual network: `project1-vnet1`
  - Subnet: `vnet1-subnet1`
  - Public IP: None 
  - Place this webserver in the load balancer created earlier

- **Webserver 2**
  - Name: `webserver2-vm`
  - Availability zone: 2
  - Image: Windows Server 2019 Datacenter – Gen2 (*)
  - Size: Standard_B1s
  - Virtual network: `project1-vnet1`
  - Subnet: `vnet1-subnet1`
  - Public IP: None
  - Place this webserver in the load balancer created earlier

(*) Microsoft tends to upgrade their images. Feel free to use another Gen# if Gen2 is not available.

#### IIS on Webserver 1
- Webpage Title: `Project1 Windows Server`
- Body Text: `This webpage is hosted on webserver1-vm`
- Background Color: Yellow

#### IIS on Webserver 2
- Webpage Title: `Project1 WebPage`
- Body Text: `This webpage is hosted on webserver2-vm`
- Background Color: Green

### Client VM Requirements
Create 1 virtual machine which will be running Windows 10 operating system. This virtual machine will reside in a different virtual network than the webservers. We will need to configure virtual network peering in order for us to log into the virtual machine using our Bastion Host.
- Name: `client-vm`
- Image: Windows 10 Pro, Version 20H2 – Gen2 (*)
- Size: Select an appropriate size based on the OS
- Virtual network: `project1-vnet2`
- Subnet: `vnet2-subnet1`
- Public IP: None

Remember to configure Virtual Network Peering between `vnet1` and `vnet2`. This step is required for us to connect to the client VM using our Bastion Host.

(*) Microsoft tends to upgrade their images. Feel free to use another version of the Windows 10 image if Windows 10 Pro, Version 20H2 – Gen2 is not available.

### Private DNS Zones Requirements
Create a private DNS zone which will be used when we access our website from our client VM. Configure the private DNS zone so that the client VM can connect to the webserver using its FQDN. Remember you may need to create an A record for the load balancer.
