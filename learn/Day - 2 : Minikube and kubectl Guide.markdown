# Day 2: Deploying Minikube

This guide covers setting up Minikube with the Docker driver, a single-node Kubernetes cluster for local development, and `kubectl`, the Kubernetes CLI. It includes installation instructions, common commands, features, hands-on labs, and requirements for `kubectl` to interact with Kubernetes clusters.

## Minikube: Overview

**Minikube** runs a single-node Kubernetes cluster locally, ideal for learning, testing, and developing Kubernetes applications. It combines the control plane and worker node components into a single environment, simplifying Kubernetes setup.

### Importance of Minikube
- **Local Testing Environment**: Provides a safe, isolated Kubernetes cluster for experimentation without impacting production systems.
- **Cost-Effective**: Eliminates the need for cloud-based clusters, reducing costs for development and learning.
- **Simplified Setup**: Abstracts complex cluster configuration, making Kubernetes accessible to beginners.
- **Production Simulation**: Mimics production Kubernetes environments for testing applications and configurations.
- **CI/CD Development**: Enables testing of Kubernetes manifests and Helm charts locally.
- **Community Support**: Backed by the Kubernetes community with extensive documentation and add-ons.

### Key Features of Minikube
- **Cross-Platform**: Runs on Linux, macOS, and Windows.
- **Multiple Drivers**: Supports Docker, VirtualBox, HyperKit (macOS), Hyper-V (Windows), and KVM (Linux).
- **Add-Ons**: Enables features like Ingress, Kubernetes Dashboard, and local registry.
- **Customizable**: Configure Kubernetes version, CPU, and memory.
- **Networking**: Supports port forwarding and service exposure.
- **Dashboard**: Web-based UI via `minikube dashboard`.
- **Multi-Cluster**: Manage multiple clusters using profiles.
- **Local Development**: Simulates production Kubernetes for safe testing.

### Minikube Installation
**Prerequisites**:
- 2 CPUs, 2GB RAM, 20GB disk space.
- Docker (for Docker driver; ensure Docker Desktop or Docker Engine is installed and running).
- `kubectl` (Kubernetes CLI).

**Installation Commands**:
- **Linux**:
  ```bash
  curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
  sudo install minikube-linux-amd64 /usr/local/bin/minikube
  ```
- **macOS**:
  ```bash
  brew install minikube
  ```
- **Windows**:
  - Via Chocolatey: `choco install minikube`
  - Or download from [Minikube GitHub](https://github.com/kubernetes/minikube/releases).

**Verify Installation**:
```bash
minikube version
```

### Deploying Minikube with Docker Driver
1. Ensure Docker is installed and running:
   ```bash
   docker --version
   docker ps
   ```
2. Start Minikube with the Docker driver:
   ```bash
   minikube start --driver=docker
   ```
   - Optional: Customize resources or Kubernetes version:
     ```bash
     minikube start --driver=docker --cpus=4 --memory=8g --kubernetes-version=v1.26.0
     ```
3. Verify the cluster:
   ```bash
   minikube status
   kubectl cluster-info
   ```

### Common Minikube Commands
- Start cluster: `minikube start --driver=docker [--cpus=4] [--memory=8g] [--kubernetes-version=v1.26.0]`
- Check status: `minikube status`
- Enable add-on: `minikube addons enable dashboard`
- Open Dashboard: `minikube dashboard`
- Get cluster IP: `minikube ip`
- SSH into cluster: `minikube ssh`
- Pause cluster: `minikube pause`
- Stop cluster: `minikube stop`
- Delete cluster: `minikube delete`
- View logs: `minikube logs`
- List add-ons: `minikube addons list`
- Add nodes: `minikube node add`

## kubectl: Overview

**kubectl** is the command-line tool for interacting with Kubernetes clusters. It communicates with the Kubernetes API server to manage resources, inspect cluster state, and perform administrative tasks.

### Importance of kubectl
- **Universal Control**: Manages any Kubernetes cluster (local or remote) with a consistent interface.
- **Resource Management**: Enables creation, updating, and deletion of resources like pods and services.
- **Automation**: Supports scripting and CI/CD integration for automated workflows.
- **Debugging**: Provides tools for logs, container execution, and port-forwarding for troubleshooting.
- **Flexibility**: Supports both imperative and declarative management.
- **Ecosystem Integration**: Works with tools like Helm, Kustomize, and Minikube.

### What kubectl Needs to Interact with a Kubernetes Cluster
To function, `kubectl` requires:
- **Kubeconfig File**: A configuration file (default: `~/.kube/config`) specifying:
  - **Clusters**: API server endpoint (e.g., Minikube’s IP and port).
  - **Users**: Authentication details (e.g., certificates, tokens).
  - **Contexts**: Cluster, user, and namespace combinations.
  - Minikube auto-configures this file.
- **API Server Access**: Network connectivity to the Kubernetes API server (e.g., `https://192.168.49.2:8443`).
- **Authentication**: Valid credentials (e.g., client certificates or tokens).
- **Permissions**: RBAC permissions for the user to perform actions.
- **kubectl Binary**: Installed and in the system PATH.

**Verify Configuration**:
```bash
kubectl config view
kubectl config get-contexts
```

### kubectl Installation
- **Linux**:
  ```bash
  curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
  sudo install kubectl /usr/local/bin/
  ```
- **macOS**:
  ```bash
  brew install kubectl
  ```
- **Windows**:
  - Via Chocolatey: `choco install kubernetes-cli`
  - Or download from [Kubernetes releases](https://kubernetes.io/releases/).

**Verify Installation**:
```bash
kubectl version --client
```

### Commonly Used kubectl Commands
Below is a detailed description of commonly used `kubectl` commands, categorized by their purpose:

#### Cluster Management
- **`kubectl cluster-info`**: Displays the cluster’s control plane and add-on service endpoints (e.g., CoreDNS). Useful for verifying connectivity to the Kubernetes API server.
  - Example: Shows `https://192.168.49.2:8443` for Minikube’s control plane.
- **`kubectl get nodes`**: Lists all nodes in the cluster, including their status and roles (e.g., control-plane or worker).
  - Example: `kubectl get nodes --show-labels` to include node labels.
- **`kubectl config get-contexts`**: Lists available contexts (combinations of cluster, user, and namespace) in the kubeconfig file.
  - Example: Shows `minikube` as the current context after starting Minikube.
- **`kubectl config use-context <context-name>`**: Switches to a specific context (e.g., `minikube`).
  - Example: `kubectl config use-context minikube`.

#### Resource Management
- **`kubectl get <resource>`**: Lists resources like pods, services, or deployments. Supports flags like `-n <namespace>` for namespace scoping or `--all-namespaces`.
  - Example: `kubectl get pods -n kube-system` lists system pods.
- **`kubectl describe <resource> <name>`**: Provides detailed information about a specific resource, including events, status, and configuration.
  - Example: `kubectl describe pod my-pod` shows pod details.
- **`kubectl apply -f <file.yaml>`**: Creates or updates resources from a YAML/JSON file in a declarative manner.
  - Example: `kubectl apply -f pod.yaml` applies a pod definition.
- **`kubectl delete <resource> <name>`**: Deletes a specific resource.
  - Example: `kubectl delete pod my-pod`.
- **`kubectl create <resource>`**: Creates resources imperatively (less common than `apply`).
  - Example: `kubectl create namespace test`.

#### Pod Operations
- **`kubectl run <name> --image=<image> --restart=Never`**: Creates a pod imperatively with the specified image.
  - Example: `kubectl run nginx --image=nginx --restart=Never`.
- **`kubectl logs <pod-name>`**: Retrieves logs from a pod’s container (add `-c <container-name>` for multi-container pods).
  - Example: `kubectl logs my-pod`.
- **`kubectl exec -it <pod-name> -- <command>`**: Runs a command inside a pod’s container, typically for debugging.
  - Example: `kubectl exec -it my-pod -- bash`.
- **`kubectl port-forward <pod-name> <local-port>:<pod-port>`**: Forwards a local port to a pod’s port for testing.
  - Example: `kubectl port-forward my-pod 8080:80`.

#### Debugging
- **`kubectl get events`**: Lists cluster events, useful for diagnosing issues like pod failures.
  - Example: `kubectl get events -n default`.
- **`kubectl top pod`**: Displays CPU and memory usage for pods (requires metrics server).
  - Example: `kubectl top pod my-pod`.
- **`kubectl describe node <node-name>`**: Shows node details, including resource allocation and conditions.
  - Example: `kubectl describe node minikube`.

#### Namespace Management
- **`kubectl create namespace <name>`**: Creates a new namespace for organizing resources.
  - Example: `kubectl create namespace dev`.
- **`kubectl get <resource> -n <namespace>`**: Lists resources in a specific namespace.
  - Example: `kubectl get pods -n dev`.
- **`kubectl config set-context --current --namespace=<namespace>`**: Sets the default namespace for commands.
  - Example: `kubectl config set-context --current --namespace=dev`.

## Demo: Setting Up Minikube with Docker Driver and Checking Cluster
1. **Start Minikube**:
   ```bash
   minikube start --driver=docker
   ```
   - Initializes a cluster using the Docker driver.
   - Optional: `minikube start --driver=docker --cpus=4 --memory=8g`.
2. **Verify Cluster**:
   ```bash
   kubectl cluster-info
   ```
   - Shows control plane and CoreDNS endpoints.
3. **Check Status**:
   ```bash
   minikube status
   ```
   - Confirms cluster components are running.
4. **Enable Dashboard**:
   ```bash
   minikube dashboard
   ```
   - Opens the Kubernetes Dashboard.

## Lab Exercises: Minikube-Based Tasks

### Lab 1: Adding Nodes to Minikube Cluster
**Objective**: Add a new node to an existing Minikube cluster and verify its integration.
*Note*: Minikube’s multi-node support is experimental.
1. Start a single-node Minikube cluster:
   ```bash
   minikube start --driver=docker
   ```
2. Add a new node:
   ```bash
   minikube node add
   ```
   - Adds a worker node (e.g., `minikube-m02`).
3. Verify nodes:
   ```bash
   kubectl get nodes
   ```
   - Should show `minikube` (control-plane) and `minikube-m02` (worker).
4. Label the new node:
   ```bash
   kubectl label nodes minikube-m02 role=worker
   ```
5. Deploy a pod to the new node:
   ```bash
   kubectl run test-pod --image=nginx --restart=Never --node-selector="role=worker"
   ```
6. Verify pod placement:
   ```bash
   kubectl get pods -o wide
   ```
   - Confirm `test-pod` is on `minikube-m02`.
7. Clean up:
   ```bash
   kubectl delete pod test-pod
   minikube delete
   ```

### Lab 2: Starting a Multi-Node Cluster
**Objective**: Create a Minikube cluster with multiple nodes at startup and deploy a pod to a worker node.
*Note*: This lab uses `--nodes` to initialize a multi-node cluster, distinct from adding nodes to an existing cluster.
1. Start a multi-node cluster:
   ```bash
   minikube start --driver=docker --nodes 2
   ```
   - Creates one control-plane node and one worker node.
2. Verify nodes:
   ```bash
   kubectl get nodes
   ```
   - Should show `minikube` and `minikube-m02`.
3. Label the worker node:
   ```bash
   kubectl label nodes minikube-m02 role=worker
   ```
4. Deploy a pod to the worker node:
   ```bash
   kubectl run test-pod --image=nginx --restart=Never --node-selector="role=worker"
   ```
5. Verify pod placement:
   ```bash
   kubectl get pods -o wide
   ```
   - Confirm `test-pod` is on `minikube-m02`.
6. Clean up:
   ```bash
   kubectl delete pod test-pod
   minikube delete
   ```

### Lab 3: Getting Node Information
**Objective**: Gather detailed node information in the Minikube cluster.
1. Start Minikube:
   ```bash
   minikube start --driver=docker
   ```
2. List nodes:
   ```bash
   kubectl get nodes
   ```
   - Note the node name (e.g., `minikube`).
3. Describe a node:
   ```bash
   kubectl describe node minikube
   ```
   - Review resources, conditions, and events.
4. Check resource usage:
   ```bash
   kubectl top node
   ```
   - Requires metrics server (`minikube addons enable metrics-server`).
5. Get node labels:
   ```bash
   kubectl get nodes --show-labels
   ```
6. Add a custom label:
   ```bash
   kubectl label nodes minikube env=dev
   ```
7. Verify label:
   ```bash
   kubectl get nodes --show-labels
   ```
8. Clean up:
   ```bash
   kubectl label nodes minikube env-
   minikube stop
   ```

### Lab 4: SSH into Minikube
**Objective**: Access the Minikube node via SSH to inspect the cluster environment.
1. Start Minikube:
   ```bash
   minikube start --driver=docker
   ```
2. SSH into the Minikube node:
   ```bash
   minikube ssh
   ```
3. Explore the node:
   - Check Docker containers:
     ```bash
     docker ps
     ```
   - View Kubernetes system files:
     ```bash
     ls /etc/kubernetes
     ```
   - Check running processes:
     ```bash
     ps aux
     ```
4. Exit SSH:
   ```bash
   exit
   ```
5. Clean up:
   ```bash
   minikube stop
   ```

### Lab 5: Enable Minikube Dashboard Add-On
**Objective**: Enable and explore the Kubernetes Dashboard add-on in Minikube.
1. Start Minikube:
   ```bash
   minikube start --driver=docker
   ```
2. List available add-ons:
   ```bash
   minikube addons list
   ```
3. Enable the Dashboard add-on:
   ```bash
   minikube addons enable dashboard
   ```
4. Access the Dashboard:
   ```bash
   minikube dashboard
   ```
   - Opens the Kubernetes Dashboard in your browser.
   - Explore cluster resources (e.g., nodes, pods, namespaces).
5. Verify Dashboard pod:
   ```bash
   kubectl get pods -n kubernetes-dashboard
   ```
   - Check that the Dashboard pod is running.
6. Disable the Dashboard:
   ```bash
   minikube addons disable dashboard
   ```
7. Clean up:
   ```bash
   minikube stop
   ```

## Resources
- [Minikube Documentation](https://minikube.sigs.k8s.io/docs/)
- [Kubernetes Minikube Setup](https://kubernetes.io/docs/setup/learning-environment/minikube/)
- [kubectl Documentation](https://kubernetes.io/docs/reference/kubectl/)

## Cheatsheet: Minikube and kubectl Commands
### Minikube Commands
- Install (Linux):
  ```bash
  curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
  sudo install minikube-linux-amd64 /usr/local/bin/minikube
  ```
- Install (macOS): `brew install minikube`
- Install (Windows): `choco install minikube`
- Start cluster: `minikube start --driver=docker [--cpus=4] [--memory=8g] [--nodes=2]`
- Check status: `minikube status`
- Enable add-on: `minikube addons enable dashboard`
- Open Dashboard: `minikube dashboard`
- Get cluster IP: `minikube ip`
- SSH into cluster: `minikube ssh`
- Stop cluster: `minikube stop`
- Delete cluster: `minikube delete`
- View logs: `minikube logs`
- List add-ons: `minikube addons list`
- Add node: `minikube node add`

### kubectl Commands
- Install (Linux):
  ```bash
  curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
  sudo install kubectl /usr/local/bin/
  ```
- Install (macOS): `brew install kubectl`
- Install (Windows): `choco install kubernetes-cli`
- Verify: `kubectl version --client`
- Cluster info: `kubectl cluster-info`
- List nodes: `kubectl get nodes [--show-labels]`
- List pods: `kubectl get pods [--all-namespaces | -n <namespace>]`
- Create pod: `kubectl run <name> --image=<image> --restart=Never`
- Describe resource: `kubectl describe <resource> <name>`
- View logs: `kubectl logs <pod-name>`
- Exec into pod: `kubectl exec -it <pod-name> -- bash`
- Port-forward: `kubectl port-forward <pod-name> <local-port>:<pod-port>`
- Apply file: `kubectl apply -f <file.yaml>`
- Delete resource: `kubectl delete <resource> <name>`
- View events: `kubectl get events`
- Resource usage: `kubectl top pod`
- Create namespace: `kubectl create namespace <name>`
- Set namespace: `kubectl config set-context --current --namespace=<name>`
- Label node: `kubectl label nodes <node-name> <key>=<value>`

## Notes
- **Troubleshooting**:
  - Minikube logs: `minikube logs`
  - Reset cluster: `minikube delete && minikube start --driver=docker`
  - Ensure Docker is running.
- **kubectl Configuration**:
  - Uses `~/.kube/config`.
  - Minikube auto-configures context.
  - View config: `kubectl config view`
- **Best Practices**:
  - Use `kubectl apply` for declarative management.
  - Organize with namespaces.
  - Test in Minikube before production.
- **Multi-Node Note**: Minikube’s multi-node feature is experimental and may vary in stability.