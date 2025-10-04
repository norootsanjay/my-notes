# Private IP & DNS Naming Inside AWS

==When you launch an EC2 instance in a subnet== (public or private), AWS ==always assigns it a private IPv4 address== from that subnet’s IP range.

> [!Example]
> In subnet `10.0.100.0/24`, an instance might get `10.0.100.10`.

- AWS automatically creates a private DNS hostname for that IP.
> [!Example] 
ip-10-0-100-10.ec2.internal   (for AWS US East region)
ip-10-0-100-10.ec2.internal   (varies by region)

These DNS names are ==internal only== — they resolve to the ==private IP== and can only be used inside the VPC (or peered VPCs with DNS resolution enabled).

---

# Public DNS Naming

If your instance is in a ==public subnet== and has a ==public IP== (or Elastic IP), AWS can also give it a ==public DNS name:==
> [!Example]
> ec2-203-0-113-25.compute-1.amazonaws.com

**But here’s the catch:** AWS ==only assigns public DNS hostnames if “DNS Hostnames” is enabled== for your VPC.
- If it’s off, even with a public IP, there will be no public DNS name — you’ll have to use the IP directly.

---

# How AWS DNS Servers work in VPC

AWS provides DNS resolution inside your VPC without you having to set up your own DNS infrastructure.

**Two ways to reach the AWS-provided DNS server:**
1. _Special link-local address: `169.254.169.253`
        - This is the same in all VPCs, globally accessible to resources in that VPC.
            
2. _Second IP address of your VPC CIDR block:_
    - Example:
	    - If your VPC CIDR = `10.10.0.0/16`, AWS DNS = `10.10.0.2`
         - If your VPC CIDR = `10.20.0.0/16`, AWS DNS = `10.20.0.2`
    - This is easier for some configurations because it’s in the same IP range as your resources.
            
**Purpose:**
- These servers resolve:        
    - AWS internal hostnames → private IPs
    - Public domain names → public IPs
    - Any custom domains you set up in ==Route 53== if the resolver is pointed there.

---

# Key VPC Settings for DNS

 **a. Enable DNS Support**
- Controls ==whether your VPC can resolve DNS at all== using AWS’s internal DNS servers.
- **Default:** ON
- If **off**:
    - Any DNS query from inside your VPC to `10.x.x.2` or `169.254.169.253` will fail.
    - You’d need your own DNS server to resolve anything.
        
 **b. Enable DNS Hostnames**
- Controls ==whether instances with public IPs get public DNS names==.
- **Default for custom VPCs:** OFF  
  **Default for default VPC:** ON    
- If **off**:
    - Private IPs will still have private DNS names.     
    - Public IPs will ==not== get public DNS hostnames — you’ll only be able to reach them via the IP.

---

| Step | Instance Location | Private IP | Public IP   | Private DNS | Public DNS                      | Required Settings              |
| ---- | ----------------- | ---------- | ----------- | ----------- | ------------------------------- | ------------------------------ |
| 1    | Public subnet     | 10.0.1.25  | 18.200.5.10 | ✅ Yes       | ✅ Yes (if DNS Hostnames ON)     | DNS Hostnames + DNS Support ON |
| 2    | Public subnet     | 10.0.1.30  | 18.200.5.15 | ✅ Yes       | ❌ No (if DNS Hostnames OFF)     | DNS Support ON                 |
| 3    | Private subnet    | 10.0.2.15  | None        | ✅ Yes       | ❌ No                            | DNS Support ON                 |
| 4    | Private subnet    | 10.0.2.18  | None        | ✅ Yes       | ❌ No (public IP missing anyway) | DNS Support ON                 |
![[Screenshot 2025-08-10 at 12.35.07 AM.png]]
