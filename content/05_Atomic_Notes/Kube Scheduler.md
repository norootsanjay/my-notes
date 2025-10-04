## Scheduler Process Overview

The primary responsibility of the Kubernetes scheduler is to assign pods to nodes based on a series of criteria. This ensures that the selected node has sufficient resources and meets any specific requirements. For instance, different nodes may be designated for certain applications or come with varied resource capacities. When multiple pods and nodes are involved, the scheduler assesses each pod against the available nodes through a two-phase process: filtering and ranking.

### 1. Filtering Phase

In the filtering phase, the scheduler eliminates nodes that do not meet the pod's resource requirements. For example, nodes that lack sufficient CPU or memory are immediately excluded.

![The image illustrates a Kubernetes scheduler filtering nodes based on CPU availability, showing nodes with 4, 12, and 16 CPUs, and a container requiring 10 CPUs.](https://kodekloud.com/kk-media/image/upload/v1752869723/notes-assets/images/CKA-Certification-Course-Certified-Kubernetes-Administrator-Kube-Scheduler/frame_110.jpg)

As depicted above, the diagram demonstrates the elimination of nodes with insufficient resources, leaving only the candidate nodes that can accommodate the pod's needs.

### 2. Ranking Phase

After filtering, the scheduler enters the ranking phase. Here, it uses a priority function to score and compare the remaining nodes on a scale from 0 to 10, ultimately selecting the best match. For instance, if placing a pod on one node would leave six free CPUs (four more than an alternative node), that node is assigned a higher score and is chosen.

![The image illustrates the Kube-Scheduler process, showing node filtering and ranking, with nodes labeled by CPU capacity.](https://kodekloud.com/kk-media/image/upload/v1752869724/notes-assets/images/CKA-Certification-Course-Certified-Kubernetes-Administrator-Kube-Scheduler/frame_160.jpg)

This high-level overview outlines how Kubernetes efficiently filters and ranks nodes for optimal pod placement. The scheduler’s design is highly customizable, allowing you to develop your own scheduler if the need arises. For more advanced scheduling configurations—such as resource limits, taints and tolerations, node selectors, and affinity rules—refer to the [Kubernetes Documentation](https://kubernetes.io/docs/).

- [n] Customizing the scheduling process can help tailor your Kubernetes environment to meet specific workloads and performance requirements.

## Installing and Running the Kube Scheduler

To install the kube-scheduler, download the binary from the Kubernetes release page. Once downloaded and extracted, you can run it as a service by specifying the scheduler configuration file. Below is a sample command for downloading the binary and an example systemd service configuration:

```bash
wget https://storage.googleapis.com/kubernetes-release/release/v1.13.0/bin/linux/amd64/kube-scheduler
```

Below is an example of the systemd service configuration for the kube-scheduler:

```
# File: kube-scheduler.service
ExecStart=/usr/local/bin/kube-scheduler \
  --config=/etc/kubernetes/config/kube-scheduler.yaml \
  --v=2
```

If you are using the kubeadm tool to set up your cluster, kubeadm deploys the kube-scheduler as a pod in the `kube-system` namespace on the master node. You can inspect the scheduler configuration by viewing the pod manifest file:

```bash
cat /etc/kubernetes/manifests/kube-scheduler.yaml
```

This manifest file outlines the options used during the scheduler's deployment. To verify the running process and see the effective options, list the processes on the master node with:

``` bash
ps -aux | grep kube-scheduler
```

An example output might look similar to:

```
root     2477  0.8  1.6  48524 34044 ?        Ssl  17:31   0:08 kube-scheduler -
```