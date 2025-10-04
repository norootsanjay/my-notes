
# Commands and Arguments in Docker
## Understanding Container Commands

When you run a Docker container using the Ubuntu image, as shown below:

```
docker run ubuntu
```

Docker launches a container based on the Ubuntu image, which starts and immediately exits since it runs a default process that completes quickly. If you inspect the currently running containers:

```
docker ps
```

you will notice that the container is absent because it has already exited. However, viewing all containers—including those that have stopped—with:

```
docker ps -a
```

reveals that the container is in an "exited" state:

```
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                     PORTS
45aacca36850        ubuntu              "/bin/bash"         43 seconds ago      Exited (0) 41 seconds ago
```

This behavior is different from traditional virtual machines. Containers are optimized to run a single task or process, such as hosting a web server, application server, database, or performing a specific computation. Once the task completes, the container stops because its lifecycle is tied directly to the process running inside it.

## Default Commands in Docker Images

Each Docker image contains instructions that define the process to run when a container starts. Many popular images, such as [nginx](https://www.nginx.com/) or [MySQL](https://www.mysql.com/), include a CMD instruction in their Dockerfile that sets the default command. For instance, the nginx image typically has the command `nginx`, and the MySQL image uses `mysqld`.

Consider this Dockerfile snippet for installing and configuring nginx:

```
# Install Nginx.
RUN \
    add-apt-repository -y ppa:nginx/stable && \
    apt-get update && \
    apt-get install -y nginx && \
    rm -rf /var/lib/apt/lists/* && \
    echo "\ndaemon off;" >> /etc/nginx/nginx.conf && \
    chown -R www-data:www-data /var/lib/nginx


# Define mountable directories.
VOLUME ["/etc/nginx/sites-enabled", "/etc/nginx/certs", "/etc/nginx/conf"]


# Define working directory.
WORKDIR /etc/nginx


# Define default command.
CMD ["nginx"]
```

Now, let's examine the Ubuntu image's Dockerfile. Notice that in this example, the default command is set to Bash:

```
# Pull base image.
FROM ubuntu:14.04


# Install necessary packages.
RUN \
    sed -i 's/# \(.*multiverse$\)/\1/g' /etc/apt/sources.list && \
    apt-get update && \
    apt-get -y upgrade && \
    apt-get install -y build-essential software-properties-common byobu curl git htop man unzip vim wget && \
    rm -rf /var/lib/apt/lists/*


# Add configuration files.
ADD root/.bashrc /root/.bashrc
ADD root/.gitconfig /root/.gitconfig
ADD root/.scripts /root/.scripts


# Set environment variables.
ENV HOME /root


# Define working directory.
WORKDIR /root


# Define default command.
CMD ["bash"]
```

Note

Remember: Bash is a shell, not a persistent server process. When the Ubuntu container is launched without an attached terminal, the shell exits immediately.

## Overriding the Default Command

To override the default command for a container, you can append a command to the end of the `docker run` command. For example, this command instructs the container to run `sleep 5`:

```
docker run ubuntu sleep 5
```

In this scenario, the container executes `sleep 5`, waits for five seconds, and then exits.

If you want to permanently change the behavior of the image so that it always runs `sleep 5`, you must create a new image based on Ubuntu and specify the new default command in its Dockerfile. You can specify the command in either of two formats:

1. Shell form:
    
    ```
    FROM ubuntu
    CMD sleep 5
    ```
    
2. JSON array format:
    
    ```
    FROM ubuntu
    CMD ["sleep", "5"]
    ```
    

Build the new image with:

```
docker build -t ubuntu-sleeper .
```

Then run the container using:

```
docker run ubuntu-sleeper
```

By default, this container will sleep for five seconds.

## Configuring ENTRYPOINT for Runtime Arguments

Sometimes, you may want to specify only runtime arguments without changing the default command. In such cases, the ENTRYPOINT instruction is useful. This instruction sets the executable to run when the container starts, and any command-line arguments provided at runtime are appended to it.

Consider the following Dockerfile:

```
FROM ubuntu
ENTRYPOINT ["sleep"]
CMD ["5"]
```

After building and running this image:

```
docker build -t ubuntu-sleeper .
docker run ubuntu-sleeper
```

the container executes `sleep 5` by default. You can override the sleep duration at runtime by specifying a new parameter:

```
docker run ubuntu-sleeper 10
```

This command runs `sleep 10`.

Key Difference

- With CMD alone, runtime arguments replace the default command.
- With ENTRYPOINT, runtime arguments are appended to the specified executable, allowing you to override just the parameters.

## Overriding ENTRYPOINT at Runtime

At times, you might want to completely override the ENTRYPOINT. For example, if you wish to use a different command (like switching from `sleep` to `sleep2.0`), you can do so using the `--entrypoint` flag in the `docker run` command.

Given the Dockerfile:

```
FROM ubuntu
ENTRYPOINT ["sleep"]
CMD ["5"]
```

Build the image:

```
docker build -t ubuntu-sleeper .
```

Running the container without modifications:

```
docker run ubuntu-sleeper
```

will produce an error because `sleep` expects an operand:

```
docker run ubuntu-sleeper
# Output:
# sleep: missing operand
# Try 'sleep --help' for more information.
```

However, running with a new parameter:

```
docker run ubuntu-sleeper 10
```

executes `sleep 10`.

To override the ENTRYPOINT with a different executable, run:

```
docker run --entrypoint sleep2.0 ubuntu-sleeper 10
```

This command starts the container with `sleep2.0 10` (provided that `sleep2.0` is a valid command).

---

# Commands and Args in Kubernetes

Previously, we built a simple Docker image—named "ubuntu-sleeper"—that executes a sleep command for a specified number of seconds. By default, running a container with this image makes it sleep for five seconds. However, you can easily change this behavior by passing a command-line argument.

## Overriding Default Behavior with Arguments

Suppose we want to create a pod using the "ubuntu-sleeper" image. We begin with a basic pod definition where the pod's name and image are specified. When the pod is created, it starts a container that runs the default sleep command (sleeping for five seconds) and then exits. To modify the sleep duration to 10 seconds, simply append an additional argument in the pod specification. Any argument provided in the Docker run command correlates with the `args` property in the pod definition file (formatted as an array).

**Understanding Arguments**

When you append an argument to the Docker run command, it overrides the default parameters defined by the CMD instruction in the Dockerfile.

Consider the following examples:

**Docker commands:**

```
docker run --name ubuntu-sleeper ubuntu-sleeper
docker run --name ubuntu-sleeper ubuntu-sleeper 10
```

Pod definition YAML:

```
apiVersion: v1
kind: Pod
metadata:
  name: ubuntu-sleeper-pod
spec:
  containers:
  - name: ubuntu-sleeper
    image: ubuntu-sleeper
    args: ["10"]
```

## Dockerfile Instructions and Their Mappings

The Dockerfile for the "ubuntu-sleeper" image is defined with both an ENTRYPOINT and a CMD:

```
FROM Ubuntu
ENTRYPOINT ["sleep"]
CMD ["5"]
```

- The **ENTRYPOINT** instruction specifies the command to run when the container starts.
- The **CMD** instruction provides default parameters for that command.

By specifying the `args` field in the pod definition file, the CMD instruction is overridden, which effectively changes the sleep duration from 5 to 10 seconds.

## Overriding the ENTRYPOINT

Now, consider a scenario where you want to override the ENTRYPOINT itself (for example, switching from "sleep" to an alternative command like "sleep2.0"). In Docker, you would use the `--entrypoint` option in the `docker run` command. In Kubernetes, this is achieved by providing the `command` field in the pod definition. Here, the `command` field corresponds to the Dockerfile’s ENTRYPOINT, while the `args` field continues to override the CMD instruction.

Docker command example with overridden ENTRYPOINT:

```
docker run --name ubuntu-sleeper \
  --entrypoint sleep2.0 \
  ubuntu-sleeper 10
```

Pod definition YAML with overridden ENTRYPOINT:

```
apiVersion: v1
kind: Pod
metadata:
  name: ubuntu-sleeper-pod
spec:
  containers:
    - name: ubuntu-sleeper
      image: ubuntu-sleeper
      command: ["sleep2.0"]
      args: ["10"]
```

## Deployment of the Pod

Once your pod definition is ready, deploy the pod using the following command:

```
kubectl create -f pod-definition.yml
```

## Key Fields Summary

Below is a table summarizing the Kubernetes pod definition fields that map to Dockerfile instructions:

|Pod Definition Field|Dockerfile Instruction|Functionality Description|
|---|---|---|
|command|ENTRYPOINT|Specifies the command to run when the container starts (completely replacing Dockerfile ENTRYPOINT).|
|args|CMD|Provides default parameters passed to the command (overriding the Dockerfile CMD).|

>[!Key Takeaway]
Remember that specifying the `command` in a pod definition replaces the Dockerfile's ENTRYPOINT entirely, while the `args` field only overrides the default parameters defined by CMD.