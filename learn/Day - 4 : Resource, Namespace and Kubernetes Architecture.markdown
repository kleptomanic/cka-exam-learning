# Day 4: Kubernetes Architecture

Kubernetes (K8s) is an open-source platform for orchestrating containerized applications, automating deployment, scaling, and management. Its architecture is a distributed system comprising the **Control Plane** and **Worker Nodes**, with components like the **Container Runtime Interface (CRI)** and **Cloud Controller Manager**. This document provides a detailed explanation of Kubernetes resources, namespaces, architecture components (including file locations), the resource request flow, a demo, labs for analyzing control plane pods and viewing logs, and comprehensive resources, based on the official Kubernetes documentation.

---

## Kubernetes Resources

### What Are Resources?
Kubernetes resources are objects that represent the state of the cluster, such as Pods, Services, or Deployments. Each resource is defined by a specification (e.g., in YAML or JSON) and managed by the Kubernetes API.

- **Definition**: A resource is a persistent entity in the Kubernetes system, representing a component (e.g., Pod, Service) or configuration (e.g., ConfigMap, Secret) that the cluster manages.
- **Structure**: Resources have:
  - **API Version**: Specifies the API group and version (e.g., `v1`, `apps/v1`).
  - **Kind**: The type of resource (e.g., `Pod`, `Deployment`).
  - **Metadata**: Includes name, namespace, labels, and annotations.
  - **Spec**: Defines the desired state (e.g., container images, replicas).
  - **Status**: Reflects the current state, updated by Kubernetes.

### Importance of Resources
- **Abstraction**: Resources provide a standardized way to define and manage cluster components.
- **State Management**: Enable declarative configuration, where Kubernetes ensures the actual state matches the desired state.
- **Modularity**: Allow separation of concerns (e.g., Pods for workloads, Services for networking).
- **Extensibility**: Custom Resource Definitions (CRDs) allow users to define custom resources.
- **Automation**: Controllers use resources to automate tasks like scaling or self-healing.

### Types of Resources in Kubernetes
Kubernetes resources are categorized based on their purpose:

1. **Workload Resources**:
   - **Pod**: The smallest deployable unit, containing one or more containers.
   - **Deployment**: Manages stateless applications with replicas and updates.
   - **StatefulSet**: Manages stateful applications with stable identities.
   - **DaemonSet**: Runs a Pod on every node (e.g., for logging agents).
   - **Job/CronJob**: Runs tasks to completion or on a schedule.
2. **Service Resources**:
   - **Service**: Abstracts network access to Pods (e.g., ClusterIP, NodePort, LoadBalancer).
   - **Ingress**: Manages external HTTP/HTTPS traffic with routing rules.
3. **Configuration Resources**:
   - **ConfigMap**: Stores configuration data (e.g., key-value pairs).
   - **Secret**: Stores sensitive data (e.g., passwords, tokens).
4. **Storage Resources**:
   - **PersistentVolume (PV)**: Cluster-wide storage resource.
   - **PersistentVolumeClaim (PVC)**: Requests storage within a namespace.
   - **StorageClass**: Defines storage provisioners and policies.
5. **Security Resources**:
   - **Role/ClusterRole**: Defines permissions for RBAC.
   - **RoleBinding/ClusterRoleBinding**: Binds roles to users or groups.
   - **ServiceAccount**: Provides an identity for Pods to interact with the API.
6. **Cluster Management Resources**:
   - **Namespace**: Isolates resources logically.
   - **Node**: Represents a worker or master node.
   - **ResourceQuota**: Limits resource usage per namespace.
   - **LimitRange**: Sets default resource limits for containers.
7. **Custom Resources**:
   - **CustomResourceDefinition (CRD)**: Allows users to define custom resources (e.g., for operators).

### Interacting with Resources Using `kubectl`
`kubectl` is the primary CLI tool for managing Kubernetes resources. Common commands include:

1. **Create a Resource**:
   ```bash
   kubectl apply -f <file.yaml>
   ```
   - Example: `kubectl apply -f pod.yaml` creates a Pod from a YAML file.
2. **List Resources**:
   ```bash
   kubectl get <resource> [-n <namespace>]
   ```
   - Example: `kubectl get pods -n default` lists Pods in the `default` namespace.
   - Use `--all-namespaces` for cluster-scoped resources: `kubectl get nodes --all-namespaces`.
3. **Describe a Resource**:
   ```bash
   kubectl describe <resource> <name> [-n <namespace>]
   ```
   - Example: `kubectl describe pod my-pod -n default` shows Pod details.
4. **Update a Resource**:
   ```bash
   kubectl edit <resource> <name> [-n <namespace>]
   ```
   - Example: `kubectl edit deployment my-app -n default` modifies a Deployment.
5. **Delete a Resource**:
   ```bash
   kubectl delete <resource> <name> [-n <namespace>]
   ```
   - Example: `kubectl delete pod my-pod -n default` deletes a Pod.
6. **View All Resources in a Namespace**:
   ```bash
   kubectl get all -n <namespace>
   ```
   - Example: `kubectl get all -n kube-system` lists all resources in `kube-system`.
7. **List Resource Types**:
   ```bash
   kubectl api-resources
   ```
   - Shows all available resource types and whether they are namespaced.

#### Example: Creating and Inspecting a Pod
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  namespace: default
spec:
  containers:
  - name: nginx
    image: nginx:latest
```
- **Create**: `kubectl apply -f nginx-pod.yaml`
- **List**: `kubectl get pods -n default`
- **Describe**: `kubectl describe pod nginx-pod -n default`
- **Delete**: `kubectl delete pod nginx-pod -n default`

#### Image: Kubernetes Resource Types
*Description*: A diagram categorizing Kubernetes resources (Workload, Service, Configuration, Storage, Security, Cluster Management) with examples like Pod, Service, ConfigMap, and PersistentVolume.
*Source*: Create a custom diagram using Draw.io or adapt from Kubernetes.io.

![Kubernetes Resource Types](images/resource-types-diagram.png)

---

## Namespaces

### Generic Definition of Namespaces
A **namespace** in Kubernetes is a logical partition within a physical cluster, used to isolate and organize resources for different users, teams, or projects. It acts like a virtual cluster, enabling resource segregation while sharing the same underlying infrastructure.

- **Real-World Scenario Example**:
  - **Scenario**: An e-commerce company uses a Kubernetes cluster to manage its online platform. The development team needs a sandbox for testing new features, the QA team requires a stable environment for testing, and the production team runs the live application. To avoid conflicts (e.g., duplicate resource names) and ensure resource limits, they create namespaces `dev`, `qa`, and `prod`. Each namespace hosts a `frontend` Service and `backend` Deployment, isolated with specific RBAC rules and resource quotas (e.g., `dev` limited to 4 CPU cores).
  - **Benefit**: Teams work independently, avoiding interference, and the cluster remains organized, secure, and efficient.

### Features of Namespaces
- **Resource Isolation**: Resources in one namespace are isolated from others.
- **Naming**: Allows identical resource names across namespaces (e.g., `frontend` Service in `dev` and `prod`).
- **Access Control**: RBAC policies restrict access to specific namespaces.
- **Resource Quotas**: Limit CPU, memory, or object counts per namespace.
- **Multi-Tenancy**: Supports multiple teams or projects in one cluster.
- **Visibility**: Resources are visible only to authorized users within the namespace.

### Purpose of Namespaces
- **Organization**: Group related resources (e.g., application components).
- **Isolation**: Separate environments (e.g., dev, prod) or teams.
- **Resource Management**: Apply quotas to prevent resource overuse.
- **Security**: Restrict access via RBAC or network policies.
- **Scalability**: Enable multi-tenancy without separate clusters.

### Default Namespaces
Kubernetes creates several default namespaces during cluster setup, each with specific purposes:

1. **default**:
   - **Purpose**: Default namespace for resources when none is specified.
   - **Use Case**: Used for user applications in small clusters or when isolation is not needed.
   - **Example**: `kubectl apply -f pod.yaml` places a Pod in `default`.
2. **kube-system**:
   - **Purpose**: Hosts system Pods and resources for cluster operations.
   - **Use Case**: Runs control plane components (e.g., `kube-apiserver`, `etcd`), networking components (e.g., `coredns`, `kube-proxy`), and add-ons (e.g., `cloud-controller-manager`).
   - **Example**: `kubectl get pods -n kube-system` lists system Pods.
3. **kube-public**:
   - **Purpose**: Contains publicly accessible resources, even without authentication.
   - **Use Case**: Stores cluster metadata (e.g., `ConfigMap` with cluster info).
   - **Example**: Rarely used but may contain public cluster details.
4. **kube-node-lease**:
   - **Purpose**: Manages node lease objects for tracking node health.
   - **Use Case**: Kubelet reports node heartbeats to detect failures.
   - **Example**: `kubectl get lease -n kube-node-lease` shows node leases.

### Namespace-Bound vs. Cluster-Scoped Resources
- **Namespace-Scoped Resources**:
  - Exist within a specific namespace and are isolated.
  - Examples:
    - Pods
    - Deployments
    - ReplicaSets
    - StatefulSets
    - Services
    - ConfigMaps
    - Secrets
    - Jobs/CronJobs
    - Ingress
    - Role/RoleBinding
    - ResourceQuotas
    - LimitRanges
  - **Explanation**: These resources are namespace-specific, preventing conflicts (e.g., a `frontend` Service in `dev` is separate from `prod`).
- **Cluster-Scoped Resources**:
  - Exist globally across the cluster, not tied to a namespace.
  - Examples:
    - Nodes
    - PersistentVolumes (PVs)
    - ClusterRoles/ClusterRoleBindings
    - Namespaces
    - StorageClasses
    - CustomResourceDefinitions (CRDs)
  - **Explanation**: These resources affect the entire cluster (e.g., Nodes are physical machines, PVs are cluster-wide storage).

### Finding Namespace-Scoped and Cluster-Scoped Resources
Use `kubectl` to identify resource scopes:
1. **List All Resources with Scope**:
   ```bash
   kubectl api-resources
   ```
   - **Output** (partial):
     ```plaintext
     NAME                              SHORTNAMES   APIVERSION                     NAMESPACED   KIND
     pods                              po           v1                             true         Pod
     services                          svc          v1                             true         Service
     nodes                             no           v1                             false        Node
     namespaces                        ns           v1                             false        Namespace
     ```
   - `NAMESPACED` column shows `true` (namespace-scoped) or `false` (cluster-scoped).
2. **List Namespace-Scoped Resources**:
   ```bash
   kubectl api-resources --namespaced=true
   ```
   - Lists resources like Pods, Services, Deployments.
3. **List Cluster-Scoped Resources**:
   ```bash
   kubectl api-resources --namespaced=false
   ```
   - Lists resources like Nodes, Namespaces, PersistentVolumes.

### Viewing Available Namespaces
1. **List All Namespaces**:
   ```bash
   kubectl get namespaces
   ```
   - **Output**:
     ```plaintext
     NAME              STATUS   AGE
     default           Active   5d
     kube-system       Active   5d
     kube-public       Active   5d
     kube-node-lease   Active   5d
     ```
2. **Describe a Namespace**:
   ```bash
   kubectl describe namespace <namespace>
   ```
   - Example: `kubectl describe namespace kube-system`
   - Shows metadata, labels, annotations, and quotas.
3. **List Resources in a Namespace**:
   ```bash
   kubectl get <resource> -n <namespace>
   ```
   - Example: `kubectl get pods -n kube-system`
4. **List All Resources in a Namespace**:
   ```bash
   kubectl get all -n <namespace>
   ```
   - Example: `kubectl get all -n default`

#### Image: Kubernetes Namespace Structure
*Description*: A diagram showing namespaces (`default`, `kube-system`, `kube-public`, `kube-node-lease`) with namespace-scoped resources (Pods, Services) and cluster-scoped resources (Nodes, PersistentVolumes).
*Source*: Create using Draw.io or adapt from [Kubernetes.io > Concepts > Namespaces](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/).

![Kubernetes Namespace Structure](images/namespace-structure.png)

---

## Kubernetes Architecture Components

Below is a breakdown of control plane and worker node components, including roles, functionalities, and important file locations (runtime, config files, log files). File locations are based on typical Linux setups (e.g., `kubeadm` or Minikube), but paths may vary.

### 1. Control Plane Components
The control plane manages the cluster’s state and runs on master nodes.

#### a. API Server (kube-apiserver)
- **Role**: Central management interface exposing the Kubernetes API.
- **Functionality**:
  - Processes RESTful requests and updates etcd.
  - Authenticates and authorizes requests (RBAC, OIDC).
  - Gateway for control plane and worker node communication.
- **Key File Locations**:
  - **Runtime**: `/usr/local/bin/kube-apiserver` (binary).
  - **Config Files**:
    - `/etc/kubernetes/manifests/kube-apiserver.yaml` (static Pod manifest).
    - `/etc/kubernetes/apiserver` (configuration flags).
    - `/etc/kubernetes/pki/` (TLS certificates, e.g., `apiserver.crt`).
  - **Log Files**:
    - `/var/log/kube-apiserver.log` (if configured).
    - Container logs: `kubectl logs kube-apiserver-<node> -n kube-system`.
- **Example**: `kubectl apply -f deployment.yaml` sends a request to the API server.

#### b. etcd
- **Role**: Distributed key-value store for cluster state.
- **Functionality**:
  - Stores all cluster data (Pods, ConfigMaps, etc.).
  - Uses Raft consensus for replication.
  - Supports watch mechanisms for state changes.
- **Key File Locations**:
  - **Runtime**: `/usr/local/bin/etcd` (binary).
  - **Config Files**:
    - `/etc/kubernetes/manifests/etcd.yaml` (static Pod manifest).
    - `/etc/etcd/etcd.conf` (etcd configuration).
    - `/etc/kubernetes/pki/etcd/` (TLS certificates).
  - **Log Files**:
    - `/var/log/etcd.log` (if configured).
    - Container logs: `kubectl logs etcd-<node> -n kube-system`.
  - **Data Directory**: `/var/lib/etcd/` (etcd data storage).
- **Example**: Stores Pod specs for scheduling.

#### c. Scheduler (kube-scheduler)
- **Role**: Assigns Pods to nodes based on resource requirements and policies.
- **Functionality**:
  - Evaluates nodes using CPU, memory, affinity, and taints/tolerations.
  - Watches API server for unscheduled Pods.
- **Key File Locations**:
  - **Runtime**: `/usr/local/bin/kube-scheduler` (binary).
  - **Config Files**:
    - `/etc/kubernetes/manifests/kube-scheduler.yaml` (static Pod manifest).
    - `/etc/kubernetes/scheduler.conf` (scheduler policy).
  - **Log Files**:
    - `/var/log/kube-scheduler.log` (if configured).
    - Container logs: `kubectl logs kube-scheduler-<node> -n kube-system`.
- **Example**: Assigns a Pod to a node with sufficient resources.

#### d. Controller Manager (kube-controller-manager)
- **Role**: Runs controllers to maintain desired cluster state.
- **Functionality**:
  - Manages controllers (e.g., Replication, Node, Deployment, StatefulSet).
  - Reconciles actual vs. desired state via API server.
- **Key File Locations**:
  - **Runtime**: `/usr/local/bin/kube-controller-manager` (binary).
  - **Config Files**:
    - `/etc/kubernetes/manifests/kube-controller-manager.yaml` (static Pod manifest).
    - `/etc/kubernetes/controller-manager.conf` (configuration flags).
  - **Log Files**:
    - `/var/log/kube-controller-manager.log` (if configured).
    - Container logs: `kubectl logs kube-controller-manager-<node> -n kube-system`.
- **Example**: Creates a new Pod if one crashes in a ReplicaSet.

#### e. Cloud Controller Manager (cloud-controller-manager)
- **Role**: Integrates with cloud provider APIs for cloud-specific resources.
- **Functionality**:
  - Manages cloud node, route, service, and volume controllers.
  - Provisions cloud resources (e.g., AWS ELB, GCE Persistent Disk).
- **Key File Locations**:
  - **Runtime**: `/usr/local/bin/cloud-controller-manager` (binary, if installed).
  - **Config Files**:
    - `/etc/kubernetes/manifests/cloud-controller-manager.yaml` (static Pod manifest).
    - `/etc/kubernetes/cloud-config` (cloud provider configuration).
  - **Log Files**:
    - `/var/log/cloud-controller-manager.log` (if configured).
    - Container logs: `kubectl logs cloud-controller-manager-<node> -n kube-system`.
- **Example**: Provisions an AWS ELB for a LoadBalancer Service.

#### Image: Kubernetes Control Plane Components
*Description*: A diagram showing control plane components (API Server, etcd, Scheduler, Controller Manager, Cloud Controller Manager) and their interactions.
*Source*: [Kubernetes.io > Concepts > Architecture](https://kubernetes.io/docs/concepts/architecture/) or create using Draw.io.

![Kubernetes Control Plane Components](images/control-plane-diagram.png)

### 2. Worker Node Components
Worker nodes run application workloads and host Pods.

#### a. Kubelet
- **Role**: Ensures containers in Pods are running and healthy.
- **Functionality**:
  - Communicates with API server for Pod specs.
  - Manages containers via CRI.
  - Monitors and reports container status.
- **Key File Locations**:
  - **Runtime**: `/usr/local/bin/kubelet` (binary).
  - **Config Files**:
    - `/etc/kubernetes/kubelet.conf` (API server connection).
    - `/var/lib/kubelet/config.yaml` (kubelet configuration).
    - `/etc/kubernetes/pki/` (TLS certificates).
  - **Log Files**:
    - `/var/log/kubelet.log` (if configured).
    - Systemd logs: `journalctl -u kubelet`.
- **Example**: Restarts a crashed container.

#### b. Kube-Proxy
- **Role**: Manages network connectivity for Pods and Services.
- **Functionality**:
  - Maintains network rules (e.g., iptables, IPVS).
  - Implements Service abstractions (ClusterIP, NodePort, LoadBalancer).
- **Key File Locations**:
  - **Runtime**: `/usr/local/bin/kube-proxy` (binary).
  - **Config Files**:
    - `/etc/kubernetes/kube-proxy.yaml` (config or manifest).
    - `/var/lib/kube-proxy/config.conf` (kube-proxy configuration).
  - **Log Files**:
    - `/var/log/kube-proxy.log` (if configured).
    - Container logs: `kubectl logs kube-proxy-<node> -n kube-system`.
- **Example**: Sets up iptables for a ClusterIP Service.

#### c. Container Runtime Interface (CRI)
- **Role**: Interface for kubelet to manage containers via runtimes.
- **Functionality**:
  - Standardizes container lifecycle and image management.
  - Supports runtimes like containerd, CRI-O, or Docker (via dockershim).
- **Key File Locations** (for containerd):
  - **Runtime**: `/usr/local/bin/containerd` (binary).
  - **Config Files**:
    - `/etc/containerd/config.toml` (containerd configuration).
    - `/var/run/containerd/containerd.sock` (CRI socket).
  - **Log Files**:
    - `/var/log/containerd.log` (if configured).
    - Systemd logs: `journalctl -u containerd`.
- **Example**: Kubelet uses CRI to start an nginx container via containerd.

#### Image: Kubernetes Worker Node Components
*Description*: A diagram showing a worker node with kubelet, kube-proxy, and CRI interacting with a container runtime (e.g., containerd).
*Source*: [Kubernetes.io > Concepts > Components](https://kubernetes.io/docs/concepts/overview/components/) or create using Draw.io.

![Kubernetes Worker Node Components](images/worker-node-diagram.png)

---

## Resource Request Flow

The resource request flow describes how a resource (e.g., a Pod) is created and managed through interactions between control plane and worker nodes.

### Steps
1. **User Submits Request**:
   - User runs `kubectl apply -f pod.yaml`, sending a Pod spec to the API server via HTTP/REST.
2. **API Server Processing**:
   - Authenticates, authorizes, and validates the request.
   - Stores the Pod spec in etcd (in the specified namespace, e.g., `default`).
3. **Scheduler Assignment**:
   - Scheduler watches API server for unassigned Pods.
   - Assigns the Pod to a node based on resource requirements, affinity, and taints.
   - Updates Pod spec with `nodeName` in etcd via API server.
4. **Kubelet Execution**:
   - Kubelet on the assigned node retrieves the Pod spec.
   - Uses CRI to instruct the container runtime to pull images and start containers.
   - Reports status to API server.
5. **Kube-Proxy Networking**:
   - Updates network rules (e.g., iptables) for Service-related Pods.
   - Cloud controller manager provisions cloud resources (e.g., LoadBalancer) if needed.
6. **Controller Manager Monitoring**:
   - Controllers (e.g., ReplicaSet) ensure desired state (e.g., recreate crashed Pods).
7. **User Feedback**:
   - User checks status with `kubectl get pods`.

### Example
- **Command**: `kubectl apply -f pod.yaml` (Pod with `nginx` image).
- **Flow**:
  1. `kubectl` sends Pod spec to API server.
  2. API server stores spec in etcd.
  3. Scheduler assigns Pod to a node.
  4. Kubelet uses CRI to start `nginx` container.
  5. Kube-proxy sets up networking.
  6. Controller manager monitors state.
  7. User runs `kubectl get pods`.

#### Image: Resource Request Flow
*Description*: A flowchart of Pod creation: user submits to API server, API server stores in etcd, scheduler assigns to node, kubelet creates containers via CRI, kube-proxy handles networking.
*Source*: Create using Draw.io or adapt from Kubernetes.io.

![Resource Request Flow](images/resource-request-flow.png)

#### Image: Kubernetes Cluster Architecture
*Description*: A diagram of a Kubernetes cluster, showing control plane nodes (API Server, etcd, Scheduler, Controller Manager, Cloud Controller Manager) and worker nodes (Kubelet, Kube-Proxy, CRI), with namespaces isolating resources.
*Source*: [Kubernetes.io > Concepts > Architecture](https://kubernetes.io/docs/concepts/architecture/) or create using Draw.io.

![Kubernetes Cluster Architecture](images/kubernetes-cluster-architecture.png)

---

## Demo: List System Pods

Kubernetes runs system Pods in the `kube-system` namespace for cluster operations.

### Command
```bash
kubectl get pods -n kube-system
```

### Explanation
- **`kubectl`**: CLI for Kubernetes API.
- **`get pods`**: Lists Pods.
- **`-n kube-system`**: Targets the `kube-system` namespace.
- **Expected Output**:
  ```bash
  NAME                                       READY   STATUS    RESTARTS   AGE
  coredns-5d78c9869d-4z5t6                  1/1     Running   0          2d
  coredns-5d78c9869d-7x8p2                  1/1     Running   0          2d
  etcd-minikube                              1/1     Running   0          2d
  kube-apiserver-minikube                    1/1     Running   0          2d
  kube-controller-manager-minikube            1/1     Running   0          2d
  kube-proxy-6v2m5                           1/1     Running   0          2d
  kube-scheduler-minikube                    1/1     Running   0          2d
  cloud-controller-manager-minikube           1/1     Running   0          2d
  storage-provisioner                        1/1     Running   0          2d
  ```

#### Image: kubectl get pods Output
*Description*: A screenshot of `kubectl get pods -n kube-system` output, showing system Pods.
*Source*: Capture a screenshot or use a mockup tool.

![kubectl get pods Output](images/kubectl-get-pods-output.png)

---

## Labs: Analyzing Control Plane Pods and Viewing Logs

These labs provide hands-on tasks to analyze control plane pods and inspect Pod and containerd logs.

### Lab 1: Analyze a Control Plane Pod
**Objective**: Inspect a control plane Pod (e.g., `kube-apiserver`, `etcd`, or `cloud-controller-manager`) in the `kube-system` namespace for configuration, status, and logs.

#### Task 1.1: Describe a Control Plane Pod
```bash
kubectl describe pod <pod-name> -n kube-system
```
- **Example**: `kubectl describe pod kube-apiserver-minikube -n kube-system`
- **Purpose**: View Pod details (node, containers, events, labels).
- **Expected Output** (partial):
  ```plaintext
  Name:         kube-apiserver-minikube
  Namespace:    kube-system
  Node:         minikube/192.168.49.2
  Status:       Running
  Containers:
    kube-apiserver:
      Image:         k8s.gcr.io/kube-apiserver:v1.26.0
      Command:
        kube-apiserver
        --advertise-address=192.168.49.2
        ...
  Events:
    Type    Reason     Age   From               Message
    ----    ------     ----  ----               -------
    Normal  Scheduled  2d    default-scheduler  Successfully assigned kube-system/kube-apiserver-minikube to minikube
  ```

#### Task 1.2: Inspect Pod Logs
```bash
kubectl logs <pod-name> -n kube-system
```
- **Example**: `kubectl logs kube-apiserver-minikube -n kube-system`
- **Purpose**: Check logs for errors or operational details (e.g., API request handling).
- **Expected Output**: Varies, showing API request logs or errors (e.g., authentication issues).

#### Task 1.3: View Pod Configuration
```bash
kubectl get pod <pod-name> -n kube-system -o yaml
```
- **Example**: `kubectl get pod kube-apiserver-minikube -n kube-system -o yaml`
- **Purpose**: Inspect the full Pod spec (container images, commands, arguments).
- **Expected Output** (partial):
  ```yaml
  apiVersion: v1
  kind: Pod
  metadata:
    name: kube-apiserver-minikube
    namespace: kube-system
  spec:
    containers:
    - name: kube-apiserver
      image: k8s.gcr.io/kube-apiserver:v1.26.0
      command:
      - kube-apiserver
      - --advertise-address=192.168.49.2
      ...
  ```

#### Task 1.4: Check Configuration File (Optional)
If you have access to the master node:
```bash
cat /etc/kubernetes/manifests/kube-apiserver.yaml
```
- **Purpose**: View the static Pod manifest for `kube-apiserver`.
- **Note**: Requires node access (e.g., SSH in a `kubeadm` cluster).

#### Why This Matters
Analyzing control plane Pods helps troubleshoot issues (e.g., crashes, misconfigurations) and understand their setup.

#### Image: kubectl describe pod Output
*Description*: A screenshot of `kubectl describe pod kube-apiserver-minikube -n kube-system`.
*Source*: Capture a screenshot or use a mockup tool.

![kubectl describe pod Output](images/kubectl-describe-pod-output.png)

### Lab 2: View Pod Logs and Containerd Logs
**Objective**: Inspect logs of a control plane Pod and the containerd runtime to diagnose issues or monitor operations.

#### Task 2.1: View Logs of a Control Plane Pod
```bash
kubectl logs <pod-name> -n kube-system
```
- **Example**: `kubectl logs etcd-minikube -n kube-system`
- **Purpose**: Check Pod logs for runtime information or errors (e.g., etcd database operations).
- **Expected Output**: Varies, showing etcd events like key-value updates or replication logs.
- **Note**: Use `--tail=50` to limit output to the last 50 lines or `-f` to follow logs in real-time.

#### Task 2.2: View Containerd Logs
If you have access to the node running containerd (e.g., via SSH):
```bash
journalctl -u containerd
```
- **Purpose**: Inspect containerd logs for container lifecycle events (e.g., image pulls, container starts) or CRI-related issues.
- **Expected Output**: Varies, showing containerd activities like:
  ```plaintext
  Jun 16 21:01:45 minikube containerd[1234]: time="2025-06-16T21:01:45.123456789+05:45" level=info msg="Container created" id=abc123
  Jun 16 21:01:46 minikube containerd[1234]: time="2025-06-16T21:01:46.123456789+05:45" level=info msg="Image pulled" image=nginx:latest
  ```
- **Alternative**: If containerd logs to a file (depends on setup):
  ```bash
  cat /var/log/containerd.log
  ```
- **Note**: Requires node access. In Minikube, use `minikube ssh` to access the node.

#### Task 2.3: Correlate Pod and Containerd Logs
- **Steps**:
  1. Run `kubectl logs kube-apiserver-minikube -n kube-system` to check for errors (e.g., container crashes).
  2. Run `journalctl -u containerd | grep kube-apiserver` to find containerd logs related to the `kube-apiserver` container.
- **Purpose**: Identify issues like image pull failures or CRI communication errors by cross-referencing Pod and containerd logs.
- **Example Issue**: If Pod logs show a container crash and containerd logs show `image not found`, the issue may be a misconfigured image in the Pod spec.

#### Task 2.4: View Logs for a User Pod (Optional)
Create a sample Pod to inspect its logs:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  namespace: default
spec:
  containers:
  - name: nginx
    image: nginx:latest
```
- **Create**: `kubectl apply -f nginx-pod.yaml`
- **View Logs**: 
  ```bash
  kubectl logs nginx-pod -n default
  ```
- **Purpose**: Compare user Pod logs (e.g., nginx access logs) with control plane Pod logs to understand different logging patterns.
- **Expected Output**: Shows nginx server logs, e.g.:
  ```plaintext
  192.168.49.1 - - [16/Jun/2025:21:01:45 +0545] "GET / HTTP/1.1" 200 612
  ```

#### Why This Matters
Inspecting Pod and containerd logs is critical for diagnosing runtime issues, such as container crashes, image pull errors, or CRI misconfigurations. Control plane logs reveal cluster management issues, while containerd logs provide low-level container runtime details.

#### Image: kubectl logs Output
*Description*: A screenshot of `kubectl logs etcd-minikube -n kube-system` output, showing etcd logs.
*Source*: Capture a screenshot or use a mockup tool.

![kubectl logs Output](images/kubectl-logs-output.png)

---

## Resources
- **Kubernetes.io > Concepts > Architecture**: [https://kubernetes.io/docs/concepts/architecture/](https://kubernetes.io/docs/concepts/architecture/)
  - Covers control plane and worker node components.
- **Kubernetes.io > Concepts > Overview > Components**: [https://kubernetes.io/docs/concepts/overview/components/](https://kubernetes.io/docs/concepts/overview/components/)
  - Details all components, including CRI and Cloud Controller Manager.
- **Kubernetes.io > Concepts > Containers > Container Runtimes**: [https://kubernetes.io/docs/setup/production-environment/container-runtimes/](https://kubernetes.io/docs/setup/production-environment/container-runtimes/)
  - CRI and runtime configuration.
- **Kubernetes.io > Concepts > Architecture > Cloud Controller Manager**: [https://kubernetes.io/docs/concepts/architecture/cloud-controller/](https://kubernetes.io/docs/concepts/architecture/cloud-controller/)
  - Cloud integration details.
- **Kubernetes.io > Concepts > Overview > Working with Kubernetes Objects > Namespaces**: [https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/)
  - Namespace details and use cases.
- **Kubernetes.io > Reference > Command Line Tools > kubectl**: [https://kubernetes.io/docs/reference/kubectl/](https://kubernetes.io/docs/reference/kubectl/)
  - `kubectl` commands for resource management.
- **Kubernetes.io > Tasks > Debug Cluster**: [https://kubernetes.io/docs/tasks/debug/debug-cluster/](https://kubernetes.io/docs/tasks/debug/debug-cluster/)
  - Troubleshooting guide for components and resources.
- **Kubernetes.io > Tutorials > Kubernetes Basics**: [https://kubernetes.io/docs/tutorials/kubernetes-basics/](https://kubernetes.io/docs/tutorials/kubernetes-basics/)
  - Interactive tutorials for `kubectl` and resources.
- **Kubernetes.io > Reference > Architecture > CRI**: [https://kubernetes.io/docs/reference/architecture/container-runtime-interface/](https://kubernetes.io/docs/reference/architecture/container-runtime-interface/)
  - CRI technical specification.
- **GitHub: Kubernetes Enhancement Proposals (KEPs)**: [https://github.com/kubernetes/enhancements](https://github.com/kubernetes/enhancements)
  - KEPs for CRI and Cloud Controller Manager.
- **Katacoda Kubernetes Playgrounds**: [https://www.katacoda.com/courses/kubernetes/](https://www.katacoda.com/courses/kubernetes/)
  - Hands-on environment for `kubectl` and namespaces.
- **Kubernetes.io > Tutorials > Hello Minikube**: [https://kubernetes.io/docs/tutorials/hello-minikube/](https://kubernetes.io/docs/tutorials/hello-minikube/)
  - Beginner-friendly cluster setup tutorial.

---

## Additional Insights
- **High Availability**: Use multiple control plane nodes with replicated etcd and API servers.
- **Networking**: Kube-proxy and CNI plugins (e.g., Calico) handle networking; Cloud Controller Manager integrates with cloud networking.
- **CRI**: Use containerd or CRI-O; Docker is deprecated.
- **Namespace Management**: Create namespaces with `kubectl create namespace <name>`; apply quotas with `kubectl create quota`.
- **Security**: Secure API server with RBAC, encrypt etcd, use secure cloud credentials.
- **Troubleshooting**:
  - Check logs with `kubectl logs` or `journalctl`.
  - Use `kubectl describe` and `kubectl get events` for diagnostics.
- **Minikube vs. Production**: Minikube runs components on one node; production clusters distribute them.

---

## Summary
Kubernetes manages resources (Pods, Services, etc.) using a control plane (API Server, etcd, Scheduler, Controller Manager, Cloud Controller Manager) and worker nodes (Kubelet, Kube-Proxy, CRI). Namespaces isolate resources, with default namespaces (`default`, `kube-system`, `kube-public`, `kube-node-lease`) serving specific roles. The resource request flow coordinates Pod creation across components. Labs analyze control plane pods and logs (Pod and containerd), and `kubectl` commands manage resources and namespaces. Use the provided resources for deeper study.

For further exploration, try creating custom resources, configuring namespaces, or troubleshooting CRI issues. Contact me for assistance!