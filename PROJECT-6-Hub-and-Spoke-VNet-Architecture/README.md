**PROJECT 6**

**Hub-and-Spoke VNet Architecture
Networking  |  VNet Peering, NSGs, Azure Bastion, Hub-and-Spoke Topology**

**What I Built**

I designed and deployed a hub-and-spoke network topology in Central US. A hub VNet hosts Azure Bastion as the single secure access point. Two spoke VNets connect to the hub via VNet peering. All VMs have no public IP, access is only through Bastion browser-based SSH. During this project I discovered a key Azure networking limitation: VNet peering does not support transitive routing by default, meaning spoke-to-spoke traffic cannot flow through the hub without a dedicated transit mechanism. This required adding a direct spoke-to-spoke peering to achieve connectivity between vm-dev and vm-prod. NSGs restrict prod subnet traffic to HTTP and HTTPS only. This was the most technically challenging project in the series, rebuilt three times before achieving a fully working configuration.

**Architecture Diagram**
<img width="975" height="759" alt="image" src="https://github.com/user-attachments/assets/6a945624-666e-4f7b-bea2-7e9e171aadda" />

 
**Azure Services Used**

vnet-hub (10.0.0.0/16)	Hub VNet hosting Azure Bastion — private subnet disabled to allow Bastion outbound connectivity

vnet-spoke-dev (10.1.0.0/16)	Dev spoke VNet hosting vm-dev with no public IP — private subnet disabled

vnet-spoke-prod (10.2.0.0/16)	Prod spoke VNet hosting vm-prod with no public IP — NSG applied to subnet-prod

VNet Peering hub↔dev and hub↔prod	Bidirectional peerings — Fully Synchronized and Connected, all access and forwarded traffic enabled

VNet Peering dev↔prod	Direct spoke-to-spoke peering added to work around Azure transitive routing limitation — required for spoke-to-spoke connectivity without Azure Firewall

Azure Bastion Basic SKU	Browser-based SSH access to all VMs — password authentication used after SSH key auth caused persistent disconnections

NSG nsg-prod	Applied to subnet-prod — allows HTTP port 80 and HTTPS port 443 inbound only

vm-dev and vm-prod	Ubuntu 24.04, Standard_D2s_v3, no public IP — passwords set via Portal Reset Password feature

**Key Concepts Demonstrated**

•	Hub-and-spoke topology — hub hosts shared services while spokes host workloads. Most common enterprise Azure network design pattern

•	Azure VNet peering is non-transitive — the most important lesson from this project. Even with hub peerings fully Connected, spoke-to-spoke traffic cannot flow through the hub without Azure Firewall, VPN Gateway, or a direct spoke-to-spoke peering. This is a real production limitation that catches experienced engineers off guard

•	Non-overlapping address spaces — hub 10.0.x, spoke-dev 10.1.x, spoke-prod 10.2.x. Overlapping ranges is the most common VNet peering error

•	Bidirectional peering — must be created from both sides. One-direction-only peering shows as Initiated not Connected

•	Private subnet setting — must be disabled on AzureBastionSubnet and all spoke subnets. Enabling it cuts off default outbound routing which breaks Bastion and VM connectivity

•	Effective routes vs OS route table — Effective routes on the NIC shows Azure platform-level routing and is the definitive source of truth. OS ip route show can lag behind and differs from what Azure has programmed

•	AzureBastionSubnet naming — must be exactly this name, case sensitive, minimum /27 size. Using the Azure Bastion subnet template ensures correct naming

**Step-by-Step Summary**

1.	Created rg-network-lab in Central US

2.	Created vnet-hub (10.0.0.0/16) with subnet-shared (10.0.1.0/24) — private subnet disabled

3.	Created vnet-spoke-dev (10.1.0.0/16) with subnet-dev (10.1.1.0/24) — private subnet disabled

4.	Created vnet-spoke-prod (10.2.0.0/16) with subnet-prod (10.2.1.0/24) — private subnet disabled

5.	Created bidirectional hub↔dev and hub↔prod peerings — all access and forwarded traffic enabled on both sides

6.	Deployed vm-dev in subnet-dev and vm-prod in subnet-prod — Standard_D2s_v3, Ubuntu 24.04, no public IP, NIC NSG set to None

7.	Added AzureBastionSubnet (10.0.2.0/26) using Azure Bastion subnet template — private subnet disabled, no NSG

8.	Created pip-bastion (Standard Static) and deployed Azure Bastion Basic SKU

9.	Reset VM passwords via Portal to enable password-based Bastion authentication

10.	Connected to vm-dev via Bastion — confirmed SSH session working

11.	Ran ip route show — confirmed 10.2.0.0/16 missing, pinged vm-prod — 100% packet loss

12.	Checked Effective routes on NIC — confirmed 10.2.0.0/16 absent at Azure platform level

13.	Identified root cause: Azure VNet peering non-transitive — added direct dev↔prod spoke-to-spoke peering

14.	Restarted both VMs — 10.2.0.0/16 appeared in route table, ping to vm-prod succeeded

15.	Created nsg-prod with allow-http (port 80, priority 100) and allow-https (port 443, priority 110), associated with subnet-prod

16.	Deleted Bastion first then deleted rg-network-lab

**Screenshots**

1. Three VNets created in rg-network-lab
 
<img width="975" height="220" alt="image" src="https://github.com/user-attachments/assets/39480660-17d2-4f70-bb22-af59d5483bc7" />

*rg-network-lab showing vnet-hub, vnet-spoke-dev, and vnet-spoke-prod created in Central US.*

2. vnet-hub peerings — Fully Synchronized and Connected
 
<img width="973" height="358" alt="image" src="https://github.com/user-attachments/assets/9b5095b6-69ef-4bef-9e22-4531de10352a" />

*vnet-hub Peerings showing hub-to-dev and hub-to-prod both Fully Synchronized and Connected.*

3. vm-dev and vm-prod with no public IP
 
<img width="973" height="250" alt="image" src="https://github.com/user-attachments/assets/0677a87d-eb9a-4d3d-a4f7-39c810798213" />

*Both VMs showing Public IP as None — all access goes through Bastion.*

4. Azure Bastion deployed in hub VNet
 
<img width="975" height="472" alt="image" src="https://github.com/user-attachments/assets/22ecb082-18dd-4c02-9c83-dbabea8e0b9c" />

*bastion-hub Basic SKU in AzureBastionSubnet of vnet-hub.*

5. Bastion browser terminal connected to vm-dev
 
<img width="975" height="925" alt="image" src="https://github.com/user-attachments/assets/2bd104bb-c9fc-459e-88be-4c09fe3933bf" />

*Browser SSH session to vm-dev via password authentication — no public IP, no local SSH client.*

7. Direct spoke-to-spoke peering added
 
<img width="973" height="306" alt="image" src="https://github.com/user-attachments/assets/e6bbadc7-84df-48b2-b6db-528c13764e1b" />

*vnet-spoke-dev Peerings showing dev-to-prod peering added to fix transitive routing limitation.*

8. Successful ping from vm-dev to vm-prod
 
<img width="973" height="467" alt="image" src="https://github.com/user-attachments/assets/9ab50f1f-9d27-4f39-936f-b7159f7432a1" />

*Ping output from vm-dev reaching vm-prod private IP — packets received confirming end-to-end connectivity.*

9. NSG rules on nsg-prod

 <img width="973" height="434" alt="image" src="https://github.com/user-attachments/assets/8153068d-b30f-4136-bbd5-b4f4f38aa5f2" />

*nsg-prod inbound rules showing allow-http (port 80) and allow-https (port 443) applied to subnet-prod.*

**What Broke and How I Fixed It**

Problem 1: Bastion disconnecting immediately — SSH key authentication

•	What happened: Every Bastion connection attempt disconnected immediately with 'connection to Azure Bastion seems unstable'. Consistent across Chrome and Edge, across three full lab rebuilds

•	What I tried: Different browsers, cleared cache, unchecked private subnet on AzureBastionSubnet, redeployed Bastion multiple times

•	What actually fixed it: Switched to password authentication. Used Portal Reset Password on each VM then connected via Bastion with Password authentication type. Connected successfully first attempt. SSH key auth is unreliable with Basic Bastion SKU browser terminal

Problem 2: Private subnet enabled on AzureBastionSubnet

•	What happened: AzureBastionSubnet had Enable private subnet checked, cutting off Bastion outbound internet connectivity required for brokering connections

•	What I tried: Found via Edit subnet page in the Portal

•	What actually fixed it: Unchecked private subnet on AzureBastionSubnet. Also ensured all spoke subnets had private subnet disabled during VNet creation

Problem 3: Azure VNet peering non-transitive — spoke-to-spoke traffic failed

•	What happened: Despite hub peerings showing Fully Synchronized and Connected, ip route show in vm-dev showed no route to 10.2.0.0/16. Ping to vm-prod had 100% packet loss

•	What I tried: Recreated peerings multiple times, restarted VMs, DHCP refresh, waited 10+ minutes. Checked Effective routes — 10.2.0.0/16 confirmed absent at Azure platform level not just OS level

•	What actually fixed it: Added direct spoke-to-spoke peering between vnet-spoke-dev and vnet-spoke-prod. After VM restart, 10.2.0.0/16 appeared in routing table and ping succeeded. Root cause: Azure VNet peering is non-transitive by default — in production this would be solved with Azure Firewall in the hub providing transit routing rather than direct spoke peering

Problem 4: Ghost peering entry causing 'already exists' error

•	What happened: After deleting a peering from one VNet side, recreating it returned an error saying peering already existed

•	What I tried: Checked both VNets for leftover entries

•	What actually fixed it: Deleted peering from both VNets (hub AND spoke side), waited 2 minutes for Azure to fully clear the state, then recreated

**What I Learned**

This was the most technically challenging project in the series and the one that taught me the most. The discovery that Azure VNet peering is non-transitive was the biggest lesson, something that reads as a footnote in documentation became very real when I watched Effective routes confirm that 10.2.0.0/16 simply did not exist at the Azure platform level despite all peerings showing Connected. This is a genuine production gotcha. I also learned that Effective routes on the NIC is the definitive source of truth for routing problems, more reliable than ip route show in the OS which can lag behind. The Bastion troubleshooting taught me that Basic SKU has real limitations and password auth is a practical fallback. Rebuilding this lab three times gave me genuine fluency with VNet creation, peering configuration, and subnet settings that one successful run would never have provided.

**What I'd Do Differently / Next Steps**

•	Replace direct spoke-to-spoke peering with Azure Firewall in the hub — the correct enterprise architecture for hub-and-spoke transit routing

•	Add User Defined Routes forcing all spoke traffic through the Azure Firewall rather than routing directly between spokes

•	Upgrade Bastion to Standard SKU for more reliable browser terminal connections and native client support

•	Disable private subnet on all subnets during VNet creation as a standard checklist item

**Lab Specifications**

Region	Central US

Hub VNet	vnet-hub — 10.0.0.0/16

Bastion subnet	AzureBastionSubnet — 10.0.2.0/26 — private subnet disabled

Spoke Dev	vnet-spoke-dev — 10.1.0.0/16 — subnet-dev 10.1.1.0/24

Spoke Prod	vnet-spoke-prod — 10.2.0.0/16 — subnet-prod 10.2.1.0/24

VM size	Standard_D2s_v3

VM OS	Ubuntu Server 24.04 LTS

Public IP on VMs	None — private only

Bastion SKU	Basic

Bastion auth	Password — SSH key auth unreliable with Basic SKU

Hub-spoke peerings	hub↔dev, hub↔prod — bidirectional, all traffic enabled

Spoke-spoke peering	dev↔prod — added to resolve transitive routing limitation

NSG	nsg-prod — HTTP port 80 + HTTPS port 443 inbound only

Private subnet	Disabled on all subnets


**Cost**

2x Standard_D2s_v3 VMs (~4 hr)	~$0.77

Azure Bastion Basic (~2 hr)	~$0.38 — deleted first

VNets, peering, NSGs	Free

Total for this lab	~$1.20 or less

**Resources and References**

•	Microsoft Learn — Implement virtual network peering

•	Microsoft Learn — Configure Azure Bastion

•	Microsoft Docs — Hub-spoke network topology in Azure

•	AZ-104 Study Guide — Implement and manage virtual networking

Part of my AZ-104 Azure Administrator lab project series — 7 hands-on projects covering every exam domain.
