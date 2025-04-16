# 🚀 Kubernetes Cluster Setup on Rocky Linux 9 with Vagrant, VMware, CRI-O, and Calico

This project automates a local Kubernetes cluster setup using **Vagrant** and **VMware Workstation**. It spins up one control plane (master) node and two worker nodes running **Rocky Linux 9**, with **CRI-O** as the container runtime and **Calico** for networking. 🌐

---

## ✨ Features

- **Vagrant + VMware Workstation**: Reproducible environments with infrastructure as code. 🛠️
- **Rocky Linux 9**: Stable, RHEL-compatible OS for Kubernetes nodes. 🪨
- **CRI-O**: Lightweight, Kubernetes-optimized container runtime. ⚡
- **Calico**: Powerful CNI plugin for pod networking. 🐾
- **Scalable**: Add or remove worker nodes with ease. 📈

---

## 📋 Requirements

- Vagrant (tested with 2.4.x) 🖥️

- VMware Workstation Pro (tested with 17.x) 💻

- Vagrant VMware Plugin:

  ```bash
  vagrant plugin install vagrant-vmware-desktop
  ```

- Minimum 12GB free RAM and 8 CPU cores for VMs 🧠

- Internet connection for package downloads 🌍

---

## 📂 Directory Structure

```
├── Vagrantfile
├── script.sh
├── post_commands.txt
└── README.md
```

- `Vagrantfile`: Defines 1 master + 2 worker nodes and provisions them. 📜
- `script.sh`: Installs CRI-O, Kubernetes tools (kubelet, kubeadm, kubectl), and configures nodes. 🛠️
- `post_commands.txt`: Initializes the cluster and deploys Calico. 🚀

---

## 🛠️ Usage

### 1. Clone the Repository

```bash
git clone <repository-url>
cd <repository-directory>
```

### 2. Spin Up the Cluster

Launch the cluster with:

```bash
vagrant up
```

This creates:

- **Master Node**: `master` (4 CPUs, 8GB RAM, 20GB disk) 🧑‍💼
- **Worker Nodes**: `worker1`, `worker2` (2 CPUs, 2GB RAM, 20GB disk each) 👷

Each node runs **Rocky Linux 9** and is provisioned with CRI-O, kubelet, kubeadm, and kubectl.

---

## ⚙️ Post-Setup (On Master Node)

SSH into the master node:

```bash
vagrant ssh master
```

Run the commands in `post_commands.txt` to initialize the cluster and deploy Calico:

```bash
sudo su
cat /vagrant/post_commands.txt | bash
```

This will:

- Initialize the control plane with `kubeadm init`. 🏁
- Configure `kubectl` for the `vagrant` user. 🔧
- Deploy Calico for pod networking. 🐾

### 🌍 Use `kubectl` from Your Local Machine

To manage the cluster locally with `kubectl`:

1. Copy `admin.conf` from the master node:

```bash
vagrant ssh master -c "sudo cat /etc/kubernetes/admin.conf" > ~/.kube/config
```

2. Verify local access:

```bash
kubectl get nodes
```

---

## 🤝 Join Worker Nodes

1. On the master node, generate a join command:

```bash
kubeadm token create --print-join-command
```

2. Copy the output (e.g., `kubeadm join 192.168.56.10:6443 --token <token> --discovery-token-ca-cert-hash sha256:<hash>`).

3. SSH into each worker node:

```bash
vagrant ssh worker1
vagrant ssh worker2
```

4. Run the join command as root:

```bash
sudo su
<join-command>
```

---

## ✅ Verify Cluster

On the master node or locally (after copying `admin.conf`), check the cluster status:

```bash
kubectl get nodes
kubectl get pods -A
```

Expected output:

- All nodes (`master`, `worker1`, `worker2`) in `Ready` state. ✅
- Calico and Kubernetes pods running in `kube-system` and `calico-system` namespaces. 🐾

---

## 📈 Add More Workers

To add a new worker node:

1. Edit the `Vagrantfile` and add a new entry to the `WORKER_NODES` map:

```ruby
WORKER_NODES = {
  "worker1" => { :ip => "192.168.56.11", :cpus => 2, :mem => 2048 },
  "worker2" => { :ip => "192.168.56.12", :cpus => 2, :mem => 2048 },
  "worker3" => { :ip => "192.168.56.13", :cpus => 2, :mem => 2048 }
}
```

2. Provision the new node:

```bash
vagrant up worker3
```

3. SSH into the new node and run the `kubeadm join` command from the master. 🤝

---

## 🗑️ Clean Up

To tear down the cluster and remove all VMs:

```bash
vagrant destroy -f
```

This deletes all nodes and resources. 🧹

---
