## Step-by-Step Guide

### Step 1: Clone the Repository
Start by Cloning the repository to your local machine using the following command:
```sh
git clone https://gitlab.com/your-username/Azure-Web-Load-Balancing.git
cd Azure-Web-Load-Balancing 
```

### Step 2: Create Resource Group
Create a resource group that will be the logical container for all your resources.

```sh
az group create --name project1-rg --location eastus
```

### Step 3: Create Virtual Networks and Subnets
Create the first virtual network with two subnets:
```sh
az network vnet create --name project1-vnet1 --resource-group project1-project2-rg --address-prefix <address-prefix-vnet1>
az network vnet subnet create --name vnet1-subnet1 --vnet-name project1-vnet1 --resource-group project1-project2-rg --address-prefix <address-prefix-subnet1>
az network vnet subnet create --name AzureBastionSubnet --vnet-name project1-vnet1 --resource-group project1-project2-rg --address-prefix <address-prefix-bastion>
```
Replace <address-prefix-vnet1>, <address-prefix-subnet1>, and <address-prefix-bastion> with your assigned network addresses.

Create the second virtual network with one subnet:
```sh
az network vnet create --name project1-vnet2 --resource-group project1-project2-rg --address-prefix <address-prefix-vnet2>
az network vnet subnet create --name vnet2-subnet1 --vnet-name project1-vnet2 --resource-group project1-project2-rg --address-prefix <address-prefix-subnet2>
```
Replace <address-prefix-vnet2> and <address-prefix-subnet2> with your assigned network addresses.

### Step 4: Configure Virtual Network Peering
Peer the two virtual networks:
```sh
az network vnet peering create --name vnet1-to-vnet2 --resource-group project1-project2-rg --vnet-name project1-vnet1 --remote-vnet /subscriptions/<subscription-id>/resourceGroups/project1-project2-rg/providers/Microsoft.Network/virtualNetworks/project1-vnet2 --allow-vnet-access
az network vnet peering create --name vnet2-to-vnet1 --resource-group project1-project2-rg --vnet-name project1-vnet2 --remote-vnet /subscriptions/<subscription-id>/resourceGroups/project1-project2-rg/providers/Microsoft.Network/virtualNetworks/project1-vnet1 --allow-vnet-access
```

### Step 5: Create Azure Bastion
Create the Azure Bastion host:
```sh
az network public-ip create --resource-group project1-project2-rg --name project1-BastionIP --sku Standard --location <location>
az network bastion create --name project1-BastionHost --public-ip-address project1-BastionIP --resource-group project1-project2-rg --vnet-name project1-vnet1 --location <location>
```
Replace <location> with the same region as your virtual networks.

### Step 6: Create Web Servers
Create the first webserver VM:
```sh
az vm create --resource-group project1-project2-rg --name webserver1-vm --image Win2019Datacenter --size Standard_B1s --vnet-name project1-vnet1 --subnet vnet1-subnet1 --admin-username <username> --admin-password <password> --no-wait --zone 1
az vm create --resource-group project1-project2-rg --name webserver2-vm --image Win2019Datacenter --size Standard_B1s --vnet-name project1-vnet1 --subnet vnet1-subnet1 --admin-username <username> --admin-password <password> --no-wait --zone 2
```
Replace <username> and <password> with your desired admin credentials.

### Step 7: Create Client VM
Create the client VM in the second virtual network:
```sh
az vm create --resource-group project1-project2-rg --name client-vm --image Win10Pro --size Standard_B1s --vnet-name project1-vnet2 --subnet vnet2-subnet1 --admin-username <username> --admin-password <password> --no-wait
```
Replace <username> and <password> with your desired admin credentials.

### Step 8: Create Load Balancer
Create an internal load balancer:
```sh
az network lb create --resource-group project1-project2-rg --name project1-lb --sku Basic --vnet-name project1-vnet1 --subnet vnet1-subnet1 --private-ip-address <private-ip>
az network lb address-pool create --resource-group project1-project2-rg --lb-name project1-lb --name project1-BackendPool
az network lb probe create --resource-group project1-project2-rg --lb-name project1-lb --name project1-HealthProbe --protocol Http --port 80 --interval 15 --threshold 3
az network lb rule create --resource-group project1-project2-rg --lb-name project1-lb --name project1-HTTPRule --protocol Tcp --frontend-port 80 --backend-port 80 --idle-timeout 15 --tcp-reset true --frontend-ip-name LoadBalancerFrontEnd --backend-pool-name project1-BackendPool --probe-name project1-HealthProbe
```
Replace <private-ip> with the desired private IP address for the load balancer.

### Step 9: Configure IIS on Web Servers
Log into each webserver using Azure Bastion and configure IIS with the specified settings.

Webserver 1

Webpage Title: studentID’s Windows Server
Body Text: This webpage is hosted on webserver1-vm
Background Color: Yellow
Webserver 2

Webpage Title: studentID’s WebPage
Body Text: This webpage is hosted on webserver2-vm
Background Color: Green

### Step 10: Create Private DNS Zone
Create a private DNS zone:
```sh
az network private-dns zone create --resource-group project1-project2-rg --name <dns-zone-name>
az network private-dns link vnet create --resource-group project1-project2-rg --zone-name <dns-zone-name> --name vnet1-link --virtual-network project1-vnet1 --registration-enabled false
az network private-dns link vnet create --resource-group project1-project2-rg --zone-name <dns-zone-name> --name vnet2-link --virtual-network project1-vnet2 --registration-enabled false
az network private-dns record-set a add-record --resource-group project1-project2-rg --zone-name <dns-zone-name> --record-set-name <record-name> --ipv4-address <load-balancer-ip>
```
Replace <dns-zone-name>, <record-name>, and <load-balancer-ip> with the desired DNS zone name, record name, and the load balancer's private IP address.

### Step 11: Test the Setup
From the client VM, access the private website using the FQDN and verify that both webservers can serve the website.




