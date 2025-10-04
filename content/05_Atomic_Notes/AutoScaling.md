
# Introduction

In this lesson series, we explore autoscaling, a key topic for the Certified Kubernetes Administrator (CKA) exam. We will focus on Horizontal Pod Autoscaling (HPA) and Vertical Pod Autoscaling (VPA) to give you the essential knowledge in a concise format. For a comprehensive dive into autoscaling, consider the [Kubernetes Autoscaling course on KodeKloud](https://learn.kodekloud.com/user/courses/kubernetes-autoscaling).

Before diving into autoscaling in Kubernetes, let’s review the traditional concepts of scaling using physical servers.

Imagine a past scenario where applications were deployed on physical servers with fixed CPU and memory capacities. When the application load exceeded server capacity, you had two options:

1. Shut down the application and upgrade the existing server by adding more CPU and memory (vertical scaling).
2. If the application could run multiple instances, add another server to distribute the load without downtime (horizontal scaling).

Vertical scaling means enhancing a single server’s resources, whereas horizontal scaling means incorporating additional servers to manage increased load.

![The image illustrates the concepts of horizontal and vertical scaling, showing two server units with CPU and memory resources, and arrows indicating scaling directions.](https://kodekloud.com/kk-media/image/upload/v1752869664/notes-assets/images/CKA-Certification-Course-Certified-Kubernetes-Administrator-Introduction-to-Autoscaling-2025-Updates/scaling-concepts-horizontal-vertical.jpg)

Now, let’s see how these concepts apply to Kubernetes and containerized environments. Kubernetes is designed to dynamically scale containerized applications. Two primary scaling strategies in Kubernetes are:

1. Scaling workloads – adding or removing containers (Pods) in the cluster.
2. Scaling the underlying cluster infrastructure – adding or removing nodes (servers) in the cluster.

![The image illustrates a concept of scaling workloads with orchestration, showing multiple user icons and container icons organized in two sections.](https://kodekloud.com/kk-media/image/upload/v1752869665/notes-assets/images/CKA-Certification-Course-Certified-Kubernetes-Administrator-Introduction-to-Autoscaling-2025-Updates/scaling-workloads-orchestration-diagram.jpg)

To clarify:
- For the cluster infrastructure:
    - **Horizontal scaling:** Add more nodes to the cluster.
    - **Vertical scaling:** Increase resources (CPU, memory) on existing nodes.
    
- For workloads:
    - **Horizontal scaling:** Create more Pods.
    - **Vertical scaling:** Increase resource limits and requests for existing Pods.

There are two approaches to scaling in Kubernetes: manual and automated.

**Manual vs. Automated Scaling**

Manual scaling and automated scaling both have their place. Manual scaling involves direct intervention and command execution, while automated scaling leverages Kubernetes controllers for dynamic adjustments.

## Manual Scaling

For manual scaling, use the following methods:
- **Cluster Infrastructure Horizontal Scaling:**  
    Manually provision new nodes and add them to the cluster:
    
    ```
    kubeadm join ...
    ```
    
- **Workload Horizontal Scaling:**  
    Adjust the number of Pods using:
    
    ```
    kubectl scale --replicas=<number> <workload-type>/<workload-name>
    ```
    
- **Workload Vertical Scaling:**  
    Edit the deployment, stateful set, or ReplicaSet to change resource limits and requests:
    
    ```
    kubectl edit <workload-type>/<workload-name>
    ```
    

Vertical scaling of cluster nodes is less common in Kubernetes because it often requires downtime. In virtualized environments, it may be easier to provision a new VM with higher resources, add it to the cluster, and then decommission the older node.

## Automated Scaling

Automated scaling in Kubernetes simplifies operations:

- **Cluster Infrastructure:**  
    Managed by the Kubernetes Cluster Autoscaler.
    
- **Workload Horizontal Scaling:**  
    Managed by the Horizontal Pod Autoscaler (HPA).
    
- **Workload Vertical Scaling:**  
    Managed by the Vertical Pod Autoscaler (VPA).
    

The manual commands summarized earlier are:

```
kubeadm join ...
```

```
kubectl scale --replicas=<number> <workload-type>/<workload-name>
```

```
kubectl edit <workload-type>/<workload-name>
```

--- 
# Manual Horizontal Scaling

As a Kubernetes administrator, you might manually scale your application to ensure it has enough resources during traffic spikes. Consider the following deployment configuration:

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
        - name: my-app
          image: nginx
          resources:
            requests:
              cpu: "250m"
            limits:
              cpu: "500m"
```

In this configuration, each pod requests 250 millicores (mCPU) and is limited to 500 mCPU. To monitor the resource usage of a pod, you might run:

```
$ kubectl top pod my-app-pod
```

The output would be similar to:

```
NAME         CPU(cores)   MEMORY(bytes)
my-app-pod   450m         350Mi
```

Once you observe the pod’s CPU usage nearing the threshold (for example, at 450 mCPU), you would manually execute a scale command to add more pods:

```
$ kubectl scale deployment my-app --replicas=3
```

>[!Manual Scaling Note]
Manual scaling requires continuous monitoring and timely intervention, which may not be ideal during unexpected surges in traffic.

# Introducing the Horizontal Pod Autoscaler (HPA)

To address the shortcomings of manual scaling, Kubernetes offers the Horizontal Pod Autoscaler (HPA). HPA continuously monitors pod metrics—such as CPU, memory, or custom metrics—using the metrics-server. Based on these metrics, HPA automatically adjusts the number of pod replicas in a deployment, stateful set, or replica set. When resource usage exceeds a preset threshold, HPA increases the pod count; when usage declines, it scales down to conserve resources.

![The image is a diagram explaining the functions of a Horizontal Pod Autoscaler (HPA), highlighting its roles in observing metrics, adding pods, balancing thresholds, and tracking multiple metrics.](https://kodekloud.com/kk-media/image/upload/v1752869662/notes-assets/images/CKA-Certification-Course-Certified-Kubernetes-Administrator-Horizontal-Pod-Autoscaler-HPA-2025-Updates/horizontal-pod-autoscaler-diagram.jpg)

For example, with the nginx deployment above, you can create an HPA by running the command below. This command configures the "my-app" deployment to maintain 50% CPU utilization, scaling the number of pods between 1 and 10:

```
$ kubectl autoscale deployment my-app --cpu-percent=50 --min=1 --max=10
```

Kubernetes will then create an HPA that monitors the CPU metrics (using the pod's 500 mCPU limit) via the metrics-server. If the average CPU utilization exceeds 50%, HPA adjusts the replica count to meet demand without manual input.

To review the status of your HPA, use:

```
$ kubectl get hpa
```

This command shows the current CPU usage, threshold set, and the number of replicas—ensuring that pod counts remain within the defined limits. When the HPA is no longer needed, you can remove it with:

```
$ kubectl delete hpa my-app
```

# Declarative Configuration for HPA

Beyond the imperative approach, you can declare the HPA configuration with a YAML file. Here's an example using the ==autoscaling/v2== API:

```
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: my-app-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: my-app
  minReplicas: 1
  maxReplicas: 10
  metrics:
    - type: Resource
      resource:
        name: cpu
        target:
          type: Utilization
          averageUtilization: 50
```

This configuration ensures that the HPA monitors the CPU utilization of the "my-app" deployment, automatically adjusting the replica count as needed. Note that HPA, integrated into Kubernetes since version 1.23, relies on the metrics-server to obtain resource utilization data.

# Metrics Sources and External Adapters

Kubernetes supports not only the internal metrics-server for collecting CPU or memory metrics but also custom metrics adapters. These adapters can retrieve metrics from other internal sources or external metrics providers like Datadog or Dynatrace through an external adapter. For further details on advanced configurations, please explore our [Kubernetes Autoscaling](https://learn.kodekloud.com/user/courses/kubernetes-autoscaling) course.

![The image is a flowchart illustrating a metrics system architecture, showing the interaction between components like the Metrics Server, Custom Metrics Adapter, and External Adapter, with connections to Datadog and Dynatrace. It includes elements such as HPA (Horizontal Pod Autoscaler) and Workload (Deployment).](https://kodekloud.com/kk-media/image/upload/v1752869663/notes-assets/images/CKA-Certification-Course-Certified-Kubernetes-Administrator-Horizontal-Pod-Autoscaler-HPA-2025-Updates/metrics-system-architecture-flowchart.jpg)

---

# In place Resize of Pods 2025 Updates

In this guide, we explore how to perform in-place resizing of Pod resources—a feature that streamlines updates to resource changes without recreating the entire Pod. This innovative approach reduces downtime, especially for stateful workloads, by updating resource requirements directly on the running Pods.

## Understanding the Default Behavior

By default, Kubernetes (v1.32 and later) replaces the existing Pod when you modify resource requests or limits in a Deployment. Consider the following deployment configuration:

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: my-app
        image: nginx
        resources:
          requests:
            cpu: "250m"
            memory: "256Mi"
          limits:
            cpu: "500m"
            memory: "512Mi"
```

When you update the resource requirements, Kubernetes terminates the existing Pod and creates a new one with the updated resource specifications. This behavior may lead to temporary service disruption.

## The In-Place Update Mechanism

To address the disruption from full Pod recreation, Kubernetes is developing an in-place update mechanism for Pod resources. This feature has been available in alpha since Kubernetes 1.27 and is not enabled by default. It is expected to transition to beta—and eventually be enabled by default—as it matures.

### Example of In-Place Update Manifest

Below is an example where the CPU resource is increased from "250m" to "1". Thanks to the in-place update feature, this change can be applied without deleting the Pod:

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: my-app
        image: nginx
        resources:
          requests:
            cpu: "1"
            memory: "256Mi"
          limits:
            cpu: "500m"
            memory: "512Mi"
```

To activate this feature, enable the in-place Pod vertical scaling feature flag with the following command:

```
$ FEATURE_GATES=InPlacePodVerticalScaling=true
```

Once enabled, you can specify additional resize policy parameters to control restart behavior for each resource. For instance, a policy can ensure that updating CPU resources does not trigger a Pod restart, while memory updates might still require one.

### Configuring Resize Policies

The following manifest demonstrates a resize policy for the CPU resource, allowing an update without restarting the Pod:

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: my-app
        image: nginx
        resizePolicy:
          - resourceName: cpu
            restartPolicy: NotRequired
        resources:
          requests:
            cpu: "250m"
            memory: "256Mi"
          limits:
            cpu: "500m"
            memory: "512Mi"
```

When the in-place update feature is active, increasing the CPU resource value (for example, from "250m" to "1") updates the running Pod directly without termination, ensuring smooth scalability and minimal service interruption.

Note

The in-place resizing feature currently supports only CPU and memory resources. Familiarize yourself with these constraints before implementation.

## Limitations of In-Place Pod Resizing

It is important to note the following limitations with the current implementation of in-place resizing:

- Only CPU and memory resources can be updated in place.
- Changes to Pod QoS classes and certain other attributes are not supported.
- Init containers and ephemeral containers are not eligible for in-place resizing.
- Resource requests and limits, once assigned to a container, cannot be shifted to another container.
- A container's memory limit cannot be reduced below its current usage; if such a request is made, the resize operation will remain in progress until the new memory limit is achievable.
- Windows Pods are not supported by this feature.

## Looking Ahead: Vertical Pod Autoscaler

While this article focused on manually resizing Pod resources, Kubernetes also offers the Vertical Pod Autoscaler (VPA) to automate scaling based on resource usage. VPA continuously monitors your application's resource consumption and adjusts requests and limits as needed, further reducing manual intervention.

---

# Vertical Pod Autoscaling VPA 2025 Updates

In this article, we explore how to optimize Kubernetes workloads by scaling them vertically using the Vertical Pod Autoscaler (VPA). As a Kubernetes administrator, your goal is to ensure that applications always receive optimal resource allocations, such as CPU and memory. Let’s start by examining a typical deployment configuration for a pod that specifies a CPU request of 250 millicores and a limit of 500 millicores:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
        - name: my-app
          image: nginx
          resources:
            requests:
              cpu: "250m"
            limits:
              cpu: "500m"
```

In this setup, the pod cannot use more than 500 millicores of CPU. To monitor its resource consumption, execute the following command (ensure that the metrics server is installed in your cluster):

```bash
$ kubectl top pod my-app-pod
NAME        CPU(cores)   MEMORY(bytes)
my-app-pod  450m         350Mi
```

If the pod's CPU consumption reaches a predefined threshold, you might need to update its resource specifications manually. For example, you can increase the CPU request to "1" while keeping the limit unchanged:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: my-app
        image: nginx
        resources:
          requests:
            cpu: "1"
          limits:
            cpu: "500m"
```

To apply this change, run:

```bash
$ kubectl edit deployment my-app
```

After saving, Kubernetes will terminate the current pod and create a new one with the updated resource configuration.

>[!Note]
Manually updating pods can be time-consuming and error-prone. Kubernetes provides the Vertical Pod Autoscaler (VPA) to automate this process.

Kubernetes distinguishes between scaling methods. While the Horizontal Pod Autoscaler (HPA) adds or removes pods based on demand, the VPA continuously monitors metrics and automatically adjusts the CPU and memory allocation of each pod. Since VPA is not enabled by default, you must install it manually. Start by applying the VPA definition file from the autoscaler GitHub repository:

```bash
$ kubectl apply -f https://github.com/kubernetes/autoscaler/releases/latest/download/vertical-pod-autoscaler.yaml
```

Verify that the VPA components are running in the kube-system namespace:

```bash
$ kubectl get pods -n kube-system | grep vpa
vpa-admission-controller-xxxx   Running
vpa-recommender-xxxx            Running
vpa-updater-xxxx                Running
```

The VPA deployment includes three key components:

1. **VPA Recommender:** Continuously monitors resource usage via the Kubernetes metrics API, analyzes historical and live data, and provides optimized recommendations for CPU and memory.
2. **VPA Updater:** Compares current pod resource settings against recommendations and evicts pods running with suboptimal resources. This eviction triggers the creation of new pods with updated configurations.
3. **VPA Admission Controller:** Intercepts pod creation requests and mutates the pod specification based on the recommender's suggestions, ensuring that new pods start with the ideal resource configuration.

Next, create a VPA resource with a YAML definition. Unlike HPA, the VPA isn’t set up through imperative commands. The example below shows a configuration that monitors the "my-app" deployment, enforces minimum and maximum CPU limits, and uses the "Auto" update mode:

```yaml
apiVersion: autoscaling.k8s.io/v1
kind: VerticalPodAutoscaler
metadata:
  name: my-app-vpa
spec:
  targetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: my-app
  updatePolicy:
    updateMode: "Auto"
  resourcePolicy:
    containerPolicies:
      - containerName: "my-app"
        minAllowed:
          cpu: "250m"
        maxAllowed:
          cpu: "2"
        controlledResources: ["cpu"]
```

In "Auto" mode, the VPA updater behaves similarly to a "recreate" strategy by terminating pods that run with non-optimal resources, allowing new pods to be created with the recommended values. In the future, when Kubernetes supports in-place updates, VPA will update pods’ resources without needing a full restart.

To inspect the resource recommendations provided by VPA for your deployment, run:

```
$ kubectl describe vpa my-app-vpa
```

You might see an output similar to this, which indicates a recommended CPU value of 1.5:

```
Recommendations:
  Target:
    Cpu: 1.5
```

## Comparing Vertical and Horizontal Pod Autoscaling

Understanding when to use VPA versus HPA is crucial for efficient resource management:

|Feature|Vertical Pod Autoscaling (VPA)|Horizontal Pod Autoscaling (HPA)|
|---|---|---|
|Scaling Method|Adjusts CPU and memory settings of individual pods (may restart pods for changes).|Increases or decreases the number of pods to distribute load.|
|Pod Behavior|May cause temporary downtime during pod restarts.|Scales pods seamlessly without interrupting existing ones.|
|Traffic Handling|Less effective for sudden spikes due to restart delays.|Ideal for handling rapid traffic spikes by adding more pods instantly.|
|Cost Optimization|Prevents over-provisioning by matching resource allocation with actual usage.|Reduces operational costs by avoiding underutilized pods.|
|Use Cases|Stateful workloads, databases, JVM-based applications, and AI workloads requiring precise tuning.|Stateless applications, web services, and microservices requiring rapid scaling.|

![The image is a comparison chart highlighting the key differences between Vertical Pod Autoscaling (VPA) and Horizontal Pod Autoscaling (HPA) in Kubernetes, focusing on features like scaling method, pod behavior, traffic handling, cost optimization, and use cases.](https://kodekloud.com/kk-media/image/upload/v1752869688/notes-assets/images/CKA-Certification-Course-Certified-Kubernetes-Administrator-Vertical-Pod-Autoscaling-VPA-2025-Updates/vpa-hpa-comparison-chart-kubernetes.jpg)

Key Takeaway

VPA focuses on optimizing resource allocation for individual pods, while HPA scales the number of pods to meet demand. The choice depends on your application’s workload characteristics and scaling requirements.