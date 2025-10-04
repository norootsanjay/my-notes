- [n] **A _load balancer_ serves as the single point of contact for clients**.

- Load balancers distribute incoming traffic across multiple targets to ensure no single server is overwhelmed.
- It improves availability by sending traffic only to healthy targets and scalability by handling various loads.
- It can also route traffic across multiple AZs for fault tolerance.
![[Pasted image 20250810084125.png]]

AWS provides built-in load balancing services under the Elastic Load Balancer umbrella, which directs traffic to various backend resources efficiently.

A ==listener checks for connection requests from clients==, using the protocol and port that you configure. The rules that you define for a listener determine how the load balancer routes requests to its registered targets. Each rule consists of a priority, one or more actions, and one or more conditions. When the conditions for a rule are met, then its actions are performed. You must define a default rule for each listener, and you can optionally define additional rules.

Each ==target group routes requests to one or more registered targets==, such as EC2 instances, using the protocol and port number that you specify. You can register a target with multiple target groups. You can configure health checks on a per-target-group basis. Health checks are performed on all targets registered to a target group that is specified in a listener rule for your load balancer.

The following diagram illustrates the basic components. Notice that each listener contains a default rule, and one listener contains another rule that routes requests to a different target group. One target is registered with two target groups.

![[Pasted image 20250810094211.png]]

---

# Types of Elastic Load Balancers

1. Classic Load Balancer
2. Application Load Balancer 
3. Network Load Balancer
4. Gateway Load Balancer

With ALBs, NLBs, and GLBs, you register targets in the target group and route traffic to the target group.
With Classic Load Balancer, you register instances with the load balancer.

### Cross-Zone Load Balancing

- When cross-zone load balancing is ==enabled==, each load balancer node ==distributes traffic across the registered targets in all enabled Availability Zones==.
- When cross-zone load balancing is ==disabled==, each load balancer node ==distributes traffic only across the registered targets in its Availability Zone==.
## Classic Load Balancer

- Limited feature,
- Only supports a single SSL certificate.
If you need to manage two separate applications, each with its own SSL certificate, behind the same load balancer, the Classic Load Balancer falls short.![[Pasted image 20250810091133.png]]

## Application Load Balancer

- Optimized for web-based application (HTTP, HTTPS, WebSockets).
- Operates on Layer 7.
- Cross-Zone Load Balancing is always enabled.
It intelligently distributes incoming HTTP and HTTPS traffic to target instances, containers, or IP addresses based on the rules and configurations set by the user.

- **Best for:** HTTP/HTTPS applications, microservices, containerized apps.
- **Features:**
    - Host-based routing (e.g., `api.example.com` vs `shop.example.com`).
    - Path-based routing (`/images` → one target group, `/api` → another).
    - Native WebSocket support.
    - Supports ==Lambda functions== as targets (serverless integration).
    - Integrates with AWS WAF for security filtering.

![[Pasted image 20250810094512.png]]

> [!Hint]
> If the question talks about routing based on **content**, choose **ALB**.

## Network Load Balancer

- Functions at the fourth layer.
- It can handle millions of requests per second.
- Designed to handle TCP, UDP, and TLS traffic.
- It is highly scalable, offers low latency, and is suitable for applications that require high performance and extreme scalability.
- Preserves client IP address (important for compliance/security).
- Supports ==Elastic IP addresses== for static endpoints
- TLS termination supported (offloading encryption).

![[Pasted image 20250810094526.png]]

> [!Hint]
> If the scenario involves **millions of connections**, **static IPs**, or **low latency**, choose **NLB**.

## Classic Load Balancer

- _CLB_ operates at both the application layer (Layer 7) and transport layer (Layer 4) of the OSI model and is ideal for applications that require basic load-balancing features.
- CLB provides basic load-balancing capabilities for distributing incoming traffic across multiple Amazon EC2 instances
- Simple health checks (TCP, HTTP, HTTPS).

> [!hint]
> Rarely is the answer unless **migrating from old infra** or "legacy" is explicitly mentioned.

## Gateway Load 

- **Best for:** Deploying, scaling, and managing ==virtual appliances== (firewalls, IDS/IPS, packet analyzers).
- Works at Layer 3 with the ==GENEVE== protocol (port 6081).
- Transparent — passes original source/destination IPs.
- Scales appliances automatically.
- It maintains the stickiness of flows to a specific target appliance using a 5-tuple (for TCP/UDP flows) or 3-tuple (for non-TCP/UDP flows).

> [!Hint]
> If the question mentions **firewall** or **traffic inspection appliances**, choose **GWLB**.

![[Screenshot 2025-08-10 at 9.43.32 AM.png]]

---

# How Elastic Load Balancer Works?

[[ELB-Working.excalidraw]]
![[Pasted image 20250810104431.png]]

---

# Cross-Zone Load Balancing


![[Screenshot 2025-08-10 at 10.45.26 AM.png]]

---

# Load Balancer Deployment Modes

1. Private: Access by users within the AWS Organization.
2. Public: Access by users across the Public Internet.
![[Screenshot 2025-08-10 at 11.12.29 AM.png]]

---

# ELB Architecture

![[Screenshot 2025-08-10 at 11.46.22 AM.png]]

---

# Listeners and Target Groups

When configuring an Elastic Load Balancer, two key components that define its behaviour are listeners and target groups:

1. Listeners: A listener is a process that ==checks for incoming connection== requests based on a specified protocol and port. For example, if you want to handle requests for =="app1.com"==, a listener can be configured to detect those requests and forward them appropriately.
2. Target Groups: A target group comprises a set of registered targets—such as EC2 instances, ECS containers, or Lambda functions—that receive the forwarded traffic according to the listener's rules. You can configure health checks for each target group to ensure only healthy targets receive traffic. For instance, one target group might manage two EC2 instances for "app1.com", while separate target groups could serve endpoints like "app2.com/auth" for ECS containers or "app2.com/cart" for Lambda functions.![[Pasted image 20250810124316.png]]

> [!Note]
> Listeners use defined rules to match incoming requests to route them to the appropriate target group.
> Health checks ensure that traffic is only sent to targets that are operational.

