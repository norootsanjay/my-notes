## Background

Originally, Docker was the sole supported container runtime for Kubernetes. To enable compatibility with other container runtimes, the Container Runtime Interface (CRI) was introduced. Docker, as a complete platform, bundled several tools together, including:

- The Docker CLI
- The API
- Build tools for creating images
- Volume support
- Security configurations
- The container runtime (runc)
- The daemon (containerd) that managed the runtime

Containerd is now CRI-compatible and functions as a standalone component that interacts directly with Kubernetes. Thanks to this change, Kubernetes no longer depends on Docker’s additional tools since it manages container operations natively.

![The image illustrates container runtimes, showing rkt, containerd, and Docker, with Kubernetes using the Container Runtime Interface (CRI). Docker is marked as deprecated.](https://kodekloud.com/kk-media/image/upload/v1752869700/notes-assets/images/CKA-Certification-Course-Certified-Kubernetes-Administrator-A-note-on-Docker-Deprecation/frame_70.jpg)

## Docker's Role in Modern Development

While Kubernetes has officially deprecated Docker as its runtime, Docker remains the most popular container solution for day-to-day development and build processes. Kubernetes simply leverages containerd for orchestration, but many development practices still benefit from Docker’s extensive toolset.