
# Configure IPv4 and IPV6 Networking Hostname Resolution

## Understanding IP Addresses

Every devices connected to a network requires an IP address.
	1. IPv4
	2. IPv6

###  IPv4

- Composed of four decimal numbers separated by dots.
- Each octet ranges from 0-255 because it represents 8 bits 2^8 possible values.
> [!Example]
> 192.168.1.10

#### CIDR Notation in IPv4

- Classless Inter-Domain Routing notation.
>[!Example]
>192.168.1.101/24 means the first 24 bits of the address denote the network prefix, while the other indicate the specific host (in this case 101)

![[Pasted image 20251004195042.png]]

> [!Note]
> With a /24 network, IP addresses from 192.168.1.0 to 192.168.1.255 belongs to the same network.
> But not 192.168.2.10

![[Pasted image 20251004195303.png]]

### IPv6 Addressing

- 128 bit addresses

**Key Differences between IPv4 and IPv6**

1. *Grouping*: has 8 groups. (unlike IPv4 having 4).
2. *Numerical System*: 0-9, A-F.
3. *Separator*: colons.
>[!Example]
>`2001:0db8:0000:0000:ff00:0042:8329`

![[Pasted image 20251004195713.png]]

#### CIDR Notation in IPv6

>[!Example]
`2001:0db8:0000:0000:0000:ff00:0042:8329/64`

![[Pasted image 20251004195850.png]]

| **IP Version** | **Notation Example**                  | **Key Characteristics**                                            |
| -------------- | ------------------------------------- | ------------------------------------------------------------------ |
| IPv4           | 192.168.1.101/24                      | 32-bit address, separated by dots, with each octet ranging 0–255   |
| IPv6           | 2001:0db8:0000:0000:ff00:0042:8329/64 | 128-bit address, organized in 8 hexadecimal groups and abbreviated |

---

# Configure IPv4 and IPv6 Networking and  Hostname Resolution Demo

>  For Ubuntu machine

```bash
ip link
```

Displays all the comprehensive details about your network configurations.
![[Pasted image 20251004201141.png]]

- lo: loop back interface used for local communication. (e.g,. connecting to a database on 127.0.0.1)
- enp0s3: physical ethernet connection.
- wlp0s20f3: wifi

```bash
ip -c a
```

## Activating an Interface

```bash
sudo ip link set dev lxb40 up
```
- set: configure a property
- dev: device
- up: bringing up the device

## Manually adding/removing IP Addresses

```bash
# IPv4
sudo ip addr add 10.0.0.40/24 dev enp0s8
sudo ip addr delete 10.0.0.40/24 dev enp0s8
sudo ip link set dev enp0s8 down
# IPv6
sudo ip a add fe80::921b:eff:fe3d:abcd/64 dev enp0s8
sudo ip a delete fe80::921b:eff:fe3d:abcd/64 dev enp0s8
sudo ip link set dev enp0s8 down
```

>[!Important]
>However, these changes are not permanent.
>To make permanent changes, we need to configure netplan

## Making Permanent Changes with Netplan

> [!Note]
> - Permanent network configurations on Ubuntu Server are managed by Netplan.
> - It reads the configuration files from /etc/netplan and instructs lower level networking services like systemd-networkd to configure the system.

> To view the current netplan configuration:
```bash
sudo netplan get
```

```bash
ls /etc/netplan
```

### Creating a new Netplan Configuration file

- Files are processed in alphabetical order.

> Netplan configuration file:
```yaml
network:
  ethernetes:
    enp0s8:
      dhcp4: false
      dhcp6: false
      addresses:
        - 10.0.0.9/24
        - fe80::921b:eff:fe3d:abcd/64
  version: 2
```

```bash
sudo netplan try
```

>[!Warning]
> If you see a warning like:
> Permissions for /etc/netplan/99-mysettings.yaml are too open. Netplan configuration should NOT be accessible by others.
> Then adjust the file permissions with: 
> ```
> sudo chmod 600 /etc/netplan/99-mysettings.yaml
> ```

### Adding DNS Resolvers and Routes in Netplan

>Further customization in network settings in Netplan by adding DNS resolvers and network routes:
```yaml
network:
  ethernets:
    enp0s8:
      dhcp4: false
      dhcp6: false
      addresses:
        - 10.0.0.9/24
        - fe80::921b:eff:fe3d:abcd/64
      nameservers:
        addresses:
          - 8.8.4.4
          - 8.8.8.8
      routes:
        - to: 192.168.0.0/24
          via: 10.0.0.100
        - to: default
          via: 10.0.0.1
  version: 2
```

```bash
sudo netplan try
```

>After confirmation, verify the routes with:
```bash
ip route
```

>To check the configured DNS resolvers, run:
```bash
resolvectl status
```

>Configuring global resolvers: edit `/etc/systemd/resolved.conf` by uncommenting the DNS line and adding your desired DNS addresses:
```text
DNS=1.1.1.1 9.9.9.9
```

```bash
sudo systemctl restart systemd-resolved.service
```

```bash
resolvectl status
resolvectl dns
```

## Configuring Local Hostname Resolution

> Edit `/etc/hosts` file

==127.0.123.123 dbserver ==

```bash
ping dbserver
```

>Example configurations are also provided in `/usr/share/doc/netplan/examples`.

---

# Start Stop and Check status of Network Services


## Viewing Active Network Connections

1. ss
2. netstat

```bash
sudo ss -tunlp 
```
> [!Options]
> - l: Only display sockets currently listening for connections.
> - t: TCP connection
> - u: UDP connection
> - n: numeric values (port numbers) instead of resolving service names
> - p: display the process using each socket

![[Pasted image 20251004214839.png]]
An IP address like 127.0.0.1 in the Local Address column indicates that the service is only accepting connections from the local machine (localhost). For example, a web server (NGINX) connecting to a database server (MariaDB) on the same machine typically uses 127.0.0.1 on port 3306.

## Stopping and Disabling Services

```bash
sudo systemctl stop ssh.service
sudo systemctl disable ssh.service
```

---

# Configure Bridge and Bonding Devices Theory

Networking techniques:
1. Bridging
2. Bonding

> [!Tip]
> - Bridging: Connecting networks
> - Bonding: Combining interfaces

![[Pasted image 20251004215453.png]]

## Bridging Network Devices

![[Pasted image 20251004215516.png]]
- A ==virtual bridge== interconnects multiple network interfaces.
- Devices on different physical networks communicate as if on the same LAN.
- Interfaces added to the bridge = *ports*.
>[!Example] 
Server with 2 NICs bridged → both networks can talk without routing.

## Bonding Network Devices

- Combines multiple NICs into ==one logical interface (bond)==.
- **Benefits:**
    - Resilience → stays connected if one NIC fails.
    - Higher throughput → aggregates bandwidth.        
    - Reliability → traffic shifts to healthy NIC.
>[!Example] 
>Server with 2 NICs bonded → appears as 1 interface, auto-failover on failure.

![[Pasted image 20251004224223.png]]
![[Pasted image 20251004224228.png]]

| **Mode** | **Name**                               | **Description**                                                           | **Key Benefit**                |
| -------- | -------------------------------------- | ------------------------------------------------------------------------- | ------------------------------ |
| *0*      | Round Robin                            | Packets sent sequentially across all NICs                                 | Max throughput                 |
| *1*      | Active Backup                          | Only one NIC active, others are standby                                   | High availability (failover)   |
| *2*      | XOR                                    | Chooses NIC based on src/dst MAC                                          | Load balancing per connection  |
| *3*      | Broadcast                              | Sends packets on all NICs simultaneously                                  | Fault tolerance                |
| *4*      | IEEE 802.3ad (LACP)                    | Uses dynamic link aggregation to combine interfaces, improving throughput | High throughput + redundancy   |
| *5*      | Adaptive Transmit Load Balancing (TLB) | Distributes outgoing traffic by selecting the least busy interface        | Better TX load distribution    |
| *6*      | Adaptive Load Balancing (ALB)          | Balances both incoming & outgoing traffic                                 | Full load balancing + failover |

| **Feature**    | **Bridging**                                              | **Bonding**                                                       |
| -------------- | --------------------------------------------------------- | ----------------------------------------------------------------- |
| *Purpose*      | Connects multiple networks into one                       | Combines multiple NICs into one logical interface                 |
| *Layer*        | Layer 2 (Data Link)                                       | Layer 2 (Data Link)                                               |
| *How it works* | Forwards traffic based on ==MAC addresses==               | Aggregates/merges NICs for throughput & redundancy                |
| *Use case*     | Server with 2 NICs → connect two networks without routing | Server with 2 NICs → increase speed or failover                   |
| *Result*       | Devices on different networks act as if on same LAN       | Single virtual interface with higher reliability and/or bandwidth |

## Demo

### Inspecting the Bridge Example

Begin by reviewing the example bridge configuration file located at `/usr/share/doc/netplan/examples/bridge.yaml`:

```bash
ls /usr/share/doc/netplan/examples/

cat /usr/share/doc/netplan/examples/bridge.yaml
```
```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    enp3s0:
      dhcp4: no
  bridges:
    br0:
      dhcp4: yes
      interfaces:
        - enp3s0
```

This configuration defines a single Ethernet interface (`enp3s0`) with DHCP disabled, and a bridge (`br0`) that obtains an IP address via DHCP. This file serves as our starting template.

>[!Note]
Before making any modifications, verify that the network interfaces referenced in the configuration match your system’s hardware.

### Copying and Securing the Configuration File

>Copy the example file to the Netplan configuration directory and set secure permissions:
```bash
sudo cp /usr/share/doc/netplan/examples/bridge.yaml /etc/netplan/99-bridge.yaml
sudo chmod 600 /etc/netplan/99-bridge.yaml
```

### Identifying Network Interfaces

>Determine the network interface names in your system with:
```bash
ip -c link
```

### Editing the Netplan File for Bridging

Update the Netplan configuration to create a bridge that combines `enp0s8` and `enp0s9`.

```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    enp0s8:
      dhcp4: no
    enp0s9:
      dhcp4: no
  bridges:
    br0:
      dhcp4: yes
      interfaces:
        - enp0s8
        - enp0s9
```

>Verify the network interface statuses with:
```bash
ip -c link
```

### Reverting the Bridge Configuration

To remove the bridge configuration and free up the network interfaces, simply delete the YAML file and remove the bridge interface:

```bash
sudo ip link delete br0
```

>[!Tip]
For a complete reset of network settings in a production environment, consider rebooting the system after deleting the configuration.

### Configuring Network Bonding

Configure network bonding to provide redundancy and load balancing. Begin by copying the bonding example file and setting secure permissions:

```bash
sudo cp /usr/share/doc/netplan/examples/bonding.yaml /etc/netplan/99-bond.yaml
sudo chmod 600 /etc/netplan/99-bond.yaml
```

Edit the bonding configuration file (`/etc/netplan/99-bond.yaml`) using your favorite text editor. The corrected configuration below ensures that the Ethernet interfaces are properly defined:

```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    enp0s8:
      dhcp4: no
    enp0s9:
      dhcp4: no
  bonds:
    bond0:
      dhcp4: yes
      interfaces:
        - enp0s8
        - enp0s9
      parameters:
        mode: active-backup
        primary: enp0s8
        mii-monitor-interval: 100
```

In this configuration:

- The Ethernet interfaces `enp0s8` and `enp0s9` have DHCP disabled.
- A bond named `bond0` is configured with DHCP enabled.
- The bonding mode is set to active-backup (Mode 1) with `enp0s8` designated as the primary interface.
- The MII monitor checks link status every 100 milliseconds.

>Apply the new Netplan configuration:
```
sudo netplan apply
```

>After applying the changes, confirm the bonding setup with:
```bash
ip -c link
```

This output indicates that both `enp0s8` and `enp0s9` are slaves of `bond0`, which is receiving its IP configuration via DHCP.

#### Viewing Bond Details

>To inspect the bond’s details, read the bond status file:
```
cat /proc/net/bonding/bond0
```

This file provides comprehensive information about the bond’s parameters and operating status.

### Managing Bonded Interfaces

Bonded interfaces can be managed like regular Ethernet interfaces. For example, to bring the bond interface down and assign a static IP address, use:

```bash
sudo ip link set dev bond0 down
sudo ip addr add 10.0.0.9/24 dev bond0
```

>[!Important]
Changes made with the IP command are temporary and will be reset after a system reboot.

# Configure Packet Filtering Firewall

