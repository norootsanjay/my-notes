Web servers are computers running software to deliver the requested web page to the user's browser.

## Query Process

![[Screenshot 2025-09-30 at 08.53.33.png]]

## Event Driven Architecture
Nginx handles incoming requests using an **event-driven architecture**.

1. **Request Reception** – Nginx receives the client’s request and passes it into the **event loop**.
2. **Logging & Dispatching** – The request is logged and forwarded to the next stage for processing.
3. **Event Processing** – Nginx gathers all the necessary data (such as files, cached content, or responses from upstream servers) and prepares the output.
4. **Response Delivery** – The processed data is sent back to the client as a response.

The ==event loop== continues running indefinitely, efficiently receiving new requests, logging them, processing the events, and sending responses. This non-blocking model allows Nginx to handle thousands of concurrent connections with minimal resources.![[Screenshot 2025-09-30 at 09.46.22.png]]

## Request Handling in Nginx

![[Screenshot 2025-09-30 at 09.50.25.png]]

## Use Cases

1. Load Balancer
2. Caching
3. Reverse/Forward Proxy

### Load Balancing

By distributing incoming requests across multiple servers, Nginx prevents any single backend from becoming a bottleneck. You declare an ==upstream== block listing your servers, then proxy traffic to it.

```nginx.conf
upstream backend {
    server backend1.example.com;
    server backend2.example.com max_fails=3 fail_timeout=30s;
}


server {
    listen 80;
    location / {
        proxy_pass http://backend;
    }
}
```
![[Pasted image 20250930101643.png]]

### Reverse Proxy

A reverse proxy accepts client requests, applies routing or SSL offloading, then forwards them to one or more backend servers. This hides your infrastructure behind a single public endpoint.

```nginx.conf
server {
    listen 443 ssl;
    server_name example.com;


    ssl_certificate     /etc/nginx/ssl/example.crt;
    ssl_certificate_key /etc/nginx/ssl/example.key;


    location / {
        proxy_pass       http://internal_app;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

![The image illustrates the concept of a reverse proxy using NGINX, showing how it routes requests from users through a network cloud to either an NGINX or Apache web server.](https://kodekloud.com/kk-media/image/upload/v1752882358/notes-assets/images/Nginx-For-Beginners-Nginx-Use-Cases/reverse-proxy-nginx-architecture.jpg)

#### Load Balancer vs. Reverse Proxy

|Feature|Load Balancer|Reverse Proxy|
|---|---|---|
|Primary Role|Distribute traffic across servers|Intercept and forward requests|
|Backend Servers|Requires two or more|Can work with a single server|
|Common Use Cases|Scaling, failover, health checks|SSL/TLS termination, path-based routing|
### Forward Proxy

A forward proxy sits between clients and the internet, filtering or anonymizing outbound requests. Configure Nginx to restrict sites or mask client IPs for privacy.

```nginx.conf
server {
    listen 3128;


    resolver 8.8.8.8;
    proxy_pass_request_headers on;


    location / {
        proxy_pass $scheme://$http_host$request_uri;
        proxy_hide_header Proxy-Authorization;
    }
}
```

>[!Warning]
Opening a forward proxy to the public can lead to abuse. Always secure it with `allow`/`deny` or authentication mechanisms.

![The image illustrates the concept of a forward proxy, showing clients accessing websites through an NGINX proxy and a network cloud.](https://kodekloud.com/kk-media/image/upload/v1752882359/notes-assets/images/Nginx-For-Beginners-Nginx-Use-Cases/forward-proxy-nginx-clients-diagram.jpg)

### Caching

Caching with Nginx reduces response times and eases load on backend services. Define a cache zone, set key parameters, and control how responses are stored.

```nginx.conf
proxy_cache_path /var/cache/nginx levels=1:2 keys_zone=my_cache:10m 
                 inactive=60m max_size=1g;


server {
    listen 80;
    location / {
        proxy_cache my_cache;
        proxy_pass http://backend;
        proxy_cache_valid 200 302 10m;
        proxy_cache_valid 404 1m;
    }
}
```

>[!Note]
Monitor cache usage and tune `inactive` and `max_size` to avoid running out of disk space. Use `proxy_cache_bypass` for selective caching.

![The image illustrates a caching system with a network cloud, a reverse proxy using NGINX, and multiple web servers, including Apache and a generic server. It shows the flow of data from users through the network and proxy to the servers.](https://kodekloud.com/kk-media/image/upload/v1752882360/notes-assets/images/Nginx-For-Beginners-Nginx-Use-Cases/caching-system-nginx-proxy-servers.jpg)