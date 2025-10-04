>[!Note]
>The purpose of Kubernetes is to host applications in the form of containers in an automated fashion, allowing you to deploy as many instances of your application as needed easily.

> `kubectl` is a command-line utility used to interact with the Kubernetes clusters

Kubernetes officially supports up to 110 pods per node.

Kubernetes _simplifies the deployment, scaling, and management of containerized applications through automation_. In Kubernetes, the cluster consists of nodes—whether physical or virtual, on-premises or cloud-hosted—that host your containerized applications.

## Master Node Components

The master node contains several control plane components that manage the entire Kubernetes cluster. It keeps track of all nodes, decides where applications should run, and continuously monitors the cluster. Think of the master node as the central command center coordinating the fleet.

![The image illustrates a Kubernetes concept with a person explaining, featuring a ship labeled "Master" and smaller ships labeled "Worker Nodes" amidst clouds.](https://kodekloud.com/kk-media/image/upload/v1752869701/notes-assets/images/CKA-Certification-Course-Certified-Kubernetes-Administrator-Cluster-Architecture/frame_140.jpg)

In a busy harbor, many containers are loaded and unloaded daily. Kubernetes maintains detailed information about each container and its corresponding node in a highly available key-value store called etcd. Etcd uses a simple key-value format along with a quorum mechanism, ensuring reliable and consistent data storage across the cluster.

When a new container (or "ship cargo") is ready, the Kubernetes scheduler—similar to port cranes—determines which worker node (or "ship") should host it. The scheduler takes into account current load, resource requirements, and specific constraints like taints, tolerations, or node affinity rules. This scheduling process is vital for efficient cluster operation.

- [n] The Kubernetes replication controller and other controllers work like dock office staff, ensuring that the desired number of containers are running and managing node operations.

Other key master node components include:

- **ETCD Cluster:** Stores cluster-wide configuration and state data.
- **Kube Scheduler:** Determines the best node for new container deployments.
- **Controllers:** Manage node lifecycle, container replication, and system stability.
- **Kube API Server:** Acts as the central hub for cluster communication and management.

![The image illustrates Kubernetes architecture, showing master and worker nodes, with components like ETCD cluster, controllers, and kube-scheduler, alongside a person explaining.](https://kodekloud.com/kk-media/image/upload/v1752869703/notes-assets/images/CKA-Certification-Course-Certified-Kubernetes-Administrator-Cluster-Architecture/frame_270.jpg)

## Worker Node Components

Worker nodes, which can be compared to cargo ships, are responsible for running the containerized applications. Each node is managed by the Kubelet, the node’s “captain,” which ensures that containers are running as instructed.

- **Kubelet:** Manages container lifecycle on an individual node. It receives instructions from the Kube API server to create, update, or delete containers, and regularly reports the node's status.
- **Kube Proxy:** Configures networking rules on worker nodes, thus enabling smooth inter-container communication across nodes. For instance, it allows a web server on one node to interact with a database on another.

- [n] The entire control system is containerized. Whether you are using Docker, Containerd, or CRI-O, every node (including master nodes with containerized components) requires a compatible container runtime engine.

The high-level worker node architecture ensures that applications remain available and responsive, even as they communicate across a distributed network.

![The image illustrates Kubernetes architecture, showing components like Master and Worker Nodes, with a person explaining the diagram.](https://kodekloud.com/kk-media/image/upload/v1752869704/notes-assets/images/CKA-Certification-Course-Certified-Kubernetes-Administrator-Cluster-Architecture/frame_510.jpg)

## Summary of Kubernetes Architecture

The Kubernetes cluster architecture is divided into two main segments:

|Component Category|Key Components|Description|
|---|---|---|
|**Master Node**|etcd, Kube Scheduler, Controllers, Kube API Server|Centralized control and management of the entire cluster.|
|**Worker Node**|Kubelet, Kube Proxy|Responsible for the lifecycle management of containers and ensuring network communication between services.|
