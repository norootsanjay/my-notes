# Logging in Docker

Docker containers typically log events to the standard output. Consider the "event simulator" container, which generates random events simulating a web server. When you run this container, it writes log entries such as:

```
docker run kodekloud/event-simulator
2018-10-06 15:57:15,937 - root - INFO - USER1 logged in
2018-10-06 15:57:16,943 - root - INFO - USER2 logged out
2018-10-06 15:57:17,944 - root - INFO - USER3 is viewing page3
2018-10-06 15:57:18,951 - root - INFO - USER4 is viewing page1
2018-10-06 15:57:19,954 - root - INFO - USER1 logged out
2018-10-06 15:57:21,956 - root - INFO - USER1 logged in
2018-10-06 15:57:22,957 - root - INFO - USER3 is viewing page2
2018-10-06 15:57:23,959 - root - INFO - USER1 logged out
2018-10-06 15:57:24,959 - root - INFO - USER2 is viewing page2
2018-10-06 15:57:25,962 - root - INFO - USER4 is viewing page3
2018-10-06 15:57:26,965 - root - INFO - USER3 is viewing page1
2018-10-06 15:57:27,965 - root - INFO - USER3 logged out
2018-10-06 15:57:29,967 - root - INFO - USER1 is viewing page2
```

If you run the container in detached mode using the `-d` flag, the logs will not appear on your terminal immediately. Instead, you can stream them later with:

```
docker run -d kodekloud/event-simulator
docker logs -f <container_id>
```

---

# Logging in Kubernetes

Deploying the same Docker image within a Kubernetes pod leverages Kubernetes' logging capabilities. To get started, create a pod using the following YAML definition:

```
apiVersion: v1
kind: Pod
metadata:
  name: event-simulator-pod
spec:
  containers:
    - name: event-simulator
      image: kodekloud/event-simulator
```

Create the pod with this command:

```
kubectl create -f event-simulator.yaml
```

Once the pod is running, view the live logs using:

```
kubectl logs -f event-simulator-pod
```

This command outputs logs similar to the Docker example:

```
2018-10-06 15:57:15,937 - root - INFO - USER1 logged in
2018-10-06 15:57:16,943 - root - INFO - USER2 logged out
2018-10-06 15:57:17,944 - root - INFO - USER2 is viewing page2
2018-10-06 15:57:18,951 - root - INFO - USER3 is viewing page3
2018-10-06 15:57:20,095 - root - INFO - USER4 is viewing page1
2018-10-06 15:57:21,956 - root - INFO - USER2 logged out
2018-10-06 15:57:21,956 - root - INFO - USER1 logged in
2018-10-06 15:57:23,093 - root - INFO - USER3 is viewing page2
2018-10-06 15:57:24,959 - root - INFO - USER1 logged out
2018-10-06 15:57:25,961 - root - INFO - USER2 is viewing page2
2018-10-06 15:57:25,961 - root - INFO - USER1 logged in
```

>[!Tip]
For more effective troubleshooting, use log filtering and analysis tools in combination with Kubernetes logs.

---

# Logging with Multiple Containers in a Pod

Kubernetes supports pods with multiple containers. If you update your pod definition to include an additional container named `image-processor`, the configuration will look like this:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: event-simulator-pod
spec:
  containers:
    - name: event-simulator
      image: kodekloud/event-simulator
    - name: image-processor
      image: some-image-processor
```

Create the pod using:

```bash
kubectl create -f event-simulator.yaml
```

Attempting to view logs without specifying the container when multiple containers are present will result in an error. Instead, specify the container name to view its logs:

```bash
kubectl logs -f event-simulator-pod event-simulator
```

This command displays log output for the `event-simulator` container:

```
2018-10-06 15:57:15,937 - root - INFO - USER1 logged in
2018-10-06 15:57:16,943 - root - INFO - USER2 logged out
2018-10-06 15:57:17,944 - root - INFO - USER2 is viewing page2
2018-10-06 15:57:18,951 - root - INFO - USER3 is viewing page3
2018-10-06 15:57:19,954 - root - INFO - USER4 is viewing page1
2018-10-06 15:57:20,955 - root - INFO - USER2 logged out
```

>[!Important]
When working with pods that contain multiple containers, always specify the container name in the `kubectl logs` command to avoid errors