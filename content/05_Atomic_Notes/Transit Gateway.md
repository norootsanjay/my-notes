> [!Note]
> AWS Transit Gateway service, which simplifies connectivity and routing between multiple VPCs and on-premises environments. AWS Transit Gateway offers a scalable alternative to managing a complex web of peering connections.

**Cons of VPC Peering:**
- VPC Peering doesn't support transitive communication. i.e., If VPC 1 is connected to VPC 2, VPC 2 is connected to VPC 3, then VPC 1 and VPC 3 cannot communicate with each other directly.
- When you scale out to include more VPCs, the number of required peering connections increases significantly.

# How Transit Gateway Works

- AWS Transit Gateway acts as a centralized router
- Allowing you to attach each VPC to the gateway, rather than wiring a full mesh of peerings

A key configuration requirement is to designate one subnet from each availability zone within a VPC for use by the Transit Gateway. For example, if your VPC spans three availability zones, create a dedicated subnet in each zone. This setup ensures that the Transit Gateway has sufficient network interfaces for effective traffic routing.

![[Pasted image 20250811211448.png]]

> [!Feature]
> A standout feature of Transit Gateways is their ability to peer with other Transit Gateways. This enables inter-region connectivity as well as connectivity across different AWS accounts. For example, if you need to link resources in two regions or manage networks across separate AWS accounts, establishing a peering relationship between Transit Gateways simplifies these scenarios considerably.

![[Pasted image 20250811211646.png]]

# Summary

It eliminates the complexities of setting up a full mesh of VPC-to-VPC peerings by enabling transitive routing. Key takeaways include:

- Each VPC requires a designated subnet per availability zone for Transit Gateway connectivity.
- Transit Gateway reduces the number of VPN connections needed for on-premises connectivity.
- Integration with both VPN and AWS Direct Connect enhances on-premises connection options.
- Peering capabilities allow Transit Gateways to connect across regions and AWS accounts.