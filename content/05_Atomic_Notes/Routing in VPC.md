==A VPC contains an implicit router that handles routing between subnets and manages traffic to and from external networks via route tables.==

>[!hint]
>_Router_: Acts as a Traffic Controller
>_Route tables_: GPS maps with rules

# VPC Router and Subnet Interface

**VPC Router**: Traffic controller inside your VPC.
- It has a special IP address (_default gateway_) inside each subnet to manage traffic for that subnet.
> [!example] Example
> If your subnet’s IP range is **192.168.1.0/24**, the router’s IP for that subnet is **192.168.1.1**
- AWS reserves that IP and is the "gateway" that devices inside the subnets use to send traffic outside their subnet.

---

# Route Tables and How Traffic is Directed

**Route Tables**: A collection of rules that guide where the network traffic from your subnet or[[Internet Gateways VPC | IGW]] goes. Each rule in this table is referred to as a route.
- Each subnet is linked to a route table. The route table includes a destination address that indicates where the network should go.
==The [[#VPC Router and Subnet Interface|Router]] manages these tables.==
- Each VPC has its own VPC router that uses the route tables in that VPC to manage the traffic flow within the VPC and external subnets.

When a packet leaves, the router checks the destination IP against these routes to decide where to forward it.

- [i] If there are multiple matching routes (e.g., a broad range like 10.16.0.0/16 and a more specific one like 10.16.1.0/24), the router chooses the **most specific one** (the smaller subnet).![[Pasted image 20250809195035.png]]

---
# Route Targets and Default Route

_Every VPC has a **default route table** with a mandatory "local" route that lets subnets communicate with each other inside the VPC._
- Each route points to a **target**, which is where the router sends the packet next.
- Targets can be:
    - **local**: traffic stays inside the VPC (between subnets).
    - A gateway (like an Internet Gateway) for traffic going outside the VPC.
    - Specific devices (like an EC2 instance) or other network components.

---

# Subnet Association and Multiple RT

- [I] Each subnet in a VPC is linked to one route table at a time.
By default, all subnets use the default rt.
But you can assign different route tables to different subnets if you want different routing behaviors (for example, public vs. private subnets).
> [!tip]
> - Grouping subnets with similar rules under one route table makes it easier to manage.

---

# Common Use Cases for AWS Route Tables

1. Routing to a Virtual Private Gateway
2. Routing to an AWS Outposts Local Gateway
3. Routing to a VPC Peering Connection
4. Routing to a Gateway VPC Endpoint
5. Routing to an Egress-only Internet Gateway
6. Routing for a Transit Gateway

