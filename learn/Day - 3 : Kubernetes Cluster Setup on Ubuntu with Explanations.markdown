# Day - 3 : Kubernetes Cluster Setup on Ubuntu

This guide provides a detailed, step-by-step process to set up a Kubernetes cluster on Ubuntu 22.04 using `kubeadm`, with Calico as the Container Network Interface (CNI) plugin. It includes explanations for each command to clarify their purpose and functionality. The process covers system preparation, control plane initialization, Calico installation, worker node joining, and cluster verification for a multi-node setup.

## Prerequisites

Ensure all nodes (control plane and worker) meet these requirements:

- **Operating System**: Ubuntu 22.04.
- **Hardware**:
  - Minimum 2 CPUs.
  - At least 2 GB RAM (4 GB recommended for control plane).
  - At least 20 GB disk space.
- **Networking**:
  - Reliable internet connection.
  - Full connectivity between nodes.
  - Unique hostname, MAC address, and product UUID per node.
  - Open ports: 6443 (API server), 10250 (kubelet), 30000–32767 (NodePort services).
- **Software**:
  - Swap disabled.
  - `containerd` as the container runtime.
  - `kubeadm`, `kubelet`, and `kubectl` installed.

## Step-by-Step Installation Process

### Step 1: Disable Swap

Kubernetes requires swap to be disabled to ensure accurate resource scheduling, as swap usage can interfere with pod scheduling decisions.

```bash
sudo swapoff -a
```
- **Explanation**: Temporarily disables swap space on the system, preventing the OS from using disk-based virtual memory.

```bash
sudo sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab
```
- **Explanation**: Modifies the `/etc/fstab` file by commenting out swap entries (adding `#` at the start of lines containing "swap") to prevent swap from re-enabling after a reboot. The `sed` command uses a regular expression to match and comment out the lines.

### Step 2: Set Up Hostnames

Unique hostnames are necessary for Kubernetes to identify nodes in the cluster.

```bash
sudo hostnamectl set-hostname <hostname>
```
- **Explanation**: Sets the system’s hostname to `<hostname>` (e.g., `master-node` for the control plane, `worker-node1` for a worker). The `hostnamectl` command updates the system’s hostname persistently.

```bash
exec bash
```
- **Explanation**: Starts a new bash session to apply the hostname change immediately in the current terminal, ensuring commands recognize the new hostname without requiring a logout.

### Step 3: Update the /etc/hosts File

Map hostnames to IP addresses for proper name resolution across the cluster.

```bash
sudo nano /etc/hosts
```
- **Explanation**: Opens the `/etc/hosts` file in the `nano` text editor with root privileges for editing, allowing you to add hostname-to-IP mappings.

Add entries like:
```
10.0.0.2 master-node
10.0.0.3 worker-node1
```
- **Explanation**: Maps IP addresses to hostnames (replace with your nodes’ actual IPs). This ensures nodes can resolve each other’s hostnames during cluster communication. Save and exit (`Ctrl+X`, `Y`, `Enter`).

### Step 4: Set Up IPv4 Bridge

Configure kernel modules and networking settings to support Kubernetes networking, enabling container traffic routing.

```bash
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
overlay
br_netfilter
EOF
```
- **Explanation**: Creates a file `/etc/modules-load.d/k8s.conf` with `overlay` and `br_netfilter` kernel modules, ensuring they load on boot. `overlay` supports container storage layers, and `br_netfilter` enables network bridge filtering for iptables.

```bash
sudo modprobe overlay
sudo modprobe br_netfilter
```
- **Explanation**: Loads the `overlay` and `br_netfilter` kernel modules immediately, without requiring a reboot, to enable container and network functionality.

```bash
cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-iptables  = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward                 = 1
EOF
```
- **Explanation**: Creates a file `/etc/sysctl.d/k8s.conf` with networking parameters: `net.bridge.bridge-nf-call-iptables=1` and `net.bridge.bridge-nf-call-ip6tables=1` allow bridged traffic to pass through iptables for IPv4 and IPv6, respectively; `net.ipv4.ip_forward=1` enables IP forwarding for routing between containers.

```bash
sudo sysctl --system
```
- **Explanation**: Applies the networking parameters from `/etc/sysctl.d/` without rebooting, making them active immediately.

### Step 5: Install Kubernetes Tools

Install `kubelet`, `kubeadm`, and `kubectl` to manage the Kubernetes cluster.

```bash
sudo apt-get update
```
- **Explanation**: Updates the package index to ensure the latest package information is available before installing software.

```bash
sudo apt-get install -y apt-transport-https ca-certificates curl
```
- **Explanation**: Installs `apt-transport-https` (enables HTTPS package sources), `ca-certificates` (verifies SSL certificates), and `curl` (fetches files over HTTP/HTTPS) to support secure package downloads.

```bash
sudo mkdir -p /etc/apt/keyrings
```
- **Explanation**: Creates the `/etc/apt/keyrings` directory to store GPG keys for verifying package authenticity.

```bash
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.31/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
```
- **Explanation**: Downloads the Kubernetes repository’s GPG key and converts it to a binary format (`--dearmor`) for APT, saving it as `/etc/apt/keyrings/kubernetes-apt-keyring.gpg` to verify package integrity.

```bash
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.31/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
```
- **Explanation**: Adds the Kubernetes APT repository to `/etc/apt/sources.list.d/kubernetes.list`, specifying the GPG key for signature verification and the repository URL for version 1.26 packages.

```bash
sudo apt-get update
```
- **Explanation**: Refreshes the package index to include the newly added Kubernetes repository.

```bash
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
```
- **Explanation**: Installs specific versions (`1.31`) of `kubelet` (node agent), `kubeadm` (cluster bootstrap tool), and `kubectl` (CLI for cluster management) with `-y` to auto-confirm. `apt-mark hold` prevents these packages from being automatically updated to maintain version stability.

### Step 6: Install and Configure Containerd

Install `containerd` as the container runtime and configure it for Kubernetes.

```bash
sudo apt-get install -y containerd
```
- **Explanation**: Installs `containerd`, a lightweight container runtime used by Kubernetes to manage containers.

```bash
sudo mkdir -p /etc/containerd
```
- **Explanation**: Creates the `/etc/containerd` directory to store `containerd` configuration files.

```bash
sudo sh -c "containerd config default > /etc/containerd/config.toml"
```
- **Explanation**: Generates a default `containerd` configuration and saves it as `/etc/containerd/config.toml`. The `sh -c` runs the command in a shell, redirecting output to the file.

```bash
sudo sed -i 's/ SystemdCgroup = false/ SystemdCgroup = true/' /etc/containerd/config.toml
```
- **Explanation**: Modifies `config.toml` to set `SystemdCgroup = true`, enabling systemd cgroups for compatibility with Kubernetes’ resource management.

```bash
sudo systemctl restart containerd
sudo systemctl restart kubelet
```
- **Explanation**: Restarts the `containerd` and `kubelet` services to apply configuration changes, ensuring they use the updated settings.

```bash
sudo systemctl enable kubelet
```
- **Explanation**: Configures the `kubelet` service to start automatically on system boot.

### Step 7: Initialize Kubernetes Cluster

Initialize the control plane on the master node to set up the cluster.

```bash
sudo kubeadm config images pull
```
- **Explanation**: Downloads container images required for control plane components (e.g., `kube-apiserver`, `etcd`) to ensure they are available before initialization.

```bash
sudo kubeadm init --pod-network-cidr=10.10.0.0/16
```
- **Explanation**: Initializes the control plane, setting up components like `kube-apiserver`, `kube-controller-manager`, `kube-scheduler`, `etcd`, and `kube-proxy`. The `--pod-network-cidr=10.10.0.0/16` specifies the IP range for pod networking, compatible with Calico.

```bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```
- **Explanation**:
  - `mkdir -p $HOME/.kube`: Creates the `.kube` directory in the user’s home directory to store kubectl configuration.
  - `sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config`: Copies the cluster’s admin configuration file to `~/.kube/config` for `kubectl` access, with `-i` prompting before overwrite.
  - `sudo chown $(id -u):$(id -g) $HOME/.kube/config`: Changes ownership of `~/.kube/config` to the current user and group, enabling non-root access to the cluster.

### Step 8: Configure Calico CNI

Install Calico to enable pod-to-pod networking.

```bash
kubectl create -f https://raw.githubusercontent.com/projectcalico/calico/v3.26.1/manifests/tigera-operator.yaml
```
- **Explanation**: Deploys the Tigera Calico operator, which manages Calico resources in the cluster, using a YAML manifest from the Calico repository.

```bash
curl https://raw.githubusercontent.com/projectcalico/calico/v3.26.1/manifests/custom-resources.yaml -O
```
- **Explanation**: Downloads the Calico custom resources YAML file, which defines networking configurations, and saves it as `custom-resources.yaml` in the current directory.

```bash
sed -i 's/cidr: 192\.168\.0\.0\/16/cidr: 10.10.0.0\/16/g' custom-resources.yaml
```
- **Explanation**: Updates the `custom-resources.yaml` file to replace the default CIDR (`192.168.0.0/16`) with the CIDR specified in `kubeadm init` (`10.10.0.0/16`), ensuring compatibility with the cluster’s pod network.

```bash
kubectl create -f custom-resources.yaml
```
- **Explanation**: Applies the modified `custom-resources.yaml` to create Calico resources, enabling the pod network.

```bash
kubectl get pods -n calico-system
kubectl get pods -n tigera-operator
```
- **Explanation**: Checks the status of pods in the `calico-system` and `tigera-operator` namespaces to confirm Calico components (e.g., `calico-node`, `calico-kube-controllers`) are running.

### Step 9: Add Worker Nodes

Join worker nodes to the cluster using the `kubeadm join` command.

```bash
sudo kubeadm join 10.0.0.2:6443 --token <token> --discovery-token-ca-cert-hash sha256:<hash>
```
- **Explanation**: Joins the worker node to the cluster by connecting to the control plane’s API server (`10.0.0.2:6443`). The `--token` authenticates the node, and `--discovery-token-ca-cert-hash` verifies the cluster’s CA certificate. Replace `<token>` and `<hash>` with values from `kubeadm init` or regenerate with:
  ```bash
  kubeadm token create --print-join-command
  ```
  - **Explanation**: Generates a new join command if the original token has expired (valid for 24 hours).

```bash
kubectl get nodes
```
- **Explanation**: Lists all nodes in the cluster, verifying that the worker node has joined and is in a `Ready` state.

### Step 10: Verify Cluster and Test Networking

Confirm the cluster is operational and test pod networking.

```bash
kubectl get pods -A
```
- **Explanation**: Lists all pods across all namespaces (`-A`), ensuring control plane and Calico pods are `Running`.

```bash
kubectl create deployment nginx-app --image=nginx --replicas=2
```
- **Explanation**: Creates a deployment named `nginx-app` with two replicas, using the `nginx` container image, to test workload scheduling.

```bash
kubectl expose deployment nginx-app --port=80 --type=NodePort
```
- **Explanation**: Creates a NodePort service for the `nginx-app` deployment, exposing port 80 and assigning a random port (30000–32767) on each node for external access.

```bash
kubectl get svc
```
- **Explanation**: Lists services, showing the `nginx-app` service’s Cluster IP and NodePort (e.g., `80:30001/TCP`).

```bash
curl <worker-node-ip>:30001
```
- **Explanation**: Tests connectivity to the Nginx service using the worker node’s IP and assigned NodePort, expecting the Nginx welcome page HTML.

```bash
kubectl taint nodes --all node-role.kubernetes.io/control-plane-
kubectl taint nodes --all node-role.kubernetes.io/master-
```
- **Explanation** (optional for single-node clusters): Removes taints from the control plane node, allowing workloads to be scheduled on it. This is only needed for single-node setups.

## Troubleshooting

- **Nodes NotReady**:
  - Check Calico pod logs: `kubectl logs <calico-pod-name> -n calico-system`
  - Verify CIDR in `custom-resources.yaml` matches `kubeadm init`.
  - Confirm `containerd` status: `systemctl status containerd`

- **Join Command Failure**:
  - Regenerate token: `kubeadm token create --print-join-command`
  - Ensure API server (`<master-node-ip>:6443`) is accessible.
  - Check `kubelet` logs: `journalctl -u kubelet`

- **Networking Issues**:
  - Verify Calico pod status and logs.
  - Test pod communication: `kubectl run -it --rm busybox --image=busybox --restart=Never -- sh`

## Resources

- [Kubernetes.io: Installing kubeadm](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/)
- [Kubernetes.io: Creating a cluster with kubeadm](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/)
- [Kubernetes.io: Joining nodes](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/join-nodes/)
- [Kubernetes.io: Cluster Networking](https://kubernetes.io/docs/concepts/cluster-administration/networking/)
- [Project Calico: Getting Started](https://docs.projectcalico.org/getting-started/kubernetes/quickstart)
- [Cherry Servers: Install Kubernetes on Ubuntu](https://www.cherryservers.com/blog/install-kubernetes-on-ubuntu)

This guide provides a comprehensive setup process for a Kubernetes cluster with Calico CNI on Ubuntu, with detailed explanations for each command to ensure clarity and successful deployment.