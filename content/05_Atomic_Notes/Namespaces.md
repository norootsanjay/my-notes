![[Pasted image 20250812193120.png]]
# Default Namespace and System Namespaces

By default, when you create objects such as pods, deployments, and services in your cluster, they are placed within a specific namespace (similar to being "inside a house"). The default namespace is automatically created during the Kubernetes cluster setup. Additionally, several system namespaces are created at startup:

- **kube-system**: Contains core system components like network services and DNS, segregated from user operations to prevent accidental changes.
- **kube-public**: Intended for resources that need to be publicly accessible across users.