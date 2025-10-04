# What are Containers?
## Introduction to Docker and Containers

If you are already familiar with Docker, feel free to skip ahead. However, I’d like to share an experience that highlights why Docker became vital in modern application development.

In one project, I faced the challenge of establishing an end-to-end application stack comprising multiple components: a Node.js web server, a MongoDB database, a Redis messaging system, and an orchestration tool such as Ansible. We encountered several compatibility issues:

1. Services required specific operating system versions. Sometimes, mismatched versions meant that one service would work on an OS that another could not.
2. Services depended on different library versions, making it difficult to find a single environment that satisfied all dependencies.

![The image explains the need for containers, showing software components like Node.js, MongoDB, and Redis, with complex dependencies on libraries and hardware infrastructure.](https://kodekloud.com/kk-media/image/upload/v1752880660/notes-assets/images/Kubernetes-and-Cloud-Native-Associate-KCNA-What-are-Containers/frame_130.jpg)

Moreover, as the application evolved with upgrades and even component switches (like changing databases), each change brought new compatibility challenges, often described as the “matrix from hell.”

Another significant hurdle was onboarding new developers. They had to execute a long list of manual setup instructions, ensuring that their development environments matched production exactly—resulting in inconsistencies and higher error rates.

![The image explains the need for containers, highlighting issues like compatibility, setup time, and environment differences, with a chaotic "Matrix from Hell" illustration.](https://kodekloud.com/kk-media/image/upload/v1752880661/notes-assets/images/Kubernetes-and-Cloud-Native-Associate-KCNA-What-are-Containers/frame_180.jpg)

Note

Docker solved these challenges by encapsulating each application component in its own isolated container. This isolation bundled libraries, dependencies, and a lightweight file system within each container—enabling seamless environment replication across development, testing, and production.

With Docker, developers simply run a Docker command as long as Docker is installed, regardless of the underlying operating system.

## What Are Containers?

Containers provide isolated environments that run processes, services, network interfaces, and file system mounts—similar to virtual machines (VMs). However, unlike VMs, containers share the host’s operating system kernel.

![The image explains containers, showing a person presenting a diagram with containers having processes, network, and mounts, all running on a shared OS kernel.](https://kodekloud.com/kk-media/image/upload/v1752880661/notes-assets/images/Kubernetes-and-Cloud-Native-Associate-KCNA-What-are-Containers/frame_290.jpg)

The concept of containerization is not new. Early implementations such as LXC, LXD, and LXCFS paved the way for Docker, which popularized containers by offering an easy-to-use, high-level interface that abstracts the complexities of container management.

## The Role of the OS Kernel

Understanding Docker’s power starts with the operating system. Consider popular Linux distributions like Ubuntu, Fedora, SUSE, or CentOS. Each consists of:

- The OS Kernel: Interacting directly with hardware.
- A set of software packages: These define the user interface, drivers, compilers, file managers, and developer tools.

Docker containers share the host’s kernel. For instance, an Ubuntu system running Docker can host containers based on Debian, Fedora, SUSE, or CentOS, as all share the same kernel. Each container includes only the additional software that sets it apart.

![A person stands in front of a diagram explaining kernel sharing with Docker, featuring software icons and an Ubuntu operating system.](https://kodekloud.com/kk-media/image/upload/v1752880663/notes-assets/images/Kubernetes-and-Cloud-Native-Associate-KCNA-What-are-Containers/frame_400.jpg)

A key exception is Windows. Docker on Linux cannot run Windows-based containers due to kernel differences; for Windows containers, Docker must run on a Windows Server.

## Containers vs. Virtual Machines

Understanding the distinction between containers and virtual machines (VMs) is crucial. Below is a comparison that highlights key differences:

|Component|Docker Container|Virtual Machine|
|---|---|---|
|Base Architecture|Host OS + Docker daemon|Host OS + Hypervisor + Guest OS|
|Resource Consumption|Lightweight (measured in megabytes)|Typically larger (measured in gigabytes)|
|Boot Time|Seconds|Minutes|
|Isolation Level|Shares host kernel|Full OS isolation|

![The image compares containers and virtual machines, highlighting differences in utilization, size, boot-up time, and architecture layers.](https://kodekloud.com/kk-media/image/upload/v1752880665/notes-assets/images/Kubernetes-and-Cloud-Native-Associate-KCNA-What-are-Containers/frame_550.jpg)

While containers require less overhead, they provide a lower level of isolation compared to VMs since they share the host's kernel.

## Running Containerized Applications

Many organizations today leverage containerized applications available on public registries like Docker Hub or Docker Store. These registries offer a vast range of images—from operating systems to databases and development tools.

Once you have the necessary Docker images and Docker is installed, deploying an application stack can be as simple as executing a series of commands. For example, you might launch services with these commands:

```
docker run ansible
docker run mongodb
docker run redis
docker run nodejs
```

If you need multiple instances of a web service, you can run additional containers and configure a load balancer to distribute traffic. Should one instance fail, it can be replaced quickly with minimal effort.

## Understanding Images and Containers

It is important to differentiate between Docker images and containers:

- A Docker image is a template containing instructions to create a container—similar to a VM template.
- A Docker container is a running instance of that image, providing an isolated environment with its own processes and resources.

![The image explains the difference between a Docker image and Docker containers, illustrating a Docker image as a template for multiple containers.](https://kodekloud.com/kk-media/image/upload/v1752880666/notes-assets/images/Kubernetes-and-Cloud-Native-Associate-KCNA-What-are-Containers/frame_690.jpg)

Many popular products are “Dockerized” already. If a prebuilt image does not meet your requirements, you can create one using a Dockerfile and push it to Docker Hub for wider use.

## Shifting Responsibilities: Developers and Operations

Traditionally, developers built applications and handed them off to the operations team for deployment. The operations team then configured the host environment, installed prerequisites, and managed dependencies. This handoff often led to miscommunications and troubleshooting difficulties.

Docker transforms this dynamic. Developers now define the application environment within a Dockerfile. Once the Docker image is built and tested, it behaves consistently across all platforms, simplifying the deployment process for operations teams.

![The image illustrates the "Container Advantage" with icons representing a developer, a Docker image, and operations personnel.](https://kodekloud.com/kk-media/image/upload/v1752880667/notes-assets/images/Kubernetes-and-Cloud-Native-Associate-KCNA-What-are-Containers/frame_780.jpg)

Key Takeaway

By encapsulating the application environment in a Docker image, you eliminate the "works on my machine" problem. The image, once verified in development, guarantees consistent production behavior.

With this foundation in containers and Docker, you are now ready to explore Kubernetes and harness its powerful orchestration capabilities.

---

# # Demo Docker

Welcome to this comprehensive guide on installing Docker on an Ubuntu virtual machine. In this lesson, we will cover the process of setting up an Ubuntu VM, installing Docker, and running Docker containers.

Prerequisite

Ensure that you are connected to your Ubuntu VM via SSH from your laptop before proceeding.

## Step 1: Becoming the Root User

To start, switch to the root user by running the following command:

```
sudo su
```

## Step 2: Installing Docker on Ubuntu

Install Docker by using the apt-get package manager to install the Docker package (docker.io). This command will install Docker along with all the necessary dependencies.

```
bash
root@osboxes:/home/osboxes# apt-get install docker.io
Reading package lists... Done
Building dependency tree
Reading state information... Done
The following additional packages will be installed:
  bridge-utils cgroupfs-mount containerd git git-man librerror-perl runc ubuntu-fan
Suggested packages:
  aufs-tools btrfs-tools debootstrap docker-doc rinse zfs-fuse | zfsutils git-daemon-run | git-daemon-svn
  git-email git-gui gitk gitweb git-arch git-cvs git-mediawiki git-svn
The following NEW packages will be installed:
  bridge-utils cgroupfs-mount containerd docker.io git git-man librerror-perl runc ubuntu-fan
0 upgraded, 9 newly installed, 0 to remove and 387 not upgraded.
Need to get 21.4 MB of archives.
After this operation, 116 MB of additional disk space will be used.
Do you want to continue? [Y/n]
```

After confirming the installation, Docker will be installed on your system. To verify the installation, display Docker's help page by running:

```
docker
```

## Step 3: Checking the Docker Version

To confirm the installed version of Docker on your Ubuntu VM, execute the following command:

```
bash
root@osboxes:/home/osboxes# docker version
Client:
 Version:       1.13.1
 API version:   1.26
 Go version:    go1.6.2
 Git commit:    092cba3
 Built:         Thu Nov  2 20:40:23 2017
 OS/Arch:       linux/amd64


Server:
 Version:       1.13.1
 API version:   1.26 (minimum version 1.12)
 Go version:    go1.6.2
 Git commit:    092cba3
 Built:         Thu Nov  2 20:40:23 2017
 OS/Arch:       linux/amd64
 Experimental:  false
root@osboxes:/home/osboxes#
```

Tip

Verifying the Docker version ensures that your installation was successful and provides details about both the Client and Server components.

## Step 4: Running the "hello-world" Docker Container

Now that Docker is installed, let's run a simple container to confirm that Docker is functioning correctly. Use the following command to run the "hello-world" container. This command will automatically pull the image from Docker Hub if it is not already available locally, and then display a confirmation message.

```
root@osboxes:/home/osboxes# docker run hello-world
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
ca4f61b1923c: Pull complete
Digest: sha256:083de497cff944f969d8499ab94f07134c50bcf5e6b9559b27182d3fa80ce3f7
Status: Downloaded newer image for hello-world:latest


Hello from Docker!
This message shows that your installation appears to be working correctly.


To generate this message, Docker took the following steps:
1. The Docker client contacted the Docker daemon.
2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
3. The Docker daemon created a new container from that image which runs the executable that produces the output you are currently reading.
4. The Docker daemon streamed that output to the Docker client, which sent it to your terminal.


To try something more ambitious, you can run an Ubuntu container with:
$ docker run -it ubuntu bash
```

## Step 5: Running the "whalesay" Docker Container

For a bit of fun, let’s run another Docker image called "whalesay." This container uses the "cowsay" program to display a message along with an ASCII art whale. Execute the command below:

```
root@osboxes:/home/osboxes# docker run docker/whalesay cowsay boo
Unable to find image 'docker/whalesay:latest' locally
latest: Pulling from docker/whalesay
903dcd34cfd7: Pull complete
00bf65475aba: Extracting  [==================>         ] 16.12 MB/37.71 MB
a3ed95caeb02: Pull complete
c57b6bbc83e3: Download complete
8978f6879e2f: Download complete
8eed3712d2cf: Download complete
```

Once the image is pulled, the container runs and produces the following output:

```
root@osboxes:/home/osboxes# docker run docker/whalesay cowsay boo
Unable to find image 'docker/whalesay:latest' locally
latest: Pulling from docker/whalesay
Digest: sha256:178598e51a26abbc958b8a2e48825c90bc22e641de3d31e18aaf55f3258ba93b
Status: Downloaded newer image for docker/whalesay:latest
< boo >
 -----
      .   :        .    
    # ## ## #  ==         
  # ## ## ## # === =        
 ~~ ~~~~ ~~ ~~ ~=== ~~ ~~
                o
root@osboxes:/home/osboxes# clear
```

## Conclusion

In this lesson, we demonstrated how to install Docker on an Ubuntu virtual machine and run Docker containers. You've learned to:

- Become the root user.
- Install Docker and its dependencies.
- Confirm the installation by checking the Docker version.
- Run the "hello-world" and "whalesay" containers to verify that Docker is working correctly.

Enjoy exploring Docker further and discovering more about containerization!

---

# Container Orchestration

Welcome to our comprehensive guide on container orchestration. After learning the fundamentals of container technology and Docker packaging, it's time to explore how to deploy containerized applications in production. Consider these essential questions:

- How do you run your application in a production environment once it's containerized?
- What steps are needed when your application relies on dependent services like databases, messaging systems, or other backend components?
- How can you scale your application up during high demand or scale it down when traffic decreases?

To address these challenges, an underlying orchestration platform is required. This platform manages inter-container connectivity and dynamically scales services based on load, a process known as container orchestration.

![The image illustrates container orchestration with Docker, showing a MySQL container and multiple web containers across different Docker hosts.](https://kodekloud.com/kk-media/image/upload/v1752880642/notes-assets/images/Kubernetes-and-Cloud-Native-Associate-KCNA-Container-Orchestration/frame_40.jpg)

Among the available orchestration tools, Kubernetes stands out. However, there are several alternatives:

- **Docker Swarm:** Offers a straightforward setup but may lack advanced features for complex applications.
- **Apache Mesos:** Provides robust functionality but can be challenging to configure initially.
- **Kubernetes:** Though it might require extra effort for initial setup, it provides extensive customization, supports complex architectures, and is integrated with all major public cloud providers such as GCP, Azure, and AWS. Its popularity is also evident on GitHub.

![The image displays logos of orchestration technologies: Docker Swarm, Kubernetes, and Mesos.](https://kodekloud.com/kk-media/image/upload/v1752880642/notes-assets/images/Kubernetes-and-Cloud-Native-Associate-KCNA-Container-Orchestration/frame_70.jpg)

Benefits of Container Orchestration

Container orchestration offers multiple advantages:

- **High Availability:** Mitigate downtime by running multiple container instances across different nodes.
- **Load Balancing:** Evenly distribute user traffic across containers.
- **Scalability:** Seamlessly deploy additional instances as demand increases.
- **Flexibility:** Scale services and adjust underlying nodes without interrupting operations.
- **Simplicity:** Manage functionalities with declarative object configuration files.

Kubernetes, in particular, enables the management and deployment of hundreds or even thousands of containers in a clustered environment.

![The image illustrates the advantages of Kubernetes, showing orchestration of web and backend services across multiple containers.](https://kodekloud.com/kk-media/image/upload/v1752880644/notes-assets/images/Kubernetes-and-Cloud-Native-Associate-KCNA-Container-Orchestration/frame_160.jpg)

In upcoming articles, we will explore Kubernetes' architecture and core concepts in greater detail. Thank you for reading, and stay tuned for more insights on advanced container orchestration!

---

# Kubernetes Architecture

Welcome to this lesson on Kubernetes Architecture. Before setting up a Kubernetes cluster, it's essential to understand the fundamental components and terminology used throughout the configuration process. This knowledge will help you design a robust, scalable, and highly available system.

## Core Concepts

In Kubernetes terminology, a **node** refers to a machine—either physical or virtual—that runs the Kubernetes software. Nodes, formerly known as minions, are the worker machines that run containerized applications. Ensuring high availability and optimal performance requires deploying multiple nodes in a clustered configuration. A cluster is a group of nodes that work together: if one node fails, the remaining nodes continue to run your applications.

Note

The master node is responsible for overseeing and managing the cluster by monitoring node statuses and coordinating container deployments.

## Role of the Master Node

The master node plays a crucial role in managing the Kubernetes cluster. It keeps track of all worker nodes and their statuses, orchestrates deployments, and ensures that the desired state of the cluster is maintained. The following diagram illustrates a typical Kubernetes cluster architecture with one master node and multiple worker nodes:

![The image illustrates a Kubernetes cluster architecture with one master node and three worker nodes, each running a Redis instance.](https://kodekloud.com/kk-media/image/upload/v1752880652/notes-assets/images/Kubernetes-and-Cloud-Native-Associate-KCNA-Kubernetes-Architecture/frame_90.jpg)

## Key Kubernetes Components

When installing Kubernetes, several essential components are set up to form the control plane and enable seamless container orchestration:

- **API Server:** Serves as the front end for Kubernetes, mediating communication between users, the command line interface, and the cluster.
- **etcd:** A distributed key-value store that holds all the crucial data for managing the cluster. In multi-master environments, etcd ensures data consistency by replicating information and implementing lock mechanisms.
- **Scheduler:** Allocates newly created container tasks to suitable worker nodes based on resource availability.
- **Controllers:** Act as the orchestration brain by monitoring cluster events and replacing failed containers or nodes.
- **Container Runtime:** The underlying software responsible for running containers on a node. While Docker is popular, alternatives like containerd or CRI-O are available.
- **kubelet:** An agent on each worker node that communicates with the master node to ensure that containers are operational and healthy.

The diagram below provides a visual representation of these Kubernetes components:

![The image shows a diagram of Kubernetes components: API Server, etcd, kubelet, Container Runtime, Controller, and Scheduler, with a key-value store representation.](https://kodekloud.com/kk-media/image/upload/v1752880653/notes-assets/images/Kubernetes-and-Cloud-Native-Associate-KCNA-Kubernetes-Architecture/frame_150.jpg)

Understanding how these components interact clarifies the distinction between master and worker nodes. Worker nodes host the containers and include elements like the container runtime (e.g., Docker) and the kubelet agent, while the master node comprises components such as the kube-apiserver, controller manager, scheduler, and an etcd-based key-value store.

![The image compares Kubernetes Master and Worker Nodes, showing components like kube-apiserver, etcd, controller, scheduler, kubelet, and container runtime.](https://kodekloud.com/kk-media/image/upload/v1752880655/notes-assets/images/Kubernetes-and-Cloud-Native-Associate-KCNA-Kubernetes-Architecture/frame_310.jpg)

## Managing your Cluster with kubectl

An indispensable tool for interacting with your Kubernetes cluster is the `kubectl`command-line utility. With `kubectl`, you can deploy and manage applications, retrieve vital cluster information, and check node statuses. Here are some foundational `kubectl` commands:

- **kubectl run:** Deploys an application on the cluster.
- **kubectl cluster-info:** Displays detailed information about the cluster.
- **kubectl get nodes:** Lists all nodes in the cluster.

Below is an example demonstrating these commands:

```
kubectl run hello-minikube
kubectl cluster-info
kubectl get nodes
```

These commands serve as the foundation for efficient cluster management. As you progress through the lesson, you will learn more advanced `kubectl` functionalities and additional Kubernetes concepts.

That concludes this lesson on Kubernetes Architecture. Happy clustering!

---

# Runtime CRI

In this article, we explore the Container Runtime Interface (CRI) and its critical role in the container ecosystem. The CRI is a fundamental component that enables Kubernetes to support a variety of container runtimes without modifying its core codebase.

## Background and Evolution

Initially, Docker became the most popular solution for container operations due to its simplicity. When Kubernetes was first introduced, it was designed to orchestrate Docker containers exclusively. However, as Kubernetes grew in popularity, new container technologies such as Rocket and ContainerD emerged that required integration with Kubernetes. To address this need, Kubernetes introduced the Container Runtime Interface (CRI).

CRI defines a plugin interface that any container vendor can implement if they adhere to the Open Container Initiative (OCI) standards. This design involves a gRPC API used by the Kubernetes Kubelet to manage container images, containers, and networking. By implementing the CRI API, container runtimes can operate independently of Kubernetes, allowing system architects the flexibility to choose the optimal runtime for their environment.

![The image explains the Container Runtime Interface (CRI), highlighting its role in defining the gRPC protocol for Kubernetes Kubelet to interact with container runtimes, images, and networking.](https://kodekloud.com/kk-media/image/upload/v1752880656/notes-assets/images/Kubernetes-and-Cloud-Native-Associate-KCNA-Runtime-CRI/frame_70.jpg)

Note

For more details on container orchestration and Kubernetes, consider reviewing the [Kubernetes Documentation](https://kubernetes.io/docs/).

## Docker and the Introduction of Docker Shim

Docker's widespread adoption in the container ecosystem meant that even as CRI was introduced, the Kubernetes community continued to support Docker. To maintain compatibility, Kubernetes implemented a temporary solution known as Docker Shim. This intermediary layer allowed Docker to communicate with Kubernetes without directly using the CRI, ensuring that existing Docker-based workflows continued to function seamlessly.

![The image illustrates the relationship between Rkt, Containerd, Docker, and Kubernetes via the Container Runtime Interface and Dockershim.](https://kodekloud.com/kk-media/image/upload/v1752880657/notes-assets/images/Kubernetes-and-Cloud-Native-Associate-KCNA-Runtime-CRI/frame_100.jpg)

Docker Shim was maintained purely for backward compatibility. As Kubernetes evolved toward a more container runtime-agnostic approach, the reliance on Docker Shim was deprecated. In Kubernetes version 1.24, Docker Shim was officially removed, and native support for Docker was dropped. However, Docker images remain compatible because they adhere to the OCI standard, which means they can be used with other container runtimes like ContainerD.

## Embracing Container-Native CRI Support

Users are now encouraged to adopt container runtimes that natively support the CRI. This shift not only enhances compatibility and standardization across container runtimes but also helps mitigate vendor lock-in. By supporting multiple container runtimes, Kubernetes enables organizations to choose the most appropriate solution for their infrastructure needs.

![The image illustrates Docker removal, highlighting Docker, Docker Images, Kubernetes, and the Open Container Initiative with tools like gVisor, containerd, kata containers, and cri-o.](https://kodekloud.com/kk-media/image/upload/v1752880658/notes-assets/images/Kubernetes-and-Cloud-Native-Associate-KCNA-Runtime-CRI/frame_160.jpg)

Additional Resources

Explore further insights into container runtimes and Kubernetes best practices by visiting the [Kubernetes Basics](https://kubernetes.io/docs/concepts/overview/what-is-kubernetes/) and other related resources.

## Conclusion

The Container Runtime Interface has paved the way for enhanced compatibility and flexibility in the container ecosystem. By enabling a diverse set of container runtimes, CRI ensures that Kubernetes users can avoid vendor-specific limitations and foster innovation in container management.

That concludes this article on the Container Runtime Interface. Thank you for reading, and we'll see you in the next one.

---

# Docker vs ContainerD

In this lesson, we explore the distinctions between Docker and ContainerD, along with several associated CLI tools. As containerization and Kubernetes evolve, you may find older documentation referring to Docker, while newer resources emphasize ContainerD. You might also encounter CLI tools such as CTR, nerdctl, and crictl, and wonder which one best fits your use case. This lesson aims to clarify these differences and outline their respective roles.

## The Evolution of Container Runtimes

Originally, Docker was the dominant tool in the container ecosystem due to its user-friendly approach to container management. When Kubernetes was initially developed, it was tightly integrated with Docker, which led to Docker’s pervasive use in container orchestration.

As Kubernetes expanded, alternative container runtimes like Rocket emerged. To address this diversity, the Kubernetes community introduced the Container Runtime Interface (CRI)—an API standard that enables any runtime adhering to the Open Container Initiative (OCI) standards to integrate with Kubernetes. The OCI specifies:

- How images should be built (image specification)
- Standards for container runtimes (runtime specification)

Although Docker images conform to OCI standards, Docker itself predates CRI and lacks native CRI support. To bridge this gap, Kubernetes implemented the Docker Shim, which allowed Kubernetes to interact with Docker despite its non-compliance with CRI. With the maturation of alternatives like ContainerD (which evolved from Docker’s architecture), Kubernetes now directly supports these CRI-compatible runtimes, thereby eliminating the extra maintenance overhead of the Docker shim.

![The image illustrates the relationship between Docker, containerd, Kubernetes, and rkt, highlighting components like CLI, API, and CRI, with a focus on container runtime interfaces.](https://kodekloud.com/kk-media/image/upload/v1752880645/notes-assets/images/Kubernetes-and-Cloud-Native-Associate-KCNA-Docker-vs-ContainerD/frame_190.jpg)

## Understanding Docker’s Components

Docker is more than just a container runtime; it is a suite of tools that includes the Docker CLI, API, build utilities, volume management, authentication, and security features. One important component is containerd—a daemon responsible for container execution using the runc runtime. Although containerd was originally embedded within Docker, it is now maintained as a separate, CRI-compliant project. This separation allows you to install ContainerD independently if you do not require Docker’s additional features.

## Using ContainerD and Its CLI Tools

Historically, containers were managed using the Docker run command. When running ContainerD as a standalone tool, you can install it with a simple command:

```
$ tar -C /usr/local -xzvf containerd-1.6.2-linux-amd64.tar.gz
```

This installation extracts several binaries:

- containerd
- containerd-shim
- containerd-shim-runc-v1
- containerd-shim-runc-v2
- ctr
- containerd-stress

Once installed, ContainerD comes with the command-line tool CTR. Keep in mind that ctr was designed primarily for debugging ContainerD and only supports a limited range of features. For most container management tasks, you will interact with the ContainerD API, which is less user-friendly than a dedicated CLI tool.

For example, to pull a Redis image and run a container using ctr, you can execute:

```
$ ctr images pull docker.io/library/redis:alpine
$ ctr run docker.io/library/redis:alpine redis
```

Note

For day-to-day container management in production environments, consider using tools that provide a more comprehensive and user-friendly experience.

## nerdctl: A Docker-like CLI for ContainerD

The nerdctl tool, developed by the ContainerD community, offers a Docker-like command-line experience while incorporating advanced features such as:

- Encrypted images
- Lazy image pulling
- Peer-to-peer image distribution
- Image signing and verification
- Kubernetes namespace support

Using nerdctl is straightforward. Simply replace the Docker command with nerdctl. For instance, to run a container and map ports, you can use:

```
$ nerdctl run --name redis redis:alpine
$ nerdctl run --name webserver -p 80:80 -d nginx
```

![The image describes "nerdctl," a Docker-like CLI for containerD, supporting features like Docker Compose, encrypted images, lazy pulling, P2P distribution, image signing, and Kubernetes namespaces.](https://kodekloud.com/kk-media/image/upload/v1752880646/notes-assets/images/Kubernetes-and-Cloud-Native-Associate-KCNA-Docker-vs-ContainerD/frame_380.jpg)

This tool not only mirrors Docker’s functionality but also provides access to innovative features emerging within ContainerD.

## The Role of crictl for CRI-Compatible Container Runtimes

crictl is another essential CLI utility designed to interact with CRI-compatible container runtimes from a Kubernetes perspective. Unlike ctr and nerdctl, which are ContainerD-focused, crictl offers a standardized approach that works across all CRI-supported runtimes—including ContainerD, Rocket, and CRI-O.

Maintained by the Kubernetes community, crictl is primarily used for inspecting and debugging container runtimes. Although it technically supports container creation, it is not recommended, as containers created outside the control of kubelet (the Kubernetes node agent) may be deleted.

Basic crictl commands include:

```
$ crictl pull busybox
$ crictl images
$ crictl ps -a
```

To run commands inside a container or to view logs, crictl provides syntax similar to Docker’s, while also handling pod-specific details—a feature Docker does not offer.

![The image explains "crictl," a CLI for CRI-compatible container runtimes, used for inspecting and debugging, not creating containers, and works across different runtimes.](https://kodekloud.com/kk-media/image/upload/v1752880647/notes-assets/images/Kubernetes-and-Cloud-Native-Associate-KCNA-Docker-vs-ContainerD/frame_470.jpg)

A comparison of common Docker CLI commands versus crictl commands highlights many similarities in functionality, covering tasks such as attach, exec, images, info, inspect, logs, ps, stats, and version.

![A comparison table of Docker CLI and crictl commands, descriptions, and unsupported features for debugging information retrieval.](https://kodekloud.com/kk-media/image/upload/v1752880649/notes-assets/images/Kubernetes-and-Cloud-Native-Associate-KCNA-Docker-vs-ContainerD/frame_640.jpg)

![A comparison table of Docker CLI and crictl commands, showing descriptions and unsupported features for container management tasks.](https://kodekloud.com/kk-media/image/upload/v1752880650/notes-assets/images/Kubernetes-and-Cloud-Native-Associate-KCNA-Docker-vs-ContainerD/frame_650.jpg)

For additional details on these differences, refer to the documentation linked in the original repository.

## Changes in CRI Endpoints with Kubernetes 1.24

In earlier versions of Kubernetes, crictl connected to runtime endpoints using a default order that included several paths:

```
unix:///var/run/dockershim.sock
unix:///run/containerd/containerd.sock
unix:///run/crio/crio.sock
unix:///var/run/cri-dockerd.sock
```

With Kubernetes 1.24, the Docker shim was removed, and the default endpoint configuration was updated. The docker-socket has now been replaced, and users are encouraged to set the runtime endpoint manually:

```
$ crictl --runtime-endpoint
$ export CONTAINER_RUNTIME_ENDPOINT=<your_runtime_endpoint>
```

For example, valid endpoints include:

```
• unix:///run/containerd/containerd.sock
• unix:///run/crio/crio.sock
• unix:///var/run/cri-dockerd.sock
```

Reviewing pull request 869 and issue 868 in the Kubernetes CRI tools repository can provide further insights into these changes.

Warning

Always ensure your runtime endpoint is configured correctly to avoid connectivity issues between Kubernetes and your container runtime.

## Summary

To recap the key points:

- The CTR tool bundled with ContainerD is intended primarily for debugging, offering limited functionality.
- nerdctl provides a full-featured, Docker-like CLI for efficient container management with ContainerD and is ideal for production scenarios.
- crictl, maintained by the Kubernetes community, is excellent for debugging and inspecting container runtimes across any CRI-supported platform.

![The image compares container tools: ctr, nerdctl, and crictl, detailing their purposes, communities, and compatibility with ContainerD and Kubernetes.](https://kodekloud.com/kk-media/image/upload/v1752880651/notes-assets/images/Kubernetes-and-Cloud-Native-Associate-KCNA-Docker-vs-ContainerD/frame_750.jpg)

Thank you for engaging with this lesson. We hope it has clarified the differences between Docker, ContainerD, and the various CLI tools used in managing containers and orchestrating Kubernetes deployments.

For more detailed information, please visit the [Kubernetes Documentation](https://kubernetes.io/docs/) or explore related articles on container management.

