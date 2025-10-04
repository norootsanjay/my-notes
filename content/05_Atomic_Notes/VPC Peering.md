> [!Note]
> VPC peering is an essential component in cloud networking, enabling direct communication between Virtual Private Clouds (VPCs) by connecting them through a private network.

# Overview of VPC

By default, resources hosted in different VPCs are isolated. This prevents instances in separate VPCs from communicating with each other. VPC peering creates a direct network connection between two VPCs, allowing instances in each VPC to interact as if they were on the same network.

VPC peering supports connections under various scenarios:

- Between VPCs in the same region.
- Across different regions.
- Between VPCs in different AWS accounts.

![[Pasted image 20250811003412.png]]

---
# How VPC Peering Works

Consider an example with two VPCs:

- **VPC One**: CIDR block 10.1.0.0/16
- **VPC Two**: CIDR block 10.2.0.0/16

One VPC initiates a peering request to the other. If the VPCs belong to different AWS accounts, the owner of the target VPC must accept the request. In the same account, the request is automatically approved. Once accepted, the peering connection is established.

After establishing the connection, you need to manually update the route tables in both VPCs. For instance, in VPC One, add a route that directs traffic destined for the 10.2.0.0/16 CIDR block to the peering connection. Similarly, in VPC Two, create a route for the 10.1.0.0/16 CIDR block pointing to the same connection.

![The image illustrates a VPC peering process between two virtual private clouds (VPC1 and VPC2) with their respective IP ranges, showing the sending and accepting of a peering request.](https://kodekloud.com/kk-media/image/upload/v1752865725/notes-assets/images/AWS-Solutions-Architect-Associate-Certification-VPC-Peering/vpc-peering-process-ip-ranges.jpg)

---

# Key Considerations for VPC Peering

One critical aspect of VPC peering is its non-transitive nature. For example, if you have three VPCs (VPC One, VPC Two, and VPC Three) and establish peering between VPC One & VPC Two and between VPC Two & VPC Three, VPC One will not automatically communicate with VPC Three. Each communication pair requires a dedicated peering connection.

![The image is a diagram illustrating VPC peering between three virtual private clouds (VPC 1, VPC 2, and VPC 3), with a central point indicating a connection issue.](https://kodekloud.com/kk-media/image/upload/v1752865727/notes-assets/images/AWS-Solutions-Architect-Associate-Certification-VPC-Peering/vpc-peering-diagram-connection-issue.jpg)
![[Screenshot 2025-08-11 at 9.15.57 AM.png]]

---
# Summary

VPC peering offers a secure and efficient mechanism to connect two VPCs, allowing direct routing of traffic as if they are part of the same network. The key benefits include:

|Feature|Description|
|---|---|
|Direct Connectivity|Enables private communication between VPC resources|
|Cross-Region Support|Works across regions and between AWS accounts|
|Cost Efficiency|No additional charge for peering connections; only data transfer is billed (if applicable)|