> [!Hint]
> Public IP address: Temporary
Elastic IP: Permanent

A static Elastic IP remains constant, ensuring that even if your EC2 instance is rebooted or relocated to a different physical server, the IP address stays the same.

# What are Elastic IPs

Elastic IP addresses are static IPv4 addresses allocated to your AWS account. Once allocated, the address is reserved exclusively for your use and cannot be allocated to others. You can then associate an Elastic IP with an EC2 instance so that it keeps the same IP address, regardless of reboots or host migration.

## Key Benefits

- **Consistent Connectivity:** End users always connect to the same IP, improving reliability.
- **Flexibility in Maintenance:** You can easily reassign the Elastic IP from one server to another during scheduled maintenance or in the event of unexpected server issues.
![[Pasted image 20250810003821.png]]

---

# Pricing

| Scenario                                                                | Pricing (USD per Hour)         | Notes                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| ----------------------------------------------------------------------- | ------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Primary Elastic IP associated with a running EC2 instance**           | $0.00                          | No cost when only one EIP is attached and instance is running ([Stack Overflow](https://stackoverflow.com/questions/10051533/aws-ec2-elastic-ips-bandwidth-usage-and-charges?utm_source=chatgpt.com "amazon web services - AWS EC2 Elastic IPs bandwidth usage and charges - Stack Overflow"), [AWS Documentation](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/elastic-ip-addresses-eip.html?utm_source=chatgpt.com "Elastic IP addresses - Amazon Elastic Compute Cloud")).   |
| **Additional (secondary) EIP on a running EC2 instance**                | $0.005                         | Charged for each extra EIP beyond the first ([Stack Overflow](https://stackoverflow.com/questions/10051533/aws-ec2-elastic-ips-bandwidth-usage-and-charges?utm_source=chatgpt.com "amazon web services - AWS EC2 Elastic IPs bandwidth usage and charges - Stack Overflow")).                                                                                                                                                                                                          |
| **Elastic IP not associated with any running instance (idle)**          | $0.005                         | Idle EIP incurs hourly charge ([Stack Overflow](https://stackoverflow.com/questions/10051533/aws-ec2-elastic-ips-bandwidth-usage-and-charges?utm_source=chatgpt.com "amazon web services - AWS EC2 Elastic IPs bandwidth usage and charges - Stack Overflow"), [AWS Documentation](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/elastic-ip-addresses-eip.html?utm_source=chatgpt.com "Elastic IP addresses - Amazon Elastic Compute Cloud")).                                   |
| **All public IPv4 addresses (EIPs, AWS-provided, etc.)—in use or idle** | $0.005                         | Charged per IP per hour starting Feb 1, 2024, across AWS services ([Amazon Web Services, Inc.](https://aws.amazon.com/blogs/aws/new-aws-public-ipv4-address-charge-public-ip-insights/?utm_source=chatgpt.com "New – AWS Public IPv4 Address Charge + Public IP Insights \| AWS News Blog"), [interlir.com](https://interlir.com/2024/03/17/aws-charging-for-public-ipv4-addresses/?utm_source=chatgpt.com "AWS Charging for Public IPv4 Addresses - Interlir networks marketplace")). |
| **AWS Free Tier—first 12 months**                                       | Included (up to 750 hrs/month) | First 750 hours of public IPv4 usage (including EIP usage) are free for new accounts under Free Tier ([Amazon Web Services, Inc.](https://aws.amazon.com/blogs/aws/new-aws-public-ipv4-address-charge-public-ip-insights/?utm_source=chatgpt.com "New – AWS Public IPv4 Address Charge + Public IP Insights \| AWS News Blog"), [Reddit](https://www.reddit.com/r/aws/comments/1ecm37a?utm_source=chatgpt.com "Am I charged for the unused VPC IPv4 address?")).                       |
| **Amazon-provided contiguous IPv4 block (e.g., /30 or /29 blocks)**     | $0.008 per IP per hour         | Charged for each IP in block regardless of usage status ([Amazon Web Services, Inc.](https://aws.amazon.com/vpc/pricing//?utm_source=chatgpt.com "Logically Isolated Virtual Network - Amazon VPC Pricing - Amazon Web Services")).                                                                                                                                                                                                                                                    |

---

# Regional Specificity and IP Origins

Elastic IPs are tied to specific AWS regions and cannot be transferred across regions. They can be associated only with EC2 instances within the same region. Additionally, these IP addresses are sourced either from AWS’s pool of IPv4 addresses or from a custom pool you bring to your account. The infographic below reinforces these points:

![The image is an infographic about Elastic IPs, highlighting that they are specific to a region and come from Amazon's pool of IPv4 addresses.](https://kodekloud.com/kk-media/image/upload/v1752865553/notes-assets/images/AWS-Solutions-Architect-Associate-Certification-Elastic-IP/elastic-ips-infographic-amazon-ipv4.jpg)
