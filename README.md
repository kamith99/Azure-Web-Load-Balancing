# Azure Web Load Balancing

## Project Overview

This project demonstrates the setup of load balancing for websites using Azure services. It includes detailed documentation and scripts to automate the deployment of essential Azure resources such as resource groups, virtual networks, virtual machines, load balancers, and private DNS zones.

## Repository Structure

- **Documentation/**: Contains detailed guides and tutorials.
  - `ProjectDemonstration-W24.md`: Comprehensive project document.
- **Scripts/**: Contains shell scripts to automate the creation of Azure resources.
  - `create-resource-group.sh`: Script to create a resource group.
  - `create-vnets.sh`: Script to create virtual networks.
  - `create-load-balancer.sh`: Script to create a load balancer.
  - `create-vms.sh`: Script to create virtual machines.
  - `create-dns-zone.sh`: Script to create a private DNS zone.
- **Media/**: Contains images and diagrams used in the documentation.
  - `topology-diagram.png`: Network topology diagram.
  - `webserver1-vm.png`: Screenshot for creating a resource group.
  - `webserver2-vm.png`: Screenshot for creating a virtual network.
  - `lient-vm.png`: Screenshot for setting up the load balancer.
 

## Getting Started

To get started, clone this repository and follow the instructions provided in the documentation files.

   ```sh
   git clone https://gitlab.com/your-username/Azure-Web-Load-Balancing.git
   cd Azure-Web-Load-Balancing
