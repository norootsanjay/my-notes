# Overview of Firewalls

Consider a web server listening on port 443 (HTTPS). When a client sends a request, the firewall controls the flow of data by applying a set of predefined inbound (incoming traffic) and outbound (outgoing traffic) rules. There are two primary types of firewalls:

- **Stateless Firewalls:**  
    Evaluate each packet individually. Rules must be configured for both inbound and outbound traffic as there is no tracking of connection states.![[Pasted image 20250810005343.png]]
    
- **Stateful Firewalls:**  
    Monitor active connections. When an inbound request is allowed, the corresponding outbound response is automatically permitted without the need for an explicit rule.![[Pasted image 20250810005400.png]]

---

# Network Access Control Lists (NACLS)

- Subnet-level filtering.
- Stateless Behaviour.
![[Pasted image 20250810005808.png]]

NACL rules offer greater control by allowing both allow and deny actions:

- **Rule Order:**  
    Each rule is assigned a number, and lower-numbered rules are evaluated before higher ones.
- **Allow and Deny Actions:**  
    NACLs can explicitly allow or block traffic based on type, protocol, port range, and source address, offering granular control over subnet traffic.
![[Pasted image 20250810010057.png]]

---
# Security Groups (SG)

- Resource level protection.
- Stateful behaviour.
- Allow rules only.
> [!info]
If no rules are defined, all traffic is blocked by default. Also, when multiple security groups are attached to a resource, their rules merge to form a consolidated security policy.


![[Pasted image 20250810005917.png]]
When configuring security group rules in the AWS console, you will encounter separate sections for inbound and outbound traffic. Here’s a breakdown of the typical fields within a security group rule:

- **Name:**  
    A descriptive name that helps identify the rule.
- **Rule ID:**  
    A unique identifier for the rule.
- **IP Version:**  
    Indicates whether the rule applies to IPv4 or IPv6 traffic.
- **Type, Protocol, and Port Range:**  
    For example, selecting "HTTP" sets the rule to allow TCP traffic on port 80.
- **Source/Destination:**  
    Specifies the IP address or range permitted by the rule. For instance, 0.0.0.0/0 allows traffic from any IP, while specifying an IP like 1.1.1.1 restricts access.
- **Description:**  
    Explains the rule’s purpose.
    
Consider these examples:

1. **HTTP Traffic Rule:**  
    Permits HTTP (TCP port 80) traffic from any IP address.
2. **Custom TCP Rule:**  
    Configured as a "custom TCP" rule, it allows traffic on port 200 only from IP address 1.1.1.1. If a range (e.g., ports 200 through 300) is required, the rule can be configured accordingly.
![[Pasted image 20250810010157.png]]
![[Pasted image 20250810010202.png]]
