> [!Note]
> A certificate is used to guarantee trust between two parties during a transaction.
> 

# TLS Certificates in the Kubernetes Cluster

A Kubernetes cluster consists of master and worker nodes that require secure, encrypted communication. Whether the connection is being made by an administrator using the kubectl utility or directly interacting with the Kubernetes API, a secure TLS connection is essential. Additionally, services within the cluster use server certificates to secure their communications, while client certificates authenticate users or other cluster components.

## Kubernetes Components and Their Certificates

Below is an overview of the main Kubernetes components and the associated certificate configurations:

1. **Kube API Server**  
    The API server exposes an HTTPS service for managing the cluster. It acts as a server and requires its own certificate and key pair (e.g., `api-server.crt` and `api-server.key`).  
    _Note: Certificate names may vary based on your cluster setup._
    
2. **ETCD Server**  
    Serving as the datastore for all cluster information, ETCD requires a unique pair of certificates and keys (e.g., `etcd-server.crt` and `etcd-server.key`).
    
3. **Kubelet on Worker Nodes**  
    On each worker node, the kubelet service exposes an HTTPS endpoint that communicates with the API server for monitoring and management. This service uses its own certificate and key pair (typically named `kubelet.cert` and `kubelet.key`).
    

![The image illustrates server certificates for Kube-API, ETCD, and Kubelet servers, showing their respective certificate and key files.](https://kodekloud.com/kk-media/image/upload/v1752869977/notes-assets/images/CKA-Certification-Course-Certified-Kubernetes-Administrator-TLS-in-Kubernetes/frame_240.jpg)

## Client Components and Their Certificates

Several components operate as clients and require authentication when connecting to the Kube API Server:

1. **Administrator (kubectl/REST API)**  
    The admin user employs a client certificate and key pair (e.g., `admin.crt` and `admin.key`) to establish a secure connection to the API server.
    
2. **Scheduler**  
    Managing pod scheduling, the scheduler requires its own client certificate (e.g., `scheduler.crt` and `scheduler.key`) to interact with the API server.
    
3. **Kube Controller Manager**  
    This component also utilizes a dedicated certificate pair for authenticating its requests to the API server.
    
4. **Kube Proxy**  
    Handling network routing within the cluster, the kube proxy uses a client certificate (generally named `kube-proxy.crt` and `kube-proxy.key`) for secure communications with the API server.
    

The Kube API Server facilitates secure communications with both the ETCD server and the kubelets. For instance, when connecting to the ETCD server, the API server can either reuse its HTTPS service certificates or use a dedicated certificate pair. Likewise, interactions with kubelets can leverage either the API server’s default certificate pair or a separately generated set of certificates.

![The image illustrates the client certificates and keys used for authentication between Kubernetes components like Kube-API Server, ETCD Server, Kube-Scheduler, and others.](https://kodekloud.com/kk-media/image/upload/v1752869978/notes-assets/images/CKA-Certification-Course-Certified-Kubernetes-Administrator-TLS-in-Kubernetes/frame_390.jpg)

## Grouping Certificates for Enhanced Management

For easier certificate management, you can group TLS certificates into two main categories:

|Category|Usage|
|---|---|
|Client Certificates|Used by components (e.g., admin, scheduler, controller manager, kube-proxy) to authenticate with the API server.|
|Server Certificates|Used by server components (e.g., Kube API Server, ETCD Server, kubelet) to secure their services.|

## Utilizing a Certificate Authority (CA)

All certificates must be signed by a Certificate Authority. Kubernetes clusters require at least one CA, though multiple CAs may be used for additional segregation (for example, one CA for general cluster services and another exclusively for ETCD). In this guide, we use a single CA for simplicity. The CA itself has a certificate and key pair, typically named `CA.crt` and `CA.key`.

![The image illustrates client and server certificates for Kubernetes components, including admin, scheduler, controller-manager, kube-proxy, etcd, kube-api, and kubelet servers.](https://kodekloud.com/kk-media/image/upload/v1752869979/notes-assets/images/CKA-Certification-Course-Certified-Kubernetes-Administrator-TLS-in-Kubernetes/frame_420.jpg)

>[!Naming Conventions]
Certificate files follow specific naming conventions. Public key certificates typically have a .crt or .pem extension (e.g., server.crt, server.pem, client.crt, client.pem). In contrast, private keys usually include the word "key" in their file name or extension (e.g., server.key or server-key.pem). If a file name lacks "key," it is almost certainly a public key certificate.

---

# Generating Certificates

## 1. Generating CA Certificates

First, create the Certificate Authority (CA) certificates. The process involves generating a private key, creating a Certificate Signing Request (CSR) that includes the CA's common name, and finally signing the CSR with the private key to produce the CA certificate. The completed process provides the CA with its private key (`ca.key`) and root certificate (`ca.crt`), which are essential for subsequently signing other certificates.

```bash
openssl genrsa -out ca.key 2048
openssl req -new -key ca.key -subj "/CN=KUBERNETES-CA" -out ca.csr
openssl x509 -req -in ca.csr -signkey ca.key -out ca.crt
```


- [n] The CA certificate is the cornerstone of your certificate infrastructure, so ensure that you safeguard the private key.

## 2. Creating Client Certificates

### 2.1 Admin User Certificate

To generate a certificate for the admin user:

1. Create a private key for the admin.
2. Generate a CSR for the admin user specifying the common name (CN) and organizational unit (OU) to reflect group membership (e.g., `system:masters`). This consistency ensures that the admin identity is properly logged in audit trails and recognized in `kubectl` commands.
3. Sign the admin CSR with the CA certificate to produce the final admin certificate.

```bash
openssl genrsa -out admin.key 2048
openssl req -new -key admin.key -subj "/CN=kube-admin/O=system:masters" -out admin.csr
openssl x509 -req -in admin.csr -CA ca.crt -CAkey ca.key -out admin.crt
```

The resulting `admin.crt` file functions as a secure credential, akin to a username and password pair, for authenticating the admin user with the Kubernetes API server.

A similar process is followed to generate client certificates for other components such as the scheduler, controller manager, and kube-proxy.

![The image illustrates the process of generating and signing certificates for "Kube Scheduler," showing keys, certificate requests, and a certificate.](https://kodekloud.com/kk-media/image/upload/v1752869972/notes-assets/images/CKA-Certification-Course-Certified-Kubernetes-Administrator-TLS-in-Kubernetes-Certificate-Creation/frame_230.jpg)

## 3. Using Client Certificates in API Requests

Client certificates eliminate the requirement for using a username and password when making REST API calls to the Kubernetes API server. The admin certificate, for example, can be used to securely communicate with the server by specifying the key, certificate, and CA certificate in your request.

```bash
curl https://kube-apiserver:6443/api/v1/pods \
  --key admin.key --cert admin.crt --cacert ca.crt
```

The API server will respond with a JSON object listing the pods:

```json
{
  "kind": "PodList",
  "apiVersion": "v1",
  "metadata": {
    "selfLink": "/api/v1/pods"
  },
  "items": []
}
```

Most Kubernetes clients can load these connection parameters via a kubeconfig file that consolidates the information required to reach the API server.

![The image illustrates a diagram of client certificates for Kubernetes components, including admin, scheduler, controller-manager, kube-proxy, and kubelet, with associated keys and certificates.](https://kodekloud.com/kk-media/image/upload/v1752869973/notes-assets/images/CKA-Certification-Course-Certified-Kubernetes-Administrator-TLS-in-Kubernetes-Certificate-Creation/frame_260.jpg)

## 4. Server-Side Certificates

For secure communication, both client and server certificates must trust the same CA root certificate. This certificate is used by both parties to verify the authenticity of the certificate they receive.

![The image illustrates client and server certificates for Kubernetes components, including admin, scheduler, controller-manager, kube-proxy, kubelet, and etcd server, with associated keys.](https://kodekloud.com/kk-media/image/upload/v1752869975/notes-assets/images/CKA-Certification-Course-Certified-Kubernetes-Administrator-TLS-in-Kubernetes-Certificate-Creation/frame_330.jpg)

### 4.1 Etcd Server Certificate

The etcd server, a critical component in high availability configurations, also requires a certificate. If etcd is running as a cluster, remember to generate peer certificates to secure inter-member communications. Once created, the certificates are referenced in the etcd configuration file (commonly, `etcd.yaml`). See the example below:

```
cat etcd.yaml
- --advertise-client-urls=https://127.0.0.1:2379
- --key-file=/path-to-certs/etcdserver.key
- --cert-file=/path-to-certs/etcdserver.crt
- --client-cert-auth=true
- --data-dir=/var/lib/etcd
- --initial-advertise-peer-urls=https://127.0.0.1:2380
- --initial-cluster=master=https://127.0.0.1:2380
- --listen-client-urls=https://127.0.0.1:2379
- --listen-peer-urls=https://127.0.0.1:2380
- --name=master
- --peer-cert-file=/path-to-certs/etcdpeer1.crt
- --peer-client-cert-auth=true
- --peer-key-file=/etc/kubernetes/pki/etcd/peer.key
- --peer-trusted-ca-file=/etc/kubernetes/pki/etcd/ca.crt
- --snapshot-count=10000
- --trusted-ca-file=/etc/kubernetes/pki/etcd/ca.crt
```

The `--trusted-ca-file` option ensures that etcd client connections are authenticated using the CA certificate.

## 5. Kube API Server Certificates

The Kube API server is the primary access point for the cluster and is known by several aliases such as "kubernetes", "kubernetes.default", and "kubernetes.default.svc.cluster.local", as well as its IP address. This diversity requires that its certificate includes multiple Subject Alternative Names (SANs).

### 5.1 Creating the API Server Certificate

Start by generating a CSR for the API server:

```bash
openssl req -new -key apiserver.key -subj "/CN=kube-apiserver" -out apiserver.csr
```

Then, create an OpenSSL configuration file (e.g., `openssl.cnf`) to include all necessary SANs:

```
[req]
req_extensions = v3_req
distinguished_name = req_distinguished_name


[v3_req]
basicConstraints = CA:FALSE
keyUsage = nonRepudiation, digitalSignature, keyEncipherment
subjectAltName = @alt_names


[alt_names]
DNS.1 = kubernetes
DNS.2 = kubernetes.default
DNS.3 = kubernetes.default.svc
DNS.4 = kubernetes.default.svc.cluster.local
IP.1 = 10.96.0.1
IP.2 = 172.17.0.87
```

After configuring the CSR with the SANs, sign the certificate using your CA certificate and key. Specify the final certificate parameters in your kube-apiserver configuration, as shown in the configuration snippet below:

```
ExecStart=/usr/local/bin/kube-apiserver \
  --advertise-address=${INTERNAL_IP} \
  --allow-privileged=true \
  --apiserver-count=3 \
  --authorization-mode=Node,RBAC \
  --bind-address=0.0.0.0 \
  --enable-swagger-ui=true \
  --etcd-cafile=/var/lib/kubernetes/ca.pem \
  --etcd-certfile=/var/lib/kubernetes/apiserver-etcd-client.crt \
  --etcd-keyfile=/var/lib/kubernetes/apiserver-etcd-client.key \
  --etcd-servers=https://127.0.0.1:2379 \
  --event-ttl=1h \
  --kubelet-certificate-authority=/var/lib/kubernetes/ca.pem \
  --kubelet-client-certificate=/var/lib/kubernetes/apiserver-kubelet-client.crt \
  --kubelet-client-key=/var/lib/kubernetes/apiserver-kubelet-client.key \
  --kubelet-https=true \
  --runtime-config=api/all \
  --service-account-key-file=/var/lib/kubernetes/service-account.pem \
  --service-cluster-ip-range=10.32.0.0/24 \
  --service-node-port-range=30000-32767 \
  --client-ca-file=/var/lib/kubernetes/ca.pem \
  --tls-cert-file=/var/lib/kubernetes/apiserver.crt \
  --tls-private-key-file=/var/lib/kubernetes/apiserver.key \
  --v=2
```


- [f] Ensure that all DNS names and IP addresses used by the API server are correctly listed in the SAN section of your OpenSSL configuration.

## 6. Kubelet Certificates

The kubelet is a critical component running on each node, managing node-specific operations and secure communication with the API server. For this purpose, every node needs its own certificate and key pair. When generating these certificates, name them according to the node's identity (e.g., node01, node02, node03).

It is also a best practice to generate a separate certificate for the node when it acts as a client to the API server. This certificate should include an identity format such as "system:node" to ensure the API server can assign the appropriate group membership (e.g., `system:nodes`).

![The image illustrates Kubernetes node certificates for nodes 01, 02, and 03, showing their authentication with a kubelet server using client certificates and keys.](https://kodekloud.com/kk-media/image/upload/v1752869976/notes-assets/images/CKA-Certification-Course-Certified-Kubernetes-Administrator-TLS-in-Kubernetes-Certificate-Creation/frame_640.jpg)

Once generated, include these certificates in the kubeconfig files for the respective nodes to guarantee secure communication.