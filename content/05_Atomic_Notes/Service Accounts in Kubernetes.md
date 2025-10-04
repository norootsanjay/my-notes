There are two main types of accounts in Kubernetes:

- **User Accounts:** Designed for human users like administrators or developers.
- **Service Accounts:** Intended for machine-to-machine interactions or application-specific tasks. For instance, monitoring tools like Prometheus use a service account to query the Kubernetes API for performance metrics, while Jenkins uses one for deploying applications.
![[Pasted image 20250819151637.png]]

# Example: a Kubernetes Dashboard Application

Consider an example: "my Kubernetes dashboard," a basic dashboard application built with Python. This application retrieves a list of Pods from a Kubernetes cluster by sending API requests and subsequently displays the results on a web page. To authenticate its API requests, the application uses a dedicated service account.
![[Pasted image 20250819151725.png]]

## Creating a Service Account

```bash
kubectl create serviceaccount dashboard-sa
```

```bash
kubectl get serviceaccount
```

Upon creation, Kubernetes automatically generates a service account token, stores it as a Secret, and links it to the account. To inspect the details of your service account and its token, execute:
```bash
kubectl describe serviceaccount dashboard-sa
```

![[Screenshot 2025-08-19 at 15.30.39.png]]

```bash
kubectl describe secret dashboard-sa-token-kbbdm
```

![[Screenshot 2025-08-19 at 15.31.02.png]]

This token serves as the authentication bearer token for accessing the Kubernetes API. For example, using curl:

```bash
curl https://192.168.56.70:6443/api -k \
--header "Authorization: Bearer eyJhbgG…"
```

# Automatic Mounting of Service Account Tokens

When deploying third-party applications (such as a custom dashboard or Prometheus) on a Kubernetes cluster, you can have Kubernetes automatically mount the service account token as a volume into the Pod. This token is typically available at the path: ==/var/run/secrets/kubernetes.io/serviceaccount==.
![[Pasted image 20250819153455.png]]
   
[demo](obsidian://open?vault=sanjay&file=Service%20Accounts%20in%20Kubernetes)
