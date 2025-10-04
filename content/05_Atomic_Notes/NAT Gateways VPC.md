# What is NAT?

![[Pasted image 20250809233011.png]]
==NAT - NETWORK ADDRESS TRANSLATION==
The idea is to allow multiple devices to access the internet through a single public address.

> [!note]
> Translation of private IP to a public IP is required.

- [2] **Network Address Translation** is a process in which one or more local IP addresses are translated to one or more Global IP addresses or vice versa in order to provide internet access to the local hosts.
Also, it does the translation of port numbers, i.e., masks the port number of the host with another port number, in the packet that will be routed to the destination.
It then makes corresponding entries of IP address and port number in the NAT table.

- [f] NAT generally works on a router or a firewall.

---

# AWS NAT Gateways

 It is a managed AWS service that is scaled based on your usage. A NAT gateway allows instances in a private subnet to access the internet, while at the same time, preventing the internet from initiating connections with those instances.
 A private subnet does not have a direct route to the Internet, which means that instances launched in that subnet cannot access resources on the Internet or be accessed from the Internet. To allow instances in a private subnet to access the internet, you can use a NAT Gateway.

- [I] You can create **up to 5 NAT Gateways per Availability Zone** by default in AWS.
- [I] **Note:** NAT Gateway should always be associated with an Elastic IP.

A NAT Gateway is deployed in a public subnet and acts as a bridge between instances in the private subnet and the internet. When an instance in a private subnet sends a request to the Internet, the request is forwarded to the NAT Gateway, which replaces the instance’s private IP address with the NAT Gateway’s public IP address and sends the request to the Internet. When the response is received, the NAT Gateway translates the response back to the instance’s private IP address and sends it back to the instance.

By using a NAT Gateway, you can allow instances in a private subnet to access resources on the internet, such as software updates, patches, or external services, while at the same time, maintaining security by preventing the internet from initiating connections with those instances.

## Types

1. Public (default)
2. Private

### Public NAT

- **Purpose:**  
    Allows **private subnet instances** to initiate outbound connections to the internet (e.g., software updates, API calls) while hiding their private IP.
- **Where it’s placed:**  
    In a **public subnet** (has a route to an Internet Gateway).
- **How it works:**
    - Private subnet → routes traffic to **Public NAT Gateway** in a public subnet → NAT Gateway sends it out via the **Internet Gateway**.
    - Return traffic comes back to the NAT Gateway → the NAT Gateway forwards it to the originating private instance.    
- **Has an Elastic IP (public IP)** assigned to it.
- **Internet-facing** (but it only allows return traffic for connections initiated from inside).
### Private NAT

- **Purpose:**  
    Allows **private subnet instances** to access resources in **other VPCs or on-premises networks** over **private connections** (like VPC Peering, Transit Gateway, Direct Connect) **without exposing them to the internet**.
- **Where it’s placed:**  
    In a **private subnet** (no Internet Gateway route).
- **How it works:**
    - Private subnet → routes traffic to **Private NAT Gateway** → forwards traffic to a private destination via VPC peering or Transit Gateway.
- **No public IP** — all traffic is private.
- Used for **private-to-private network translations**, often when two networks have overlapping IP ranges and translation is required.

|Feature|Public NAT Gateway|Private NAT Gateway|
|---|---|---|
|**Internet access**|✅ Yes (outbound only)|❌ No|
|**Placement**|Public subnet|Private subnet|
|**IP type**|Public IP (Elastic IP)|Private IP only|
|**Use case**|Private subnet → Internet|Private subnet → Private network
![[Pasted image 20250809235223.png]]

---

# How NAT works?

- [1] The main use case of NAT Gateway is to allow you to have Internet access in private subnets of your [Virtual Private Cloud](https://www.geeksforgeeks.org/overview-to-the-amazon-virtual-private-cloud/). This way your instances still can’t be accessed from the Internet but the instances themselves can access the Internet. So, you have Internet access without having a risk of being hacked through publicly accessible instances.

**NAT Gateway entry in Route Table** — In the private route table you need to make an entry for the NAT Gateway so that traffic generated from private instances can be routed via this route to the internet.

> [!important] 
> By default, users do not have permission to work with NAT gateways. You can create an IAM role with a policy attached that grants users permission to create, describe, and delete NAT gateways.

---

> [!Warning]
> After completion of your practice on the NAT Gateway you have to delete it to avoid incurring charges. Remember when you provision a NAT gateway, you are charged for each hour that your NAT gateway is available and each gigabyte of data that it processes.
