When deciding whether a subnet should be public or private, ask yourself: Should internet devices interact directly with the resources deployed on the subnet? If the answer is yes, that subnet should be public; if no, it should be private.

For instance, consider a web server that serves content to internet users. This web server should reside in a public subnet. In contrast, a backend database that stores sensitive data must not be directly accessible from the internet. Instead, the database should be placed in a private subnet, where only trusted resources—like the aforementioned web server—can access it.

![The image illustrates the use of private and public subnets within a VPC, showing a web server in a public subnet connected to the internet and a database in a private subnet.](https://kodekloud.com/kk-media/image/upload/v1752865652/notes-assets/images/AWS-Solutions-Architect-Associate-Certification-Private-and-Public-Subnets/vpc-private-public-subnets-diagram.jpg)

> [!Key Design Principle]
> Place internet-facing resources, such as web servers, in public subnets while keeping sensitive backend services like databases in private subnets to ensure enhanced security.

In practice, this configuration means that end users interact with a public-facing web server, which in turn securely communicates with a private database. This setup prevents direct internet access to the database, significantly reducing potential attack vectors.

Another common scenario involves extending a private on-premises data center into AWS. In such cases, the cloud resources are treated as an extension of your existing private network and are typically deployed in private subnets. A secure VPN connection links your on-premises data center to the AWS infrastructure, thereby eliminating the need to expose these resources to the internet.

![The image illustrates a use case for a private subnet, showing a connection from a private data center to an AWS private subnet via a VPN.](https://kodekloud.com/kk-media/image/upload/v1752865653/notes-assets/images/AWS-Solutions-Architect-Associate-Certification-Private-and-Public-Subnets/private-subnet-vpn-connection-aws.jpg)