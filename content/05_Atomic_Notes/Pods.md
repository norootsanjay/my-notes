With Kubernetes, the goal is to run containers on worker nodes, but rather than deploying containers directly, Kubernetes encapsulates them within an object called a pod. A pod represents a single instance of an application and is the smallest deployable unit in Kubernetes.

In the simplest scenario, a single-node Kubernetes cluster may run one instance of your application inside a Docker container encapsulated by a pod.

When user load increases, you can scale your application by spinning up additional instances—each running in its own pod. This approach isolates each instance, allowing Kubernetes to distribute the pods across available nodes as needed.

![The image illustrates a Kubernetes cluster with a pod containing a Python application, showing user interaction and node structure.](https://kodekloud.com/kk-media/image/upload/v1752869731/notes-assets/images/CKA-Certification-Course-Certified-Kubernetes-Administrator-Pods/frame_110.jpg)

Instead of adding more containers to the same pod, additional pods are created. For instance, running two instances in separate pods allows the load to be shared across the node or even across multiple nodes if the demand escalates and additional cluster capacity is required.

![The image illustrates a Kubernetes cluster with multiple nodes, each containing pods running Python applications, and one pod marked with an error.](https://kodekloud.com/kk-media/image/upload/v1752869732/notes-assets/images/CKA-Certification-Course-Certified-Kubernetes-Administrator-Pods/frame_150.jpg)

Scaling Pods

Remember, scaling an application in Kubernetes involves increasing or decreasing the number of pods, not the number of containers within a single pod.

Typically, each pod hosts a single container running your main application. However, a pod can also contain multiple containers, which are usually complementary rather than redundant. For example, you might include a helper container alongside your main application container to support tasks like data processing or file uploads. Both containers in the pod share the same network namespace (allowing direct communication via localhost), storage volumes, and lifecycle events, ensuring they start and stop together.

![The image illustrates a Kubernetes multi-container pod setup, showing two containers within a pod on a node, labeled as "Helper Containers."](https://kodekloud.com/kk-media/image/upload/v1752869733/notes-assets/images/CKA-Certification-Course-Certified-Kubernetes-Administrator-Pods/frame_230.jpg)

To better understand the concept, consider a basic Docker example. Suppose you initially deploy your application with a simple command:

```
docker run python-app
```

When the load increases, you may launch additional instances manually:

```
docker run python-app
docker run python-app
docker run python-app
docker run python-app
```

Now, if your application needs a helper container that communicates with each instance, managing links, custom networks, and shared volumes manually becomes complex. You’d have to run commands like:

```
docker run helper --link app1
docker run helper --link app2
docker run helper --link app3
docker run helper --link app4
```

With Kubernetes pods, these challenges are resolved automatically. When a pod is defined with multiple containers, they share storage, the network namespace, and lifecycle events—ensuring seamless coordination and simplifying management.

Even if your current application design uses one container per pod, Kubernetes enforces the pod abstraction. This design prepares your application for future scaling and architectural changes, even though multi-container pods remain less common. This article primarily focuses on single-container pods for clarity.

## Deploying Pods

A common method to deploy pods is using the `kubectl run` command. For example, the following command creates a pod that deploys an instance of the nginx Docker image, pulling it from a Docker repository:

```
kubectl run nginx --image nginx
```

Once deployed, you can verify the pod's status with the `kubectl get pods` command. Initially, the pod might be in a "ContainerCreating" state, followed by a transition to the "Running" state as the application container becomes active. Below is an example session:

```
kubectl get pods
# Output:
# NAME                   READY   STATUS              RESTARTS   AGE
kubectl get pods
# Output after a few seconds:
# NAME                   READY   STATUS    RESTARTS   AGE
# nginx-8586cf59-whssr   1/1     Running   0          8s
```

At this stage, note that external access to the nginx web server has not been configured. The service is accessible only within the node. In a future article, we will explore configuring external access through Kubernetes networking and services.

Next Steps

After mastering pod deployment, advance to networking and service configuration to expose your applications to end users.

That concludes our discussion on Kubernetes Pods. Proceed to the demo section to see these concepts in action, and stay tuned for the next article!

Kubernetes leverages YAML files to define objects such as Pods, ReplicaSets, Deployments, and Services. These definitions adhere to a consistent structure, with four essential top-level properties: apiVersion, kind, metadata, and spec.

## Top-Level Fields in a Kubernetes YAML File

Every Kubernetes definition file must include the following four fields:

```
apiVersion:
kind:
metadata:
spec:
```

1. **apiVersion**  
    This field indicates the version of the Kubernetes API you are using. For a Pod, set `apiVersion: v1`. Depending on the object you define, you might need different versions such as apps/v1, extensions/v1beta1, etc.
    
2. **kind**  
    This specifies the type of object being created. In this lesson, since we're creating a Pod, you'll define it as `kind: Pod`. Other objects might include ReplicaSet, Deployment, or Service.
    
3. **metadata**  
    The metadata section provides details about the object, including its name and labels. It is represented as a dictionary. It is essential to maintain consistent indentation for sibling keys to ensure proper YAML nesting. For example:
    
    ```
    apiVersion: v1
    kind: Pod
    metadata:
      name: myapp-pod
      labels:
        app: myapp
    spec:
    ```
    
    Indentation Reminder
    
    Make sure that the properties under metadata (like name and labels) are indented to the same level. This is crucial for correct YAML parsing.
    
4. **spec**  
    The spec section provides specific configuration details for the object. For a Pod, this is where you define its containers. Since a Pod can run multiple containers, the `containers` field is an array. In our example, with a single container, the array has just one item. The dash (`-`) indicates a list item, and each container must be defined with at least `name` and `image` keys.
    

Below is the complete YAML configuration for our Pod:

```
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

## Creating and Verifying the Pod

After you have saved your configuration (for example, as `pod-definition.yaml`), use the following command to create the Pod:

```
kubectl create -f pod-definition.yaml
```

Once the Pod is created, you can verify its status by listing all Pods:

```
kubectl get pods
```

You should see output similar to this:

```
NAME         READY   STATUS    RESTARTS   AGE
myapp-pod    1/1     Running   0          20s
```

To view detailed information about the Pod, run:

```
kubectl describe pod myapp-pod
```

This command provides extensive details, including metadata, node assignment, container specifics, and event history such as scheduling, volume mounting, and container start-up. Here is an example output:

```
Name:         myapp-pod
Namespace:    default
Node:         minikube/192.168.99.100
Start Time:   Sat, 03 Mar 2018 14:26:14 +0800
Labels:       app=myapp
Annotations:  <none>
Status:       Running
IP:           172.17.0.24
Containers:
  nginx:
    Container ID:   docker://830bb56c8c42a860b4b70e9c1488faelbc38663e49186bc2f5a78e7688b8c9d
    Image:          nginx
    Image ID:       docker-pullable://nginx@sha256:4771d09578c7ca65299e110b3ee1c0a2592f5ea2618d32e4ffe7a4cab1c5de
    Port:           <none>
    State:          Running
      Started:      Sat, 03 Mar 2018 14:26:21 +0800
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-x95w7 (ro)


Conditions:
  Type              Status
  Initialized       True
  Ready             True
  PodScheduled      True


Events:
  Type    Reason                 Age   From               Message
  ----    ------                 ----  ----               -------
  Normal  Scheduled              27s   default-scheduler  Successfully assigned myapp-pod to minikube
  Normal  SuccessfulMountVolume  27s   minikube           MountVolume.SetUp succeeded for volume "default-token-x95w7"
  Normal  Pulling                27s   minikube           pulling image "nginx"
  Normal  Pulled                 27s   minikube           Successfully pulled image "nginx"
  Normal  Created                27s   minikube           Created container
  Normal  Started                27s   minikube           Started container
```

Tip

Using `kubectl describe` helps you gain detailed insights into the internal state of your Pod, which can be invaluable for troubleshooting.