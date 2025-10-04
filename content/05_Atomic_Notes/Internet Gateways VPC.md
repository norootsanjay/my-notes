By default, every subnet inside the VPC is private. Devices within this subnet cannot access the Internet, and external resources cannot reach them.
==To convert a subnet to a public subnet, you need to attach an IGW to your VPC==

> [!Important]
> - Each VPC can have only **one Internet Gateway** attached.
> - An Internet Gateway can only be attached to **one VPC** at a time.

An IGW is a horizontally scaled, highly available component that spans all availability zones within a region.

---

# Converting a Private Subnet to a Public Subnet

![[Pasted image 20250809201800.png]]
To transform a private subnet into a public one, follow these essential steps:
1. **Create an Internet Gateway.**
2. **Attach the Internet Gateway to your VPC.**
3. **Create a custom route table.**
4. **Configure a default route in the route table that points to the Internet Gateway.**  
    This default route ensures that any traffic without a more specific route is forwarded to the Internet Gateway.
5. **Associate the desired subnet with the custom route table.**  
    This association enables all resources within that subnet to access the Internet.

![The image illustrates the setup of an Internet Gateway within a VPC, showing steps like creating the gateway, attaching it to the VPC, and configuring route tables. It includes a diagram of a region with a public subnet in an availability zone.](https://kodekloud.com/kk-media/image/upload/v1752865571/notes-assets/images/AWS-Solutions-Architect-Associate-Certification-Internet-Gateways-VPC/internet-gateway-vpc-setup-diagram.jpg)

---

# Public and Private IP Addressing

When you deploy resources in a public subnet, they automatically receive a private IP address. To allow Internet-facing communication, you must enable the assignment of public IP addresses.

For example:
- A resource might have a private IP like 192.168.1.1.
- Additionally, it will receive a public IP, such as 1.1.1.1, which external clients can use to reach the resource.

It's important to note that the resource itself only recognizes its private IP. AWS manages the translation between the public and private IP addresses. When an external request is directed to the public IP, AWS forwards it to the corresponding private IP.

![The image is a diagram illustrating a network setup with a public IP, showing a resource within a public subnet in a VPC (default) in Availability Zone 2. It includes IP addresses and a checkbox indicating the intention to assign a public IP.](https://kodekloud.com/kk-media/image/upload/v1752865571/notes-assets/images/AWS-Solutions-Architect-Associate-Certification-Internet-Gateways-VPC/network-setup-public-ip-diagram.jpg)

Each network interface on a resource can have its pair of public and private IP addresses. AWS seamlessly manages the mapping between these IP addresses to ensure smooth communication.