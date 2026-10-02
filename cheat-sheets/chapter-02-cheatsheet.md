# Chapter 2: Workload Manager Integration Cheat Sheet

## Slurm on BCM: Key Configuration Files

| File | Location | Purpose | Read By |
|------|----------|---------|----------|
| slurm.conf | /etc/slurm/ | Cluster config, node definitions | Controller & compute nodes |
| gres.conf | /etc/slurm/ | GPU resource definitions | Controller (slurmctld) |
| slurmdbd.conf | /etc/slurm/ | Accounting DB config | slurmdbd daemon |

## Slurm Node Definition (slurm.conf)
```
NodeName=gpu-node[01-04] CPUs=128 Gres=gpu:a100:8 RealMemory=512000 State=UNKNOWN
PartitionName=gpu Nodes=gpu-node[01-04] Default=YES MaxTime=INFINITE State=UP
```
**Required Parameters:**
- NodeName: hostname or pattern
- CPUs: total CPU count
- Gres: gpu:TYPE:COUNT (Type required for multi-GPU-type clusters)
- RealMemory: RAM in MB
- State=UNKNOWN: initial state (auto-transitions when slurmd registers)

## GPU Resource Definition (gres.conf)
```
NodeName=gpu-node[01-04]
Name=gpu Type=a100 File=/dev/nvidia[0-7]
```
**Syntax Rules:**
- Name=gpu (fixed, must be 'gpu')
- Type=<model> (a100, v100, etc. — enables --gres=gpu:a100:2)
- File=/dev/nvidia[X-Y] (device files, determines count)
- Count parameter does NOT exist (count = number of devices in File=)

## BCM Slurm Integration
**Database Connection (BCM config):**
- Database type: MySQL/MariaDB
- Target: slurmdbd accounting database
- Required info: host, port (default 3306), database name, user, password
- Result: Slurm job stats appear in BCM Base View

**No plugin installation needed** — BCM reads directly from slurmdbd database.

## Kubernetes on BCM: Installation Steps

### 1. Initialize Control Plane
```bash
sudo kubeadm init --pod-network-cidr=10.244.0.0/16
mkdir -p $HOME/.kube
sudo cp /etc/kubernetes/admin.conf $HOME/.kube/config
```
**Common Error:** "port 6443 already in use"
- Cause: Previous K8s installation
- Fix: `sudo kubeadm reset` on all nodes, then reinit

### 2. Join Worker Nodes
```bash
# On control plane, get join command:
kubeadm token create --print-join-command

# On each worker:
sudo kubeadm join <IP>:6443 --token <token> --discovery-token-ca-cert-hash sha256:<hash>
```

### 3. Deploy NVIDIA GPU Operator
```bash
helm repo add nvidia https://helm.ngc.nvidia.com/nvidia
helm repo update
helm install --wait --generate-name \
  -n gpu-operator --create-namespace \
  nvidia/gpu-operator
```
**What GPU Operator Installs:**
- Device plugin (exposes nvidia.com/gpu resource)
- NVIDIA Container Toolkit (replaces deprecated nvidia-docker2)
- GPU drivers (if not present)
- DCGM exporter (GPU metrics)
- Node-feature-discovery (auto-labels nodes)

**Verify Device Plugin:**
```bash
kubectl get nodes -o json | jq '.items[].status.capacity'
# Look for: "nvidia.com/gpu": "8"
```

## Run:ai Installation Sequence

**Prerequisites:** Kubernetes cluster + GPU Operator installed

**Steps:**
1. Install control plane:
```bash
helm repo add runai https://runai.jfrog.io/artifactory/cp-charts-prod
helm install runai-cluster runai/runai-cluster \
  -n runai --create-namespace
```

2. Register cluster in Run:ai SaaS:
   - Log into Run:ai UI
   - Create new cluster
   - Copy generated cluster installation YAML

3. Apply cluster config:
```bash
kubectl apply -f runai-cluster-config.yaml
```

## Common Exam Traps

| Trap | Wrong Answer | Correct Answer |
|------|--------------|----------------|
| Slurm GPU config | Count=8 in gres.conf | File=/dev/nvidia[0-7] (no Count param) |
| BCM Slurm integration | Install plugin on compute nodes | Configure DB connection to slurmdbd |
| K8s GPU scheduling | Manual nvidia-docker2 install | Deploy GPU Operator via Helm |
| Slurm node State | State=IDLE in initial config | State=UNKNOWN (auto-transitions) |
| gres.conf location | On all compute nodes | On controller only |
| K8s port 6443 error | Firewall blocking | Previous install exists, run kubeadm reset |

## Quick Reference: File Locations
```
/etc/slurm/slurm.conf       # Cluster config (all nodes)
/etc/slurm/gres.conf        # GPU resources (controller)
/etc/slurm/slurmdbd.conf    # Accounting DB (controller)
/etc/kubernetes/admin.conf  # K8s admin kubeconfig
$HOME/.kube/config          # User kubeconfig
/dev/nvidia[0-N]            # GPU device files
```

## Slurm Job Submission with GPUs
```bash
# Request 2 A100 GPUs:
srun --gres=gpu:a100:2 nvidia-smi

# Submit batch job:
sbatch --gres=gpu:4 job_script.sh
```

**Pending Job Reasons:**
- (Resources): Not enough free GPUs/nodes
- (ReqNodeNotAvail): Requested node down/unavailable
- "no gres.conf file": Missing /etc/slurm/gres.conf on controller
