Imagine a cluster with several nodes where each node runs pods serving your applications. What happens if a node goes down? The pods on that node become inaccessible, and depending on your deployment strategy, this could impact your users. For instance, if you have multiple replicas of a "blue" pod, your users will still be served by the remaining replicas. However, if a "green" pod is the only instance running, its unavailability results in downtime.

Kubernetes offers mechanisms to handle such situations:

- If a node comes back online quickly, the pods are simply restarted.
- If a node remains down for more than five minutes, Kubernetes marks the pods on that node as dead. For pods managed by a ReplicaSet, new pods are automatically created on other nodes. In contrast, pods that are not part of a ReplicaSet will not be restarted, potentially leading to downtime.

>[!Quick Recap]
When a node is down for a short duration (less than five minutes) and your workload has multiple replicas, a quick upgrade and reboot might be acceptable.

# Draining a Node

For situations where the recovery time is uncertain, the safer method is to drain the node. Draining involves gracefully terminating the pods running on that node so they are recreated on other nodes. At the same time, draining marks the node as unschedulable (cordoned), preventing new pods from being scheduled on it until explicitly allowed.

After the node's workloads have been safely relocated, you can reboot the node. When it comes back online, it remains unschedulable until you uncordon it, allowing new pods to be scheduled. It is important to note that pods moved to other nodes do not automatically revert to the original node after uncordoning. If they were deleted or if new pods have been scheduled across the cluster, Kubernetes maintains the current distribution.

To perform these operations, run the following commands:

```bash
kubectl drain node-1
kubectl uncordon node-1
```

# Cordon vs. Drain

In addition to draining and uncordoning, Kubernetes provides the cordon command. Unlike drain, cordon only marks a node as unschedulable without terminating or relocating the currently running pods. This ensures that no new pods will be scheduled on the node.

>[!Warning]
Be cautious when using cordon on a node with critical workloads. Since existing pods remain and new pods cannot be scheduled, your application might become overloaded or experience unexpected behavior.

That concludes this lesson on managing node upgrades in a Kubernetes cluster. You are encouraged to practice these techniques in a hands-on lab or test environment to reinforce your understanding of draining, cordoning, and uncordoning nodes.