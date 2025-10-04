- [f] Static pods are created directly by the Kubelet without the intervention of the API server or other control plane components.

The kubelet is configured to monitor a designated directory on the host where pod definition files are stored. The kubelet periodically scans this directory, reads available files, and creates the corresponding pods. It also monitors these pods and ensures they remain running. For example, if an application crashes, the kubelet restarts it; if an updated file is detected, the kubelet recreates the pod to apply the changes; and if a file is removed, the corresponding pod is also deleted. These pods, created solely by the kubelet, are known as static pods.

It's important to note that only pod-level resources can be created this way. Higher-level abstractions such as ReplicaSets, Deployments, or Services depend on other control plane components (e.g., the replication and deployment controllers) and cannot be managed via the static pod definition files.

# Configuring the Static Pods Directory

You can place static pods in any directory on the host. The directory location is provided to the kubelet at startup by using the `--pod-manifest-path` option. Below is an example configuration in the kubelet service file:

```
ExecStart=/usr/local/bin/kubelet \
  --container-runtime=remote \
  --container-runtime-endpoint=unix:///var/run/containerd/containerd.sock \
  --pod-manifest-path=/etc/kubernetes/manifests \
  --kubeconfig=/var/lib/kubelet/kubeconfig \
  --network-plugin=cni \
  --register-node=true \
  --v=2
```

Alternatively, you can specify a configuration file that includes the manifest directory path. For example:

```
# kubelet.service
ExecStart=/usr/local/bin/kubelet \
  --container-runtime=remote \
  --container-runtime-endpoint=unix:///var/run/containerd/containerd.sock \
  --config=kubeconfig.yaml \
  --kubeconfig=/var/lib/kubelet/kubeconfig \
  --network-plugin=cni \
  --register-node=true \
  --v=2
```

```
# kubeconfig.yaml
staticPodPath: /etc/kubernetes/manifests
```

Many clusters created using kubeadm adopt this approach. When inspecting an existing cluster, first check for the ==--pod-manifest-path== option in the kubelet service file. If it is not present, look for the ==--config== option to locate the configuration file and check its ==staticPodPath== setting.

You can verify the status of static pods using container runtime commands. For instance, running the Docker command below on a host shows the containers spawned by static pods:

```
docker ps
CONTAINER ID        IMAGE                   COMMAND                  CREATED             STATUS              PORTS               NAMES
8e5d4c4db7b6        busybox                 "sh -c 'echo Hello K…'"  20 seconds ago      Up 20 seconds       k8s_myapp-container_myapp-pod-host01_default_48e37fb432f2e06350e76786bd0bac66_0
f6737e1149cb        k8s.gcr.io/pause:3.1     "/pause"                 24 seconds ago      Up 23 seconds       k8s_POD_myapp-pod-host01_default_48e37fb432f2e06350e76786bd0bac66_0
```

In a standalone scenario, use `docker ps` because the kube-apiserver is not available to process Kubernetes API requests.

# Behavior When Part of a Cluster

When a node is part of a Kubernetes cluster, the kube-apiserver instructs the kubelet to create pods via its HTTP API endpoint. In this mixed mode, the kubelet handles pod definitions provided both from the static pod directory and from the API server. Whenever the kubelet creates a static pod in this configuration, it also creates a mirror object in the kube-apiserver. This mirror object is read-only and can be viewed with `kubectl get pods`, but you cannot modify or delete it through the API. To update a static pod, modify the file in the node’s manifest directory.

For example, running the command below on the master node will display the static pod mirror:

```
kubectl get pods
NAME                READY   STATUS              RESTARTS   AGE
static-web-node01   0/1     ContainerCreating   0          29s
```

Notice that the pod name includes the node name (e.g., "node01") to indicate its origin.

![The image illustrates the architecture of static pods in Kubernetes, showing components like kube-apiserver, ETCD cluster, kube-scheduler, and kubelet with YAML files for pod configuration.](https://kodekloud.com/kk-media/image/upload/v1752869910/notes-assets/images/CKA-Certification-Course-Certified-Kubernetes-Administrator-Static-Pods/frame_340.jpg)

# Static Pods vs. DaemonSets

A common question that arises is how static pods differ from DaemonSets. The table below summarizes the key differences between the two:

| Feature                    | Static Pods                                          | DaemonSets                                                 |
| -------------------------- | ---------------------------------------------------- | ---------------------------------------------------------- |
| Creation Source            | Directly managed by the kubelet                      | Managed by the DaemonSet controller via the kube-apiserver |
| Control Plane Involvement  | No API server interaction                            | Requires kube-apiserver communication                      |
| Use Case                   | Typically used for critical control plane components | Ensures a copy of a pod runs on every node                 |
| Interaction with Scheduler | Ignored by the kube-scheduler                        | Ignored by the kube-scheduler                              |

Static pods are especially useful for deploying control plane components themselves. Once the kubelet is installed on all master nodes, you can create pod definition files for essential components like the API server and controller manager. By placing these files in the designated manifest folder, the kubelet ensures they are running as pods and restarts them automatically if they fail. When you check the pods in the kube-system namespace, you'll see these control plane components running as pods—a standard configuration in clusters set up using kubeadm.

![The image compares Static PODs and DaemonSets, highlighting their creation sources, deployment purposes, and interaction with the Kube-Scheduler.](https://kodekloud.com/kk-media/image/upload/v1752869911/notes-assets/images/CKA-Certification-Course-Certified-Kubernetes-Administrator-Static-Pods/frame_490.jpg)