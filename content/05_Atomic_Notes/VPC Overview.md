It provides a ==secure, isolated section of AWS where== we can launch AWS resources in a virtual network that we define.
- [i] One Customer's data remains separate from another's.

**Within a VPC, we can have full control over the networking environment:**

1. Create custom IP addressing through subnetting.
2. Configure routing tables to control packet flow within your account.
3. Use security features such as Security Groups and Network Access Control Lists (NACLs) to manage traffic flow.
4. Customize inbound and outbound traffic using various types of gateways.

> Managing your VPC in AWS is similar to managing a traditional data center with routers and switches; however, the AWS Management Console streamlines and simplifies the process.

---

## Regional Isolation and Deployment

==VPCs are region-specific==. So, resources within one VPC are isolated from those in another. To enable communication between the internet and between VPCs, you must explicitly configure the necessary settings.

---

# The Role of CIDR

- Each VPC is assigned a range of IP addresses via its Classless Inter-Domain Routing (CIDR) block.
- A CIDR block defines the IP addresses that the resources within the VPC can use.
- Its size can be anywhere from /16 to /24
- [2] In addition to the primary CIDR block, we can enable secondary IPv4 CIDR blocks or add up to 6 IPv6 CIDR blocks per VPC (each providing a /56 block), enhancing the flexibility and scalability of your network configuration.

---

# Types of VPC

1. **Default VPC:**
	A default VPC is automatically created by AWS when a new account is set up. Each region comes with a default VPC, pre-configured to allow immediate internet connectivity for your instances. This ready-to-use configuration lets you deploy servers without investing time in complex networking setups.
2. **Custom VPC:**
	 Custom VPCs are created and fully configured by you. When you set up a custom VPC, you define:
	- The CIDR block
	- Subnets and their IP addressing
	- Routing configurations
	- Network access rules using security groups and NACLs

## Default VPC Configuration

AWS provides several default configurations for a default VPC to ease network setup. Below is an overview of these settings:

|Feature|Default Configuration|Description|
|---|---|---|
|**CIDR Block**|172.31.0.0/16|Provides 65,536 IP addresses|
|**Default Subnets**|One subnet per availability zone (typically /20 CIDR each)|For example, one zone may have 172.31.16.0/20 and another 172.31.32.0/20|
|**Internet Gateway**|Attached with a default route (0.0.0.0/0)|Enables internet connectivity for instances|
|**Default Security Group**|Configured to allow outbound traffic|Protects instances by default|
|**Default Network ACL (NACL)**|Allows both inbound and outbound traffic|Provides an additional layer of security|
![[Pasted image 20250808171628.png]]
