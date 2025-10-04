Imagine you have an AWS Virtual Private Cloud (VPC) containing private subnets that host various resources without public IP addresses. These resources require ==secure connectivity== to an on-premises data centre. A VPN provides this secure link by establishing an ==encrypted IPsec tunnel== between the two environments, ensuring that communication remains private and protected.
- AWS Client VPN 
- AWS Site-to-Site VPN are the two services that make up this system.
![[Screenshot 2025-08-10 at 5.10.53 PM.png]]

![[Screenshot 2025-08-10 at 5.07.08 PM.png]]

# VPN Architecture in AWS

![[Pasted image 20250810170646.png]]
- **VPN Gateway (VGW):** Located on the AWS side, it terminates the VPN connection.
- **Customer Gateway (CGW):** Located on your on-premises network, it terminates the VPN connection on the customer side and possesses a public IP address.
For instance, if the Customer Gateway is assigned the public IP 1.1.1.1 and the VPN Gateway uses 2.2.2.2, an IPsec tunnel is established over the internet between these endpoints.
![[Pasted image 20250810171929.png]]
The encrypted IPsec tunnel ensures that all data transmitted across the public internet remains secure.

---

# Routing Between AWS and On-Premises Networks

To facilitate communication between the on-premises network (192.168.0.0/16) and the AWS VPC (10.0.0.0/16), you must configure routing appropriately. Packets destined for the on-premises network should be directed through the VPN Gateway. There are two routing approaches:

1. **Static Routing:** Manually add a route in the VPC routing table that directs traffic for 192.168.0.0/16 to the VPN Gateway.
2. **Dynamic Routing:** Use a routing protocol like Border Gateway Protocol (BGP) to automatically exchange routes between the VPN Gateway and the Customer Gateway. This dynamic method allows AWS to learn the on-premises routes automatically.
![[Pasted image 20250810172020.png]]

---

# Types

1. AWS Client VPN: It is a fully managed remote access VPN solution that allows your distant employees to safely access resources on AWS as well as your on-premises network. It automatically adjusts up or down, depending on demand, because it is fully elastic. Your users can access your applications in the same way before, during, and after the transfer to AWS. The OpenVPN protocol is supported by AWS Client VPN, including the software client.
2. AWS Site-to-Site VPN: It is a fully managed service that uses IP Security (IPSec) tunnels to establish a secure link between your data centre or branch office and your AWS resources. You can connect to both your Amazon Virtual Private Clouds (VPCs) and the AWS Transit Gateway when utilizing it, and two tunnels are used for each connection to increase redundancy.

| Feature                     | **Client VPN**                                                                                      | **Site-to-Site VPN**                                                                                      |
| --------------------------- | --------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------- |
| **Purpose**                 | Allows individual users to securely connect to AWS resources or on-premises networks from anywhere. | Connects an entire on-premises network (office, data center) to an AWS VPC.                               |
| **Connection Type**         | User’s laptop, desktop, or mobile device connects directly to AWS over VPN.                         | Network-to-network connection between AWS and your on-premises router/firewall.                           |
| **Endpoint**                | AWS Client VPN Endpoint (managed by AWS).                                                           | Virtual Private Gateway (VGW) or Transit Gateway (TGW) on AWS side + Customer Gateway (CGW) on your side. |
| **Access Scope**            | User gains access to only the networks and resources allowed by policies.                           | Entire on-premises network can access AWS VPC resources (and vice versa, if allowed).                     |
| **Authentication**          | Supports Active Directory, mutual authentication with certificates, or SAML-based authentication.   | Uses pre-shared keys or certificates for IPsec tunnel authentication between gateways.                    |
| **Typical Use Case**        | Remote employees working from home, traveling staff, or contractors.                                | Extending on-premises data center to AWS for hybrid cloud setup.                                          |
| **Scalability**             | Scales per user connection.                                                                         | Scales per network link.                                                                                  |
| **Transport Protocol**      | OpenVPN-based (TCP or UDP).                                                                         | IPsec VPN (usually with BGP for dynamic routing).                                                         |
| **User Device Requirement** | VPN client software needed on each device.                                                          | No special software for end-users; routing handled by network hardware.                                   |