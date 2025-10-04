
**nginx.conf**: It is the main configuration file for nginx. Typically located at `/etc/nginx/nginx.conf` on Linux systems.

At a glance, an nginx.conf file is divided into four main sections:
1. Global settings
2. events block
3. http block
4. server block 
## nginx.conf Structure

1. **Global settings**  
    Define user permissions, worker processes, PID file location, compression, caching, and more.
2. **events block**  
    Controls Nginx’s event model and the maximum number of simultaneous connections per worker.
3. **http block**  
    Contains HTTP directives for logging, timeouts, compression, MIME types, and includes for server blocks.
4. **server block**  
    Configures how Nginx responds to requests for specific domain names or IP addresses (virtual hosts).
```nginx.conf
user www-data;
worker_processes auto;
pid /run/nginx.pid;

events {
    worker_connections 1024;
}

http {
    sendfile        on;
    tcp_nopush      on;
    tcp_nodelay     on;
    keepalive_timeout 65;
    types_hash_max_size 2048;
    gzip            on;


    log_format main '$remote_addr - $remote_user [$time_local] '
                    '"$request" $status $body_bytes_sent '
                    '"$http_referer" "$http_user_agent"';


    include /etc/nginx/mime.types;
    default_type application/octet-stream;


    # Include virtual host definitions
    include /etc/nginx/conf.d/*.conf;
    include /etc/nginx/sites-enabled/*;
}
```

**Key directives explained:**

- user
    System user for Nginx worker processes (e.g., www-data on Debian/Ubuntu).
- worker_processes
    Number of worker processes—*auto* matches CPU cores.
- pid
    Path to the master process PID file.
- events → worker_connections
    The maximum simultaneous connections each worker can handle.
- http
    Configures HTTP-level settings: compression (gzip), timeouts, logging formats, MIME types, and includes server blocks.

## Server Blocks (Virtual Hosts)

Server blocks, or virtual hosts, let you ==host multiple domains on one Nginx instance==. 

![The image illustrates the concept of creating and editing server blocks (virtual hosts), showing multiple websites being hosted on a single server.](https://kodekloud.com/kk-media/image/upload/v1752882311/notes-assets/images/Nginx-For-Beginners-Nginx-Overview/server-blocks-virtual-hosts-diagram.jpg)
Incoming requests are routed to the matching block based on the *server_name* or IP *address*.
![The image illustrates the process of creating and editing server blocks (virtual hosts) in NGINX, showing a user request directed to NGINX with references to "server_name" and "IP address."](https://kodekloud.com/kk-media/image/upload/v1752882312/notes-assets/images/Nginx-For-Beginners-Nginx-Overview/nginx-server-blocks-creation-editing.jpg)

### Example: Basic HTTP Server Block

```nginx.conf
server {
    listen 80;
    server_name example.com www.example.com;

    root /var/www/example.com/html;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

**Directive breakdown**:

- `listen`  
    Port for incoming traffic (`80` for HTTP, `443` for HTTPS).
- `server_name`  
    Domain names or IP addresses handled by this block.
- `root`  
    Path to the website’s document root.
- `index`  
    Default file(s) served when a directory is requested.
- `location /`  
    URI matching; `try_files` checks for existing files or directories and returns a 404 if not found.

>[!Warning]
After editing any server block, always run `nginx -t` to validate your configuration and avoid downtime.

## Nginx Directory Structure

Nginx uses a standardized directory layout for its configuration files and web content. Below is a quick reference of the most common paths:

| **Path**                      | **Description**                                               |
| ----------------------------- | ------------------------------------------------------------- |
| `/etc/nginx/nginx.conf`       | Main configuration file                                       |
| `/etc/nginx/sites-available/` | Store individual server block files                           |
| `/etc/nginx/sites-enabled/`   | Symbolic links to enabled sites from _sites-available_        |
| `/etc/nginx/conf.d/`          | Additional configuration snippets (e.g., SSL, load balancing) |
| `/var/www/...`                | Default web content roots (Debian/Ubuntu)                     |
| `/usr/share/nginx/html`       | Default web root (RHEL/CentOS)                                |
| `/etc/nginx/mime.types`       | MIME type definitions                                         |
| `/run/nginx.pid`              | PID file location                                             |
| `/var/log/nginx/`             | Access and error logs                                         |

## Essential Nginx Commands

Use these commands to inspect, test, and manage your Nginx server:

| **Command**                    | **Description**                         |
| ------------------------------ | --------------------------------------- |
| `nginx -h`                     | Display help and available options      |
| `nginx -v`                     | Show Nginx version                      |
| `nginx -V`                     | Show version and compile-time options   |
| `nginx -t`                     | Check configuration syntax and validity |
| `nginx -T`                     | Dump complete configuration for review  |
| `nginx -s reload`              | Reload configuration without downtime   |
| `nginx -s stop`                | Graceful shutdown                       |
| `nginx -s quit`                | Immediate shutdown                      |
| `nginx -s reopen`              | Reopen log files                        |
| `sudo systemctl reload nginx`  | Reload using systemd (graceful)         |
| `sudo systemctl restart nginx` | Restart Nginx (brief downtime possible) |
| `sudo systemctl status nginx`  | Check Nginx service status              |

## Firewall and Ports

A firewall is a security barrier—hardware or software—between your system and the Internet. It inspects and filters incoming and outgoing traffic, blocking unauthorized access while permitting legitimate communication.

![The image illustrates a firewall acting as a barrier between a computer system and the internet, with a visual representation of a server, monitor, and a router.](https://kodekloud.com/kk-media/image/upload/v1752882295/notes-assets/images/Nginx-For-Beginners-Firewall-Ports/firewall-barrier-server-monitor-router.jpg)

Much like a home security system that monitors all “doors and windows,” a firewall triggers alarms when it detects suspicious activity. Hardware firewalls operate on the same principle but are deployed in network appliances or data centers.

### Built-In Firewalls Across Operating Systems

#### Debian & Ubuntu (UFW)

Debian and Ubuntu include the Uncomplicated Firewall (UFW), which is **disabled by default**, permitting all traffic until activated.

![The image shows the logos of Debian and Ubuntu with a note about the Uncomplicated Firewall (UFW), indicating it is off by default but can be enabled to restrict traffic.](https://kodekloud.com/kk-media/image/upload/v1752882296/notes-assets/images/Nginx-For-Beginners-Firewall-Ports/debian-ubuntu-ufw-logos.jpg)

#### Red Hat & Fedora (firewalld)

Red Hat and Fedora rely on **firewalld**, also off by default. Install or enable it with YUM or DNF if it’s missing.

![The image shows logos for Red Hat and Fedora, with a note about the "firewalld" service, indicating it is off by default but can be enabled to restrict traffic.](https://kodekloud.com/kk-media/image/upload/v1752882297/notes-assets/images/Nginx-For-Beginners-Firewall-Ports/red-hat-fedora-firewalld-logos.jpg)

Both UFW and firewalld serve as front ends to **iptables**, the underlying Linux utility managing packet filtering.

![The image shows logos for Red Hat and Fedora, with text about installing a firewall via YUM and mentions that iptables comes pre-installed in all Linux distributions.](https://kodekloud.com/kk-media/image/upload/v1752882297/notes-assets/images/Nginx-For-Beginners-Firewall-Ports/red-hat-fedora-firewall-yum.jpg)

#### Windows & macOS

- Windows Firewall comes **enabled by default**.
- macOS Firewall is **disabled by default** but can be activated in System Preferences.

![The image shows icons for Windows and Apple with labels "Windows Firewall" and "Firewall," along with a toggle switch labeled "OFF" and "ON."](https://kodekloud.com/kk-media/image/upload/v1752882299/notes-assets/images/Nginx-For-Beginners-Firewall-Ports/windows-apple-firewall-toggle-icons.jpg)

>[!Warning]
Always keep your firewall enabled to reduce the attack surface of your server.

### Understanding Ports

A port is a logical communication endpoint—think of it as a “door” or “window” in your network. Each service listens on a specific port number.

![The image shows an illustration of a network cable and port, with text explaining that a port is a communication endpoint for data flow in and out of a computer or network device.](https://kodekloud.com/kk-media/image/upload/v1752882300/notes-assets/images/Nginx-For-Beginners-Firewall-Ports/network-cable-port-illustration.jpg)

When you visit websites:

- HTTP traffic → port **80**
- HTTPS traffic → port **443**

Other services use different ports. Expose only what’s necessary.

| **Port** | **Service** | **Description**         |
| -------- | ----------- | ----------------------- |
| 22       | SSH         | Secure shell access     |
| 25       | SMTP        | Email delivery          |
| 53       | DNS         | Domain name resolution  |
| 80       | HTTP        | Unencrypted web traffic |
| 443      | HTTPS       | Encrypted web traffic   |

![The image lists common network ports along with their associated services, such as HTTP, HTTPS, SMTP, and others, with their respective port numbers and functions.](https://kodekloud.com/kk-media/image/upload/v1752882301/notes-assets/images/Nginx-For-Beginners-Firewall-Ports/common-network-ports-services-list.jpg)

**Best practice:** On public web servers, open only ports **80** and **443**, or restrict other ports via IP allow-lists.

### Managing UFW on Debian/Ubuntu

1. Allow SSH first to prevent lockout:
```
sudo ufw allow 22/tcp
```

2. Enable UFW:
```
sudo ufw enable
```

3. Open HTTP and HTTPS:
```
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
```
    
4. Reload to apply:
    ```
    sudo ufw reload
    ```
    
5. View rules with indices:
    ```
    sudo ufw status numbered
    ```
    
6. Delete a specific rule:
    ```
    sudo ufw delete <rule-number>
    ```

### Managing firewalld on Red Hat/Fedora

1. Install (if needed):
    
    ```
    sudo yum update && sudo yum install firewalld
    ```
    
2. Start and enable at boot:
    
    ```
    sudo systemctl start firewalld
    sudo systemctl enable firewalld
    ```
    
3. Open port permanently (e.g., HTTP):
    
    ```
    sudo firewall-cmd --permanent --add-port=80/tcp
    sudo firewall-cmd --reload
    ```
    
4. Remove a port:
    
    ```
    sudo firewall-cmd --permanent --remove-port=80/tcp
    sudo firewall-cmd --reload
    ```
    
5. Check active zones and ports:
    
    ```
    sudo firewall-cmd --list-all
    ```

### Inspecting Open Ports with netstat

`netstat` lists active connections and listening ports. Install it if missing:

```
# Debian/Ubuntu
sudo apt update
sudo apt install net-tools


# Red Hat/Fedora/CentOS
sudo yum install net-tools
```

Run:

```
sudo netstat -nltup
```

Sample output:

```
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      1042/sshd
tcp6       0      0 :::22                   :::*                    LISTEN      1042/sshd
udp        0      0 127.0.0.1:323           0.0.0.0:*                          634/chronyd
udp6       0      0 :::323                  :::*                               634/chronyd
```

`netstat` helps you verify which ports your services are actively listening on—essential for troubleshooting connectivity and firewall configurations.