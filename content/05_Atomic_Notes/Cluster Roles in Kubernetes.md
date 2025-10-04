> [!Note]
> Cluster Role and Cluster Role Binding allow us to manage permissions across a Kubernetes Cluster.

When you create roles and role bindings without specifying a namespace, they are added to the default namespace and only authorize access within that scope. This works well for namespaced resources—such as pods, deployments, and services—but not for cluster-scoped resources. For example, nodes cannot be assigned to a specific namespace (e.g., "node01" cannot belong to the "dev" namespace). Cluster-scoped resources like nodes and persistent volumes are managed at the cluster level, so they require a different approach.

Most resources (such as pods, replica sets, jobs, deployments, services, and secrets) are namespaced. In contrast, cluster-scoped resources, including nodes and persistent volumes, do not belong to any namespace. The following image clearly illustrates the difference between namespaced resources (e.g., pods, services) and cluster-scoped resources (e.g., nodes, cluster roles):
![[Pasted image 20250819142427.png]]

>[!Understanding]
>Roles and Role Bindings: namespace-level access
>Cluster Role and Cluster Role Bindings: across cluster

# Cluster Role and Role Binding

- [f] Spans Entire Cluster.
- To authorize cluster-scoped resources, such as nodes and persistent volumes.

>[!Example]
>You can define a cluster administrator role that grants the ability to list, retrieve, create, and delete nodes. Alternatively, you might establish a storage administrator role to manage persistent volumes and persistent volume claims.

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: cluster-administrator
rules:
- apiGroups: [""]
  resources: ["nodes"]
  verbs: ["list", "get", "create", "delete"]
```

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: cluster-admin-role-binding
subjects:
  - kind: User
    name: cluster-admin
    apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: ClusterRole
  name: cluster-administrator
  apiGroup: rbac.authorization.k8s.io
```

It's important to note that while cluster roles and role bindings are primarily used for cluster-scoped resources, they can also manage access to namespace-scoped resources. When you bind a cluster role that grants permissions on pods, for instance, the user will have access to pods in every namespace, unlike a namespaced role, which restricts access to a single namespace.
![[Pasted image 20250819150502.png]]

Kubernetes provides several default cluster roles when the cluster is initially set up. Be sure to review these defaults to understand the baseline permissions before creating custom roles.
