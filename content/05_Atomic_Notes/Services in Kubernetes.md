> [!Note]
> Service is an abstraction that gives a stable network endpoint(IP + DNS) to a group of pods, even if those pods change over time.

- Pods come and go (due to scaling, crashes, deployments), and each has its own IP that might change. 
- If other parts of your application had to track those IPs directly, it would be chaos.
- A ==Service== acts as a ==permanent “door”== to access the Pods — no matter which specific Pods are running at any given moment.

- [k] **Purpose:** Expose Pods reliably using a single IP/DNS name.
- [k] **Selection:** Uses ==labels== to pick which Pods to route traffic to.
- [k] **Load balancing:** Distributes traffic between selected Pods.

# Types

1. [[#NodePort]]
2. [[#ClusterIP]]
3. [[#LoadBalancer]]
4. [[#External Name]]
5. [[#Headless]]

## NodePort

NodePort Services are exposed externally through a specified static port binding on each of your Nodes. Hence, you can access the Service by connecting to the port on any of your cluster’s Nodes. NodePort Services are also assigned a cluster IP address that can be used to reach them within the cluster, just like ClusterIP Services.

Use of NodePort Services is generally unadvisable. They have functional limitations and can lead to security issues:

- Anyone who can connect to the port on your Nodes can access the Service.
- Each port number can only be used by one NodePort Service at a time to prevent conflicts.
- Every Node in your cluster has to listen to the port by default, even if they’re not running a Pod that’s included in the Service.
- No automatic load-balancing: clients are served by the Node they connect to.

When a NodePort Service is used, it’s generally to facilitate the use of your own load-balancing solution that reroutes traffic from outside the cluster. NodePorts can also be convenient for temporary debugging, development, and troubleshooting scenarios where you need to quickly test different configurations.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: myapp-service
spec:
  type: NodePort
  ports:
    - targetPort: 80
      port: 80
      nodePort: 30008
  selector:
    app: myapp
    type: front-end
---
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  labels:
    app: myapp
    type: front-end
spec:
  containers:
    - name: nginx-container
      image: nginx
```

```bash
kubectl create -f service-file.yaml
kubectl get svc
```

In this YAML:
- ==targetPort== specifies the Pod’s application port.
- ==port== is the port on the service that acts as a virtual server port within the cluster.
- ==nodePort== maps the external request to the specific port on the node (ensure it’s between 30000 and 32767).
Note that if you omit ==targetPort==, it defaults to the same value as ==port==. Similarly, if ==nodePort== isn’t provided, Kubernetes automatically assigns one.

In a production environment, your application is likely spread across multiple Pods for high availability and load balancing. When Pods share matching labels, the service automatically detects and routes traffic to all endpoints. Kubernetes employs a _round-robin (or random) algorithm_ to distribute incoming requests, serving as an integrated load balancer.

Furthermore, even if your Pods are spread across multiple nodes, ==Kubernetes ensures that the target port is mapped on all nodes.== This means you can access your web application using the IP of any node along with the designated NodePort, providing reliable external connectivity.

![[Pasted image 20250812191616.png]]

## ClusterIP

ClusterIP Services assign an IP address that can be used to reach the Service from within your cluster. This type doesn’t expose the Service externally.

ClusterIP is the default service type used when you don’t specify an alternative option. It’s the most common kind of service you’ll use as it enables simple internal networking for your workloads.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: cluster-ip-service
spec:
  ports:
  - port: 80
    targetPort: 80
  selector:
    name: nginx
```

![[Pasted image 20250812192551.png]]


## LoadBalancer

LoadBalancer Services are exposed outside your cluster using an external load balancer resource. This requires a connection to a load balancer provider, typically achieved by integrating your cluster with your cloud environment. Creating a LoadBalancer service will then automatically provision a new load balancer infrastructure component in your cloud account. This functionality is automatically configured when you use a managed Kubernetes service such as Amazon EKS or Google GKE.

Once you’ve created a LoadBalancer service, you can point your public DNS records to the provisioned load balancer’s IP address. This will then direct traffic to your Kubernetes Service. Therefore, LoadBalancers are the Service type you should normally use when you need an app to be accessible outside Kubernetes.

![[Pasted image 20250812193000.png]]

## External Name

ExternalName Services allow you to conveniently access external resources from within your Kubernetes cluster. Unlike the other Service types, they don’t proxy traffic to your Pods.

When you create an ExternalName Service, you have to set the `spec.externalName` manifest field to the external address you want to route to (such as `example.com`). Kubernetes then adds a CNAME DNS record to your cluster that resolves the Service’s internal address (such as `my-external-service.app-namespace.svc.cluster.local`) to the external address (`example.com`). This allows you to easily change the external address in the future, without having to reconfigure the workloads that refer to it.

## Headless

[Headless services](https://kubernetes.io/docs/concepts/services-networking/service/#headless-services) are a special type of Service that don’t provide load balancing or a cluster IP address. They’re “headless” because Kubernetes doesn’t automatically proxy any traffic through them. This allows you to use DNS lookups to discover the individual IP addresses of any Pods selected by the Service.

A headless service is useful when you want to interface with other service discovery systems without [kube-proxy](https://kubernetes.io/docs/reference/command-line-tools-reference/kube-proxy) interfering. You can create one by specifically setting a Service’s `spec.clusterIP` field to the `None` value.
