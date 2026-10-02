## Chapter 2: Workload Manager Integration: Slurm and Kubernetes on BCM — Practice Questions

**Q1.** A data science team needs to deploy a Slurm cluster on BCM-managed nodes with 8x A100 GPUs per node. The cluster must allow jobs to request specific GPU types and counts. Which configuration in gres.conf is CORRECT?

A) Name=gpu Type=a100 File=/dev/nvidia[0-7]
B) Name=gpu Type=a100 Count=8
C) Name=gpu Type=nvidia Count=8 File=/dev/nvidia0
D) Name=a100 Type=gpu File=/dev/nvidia[0-7] Count=8

<details>
<summary>Answer</summary>

**Correct: A**

Slurm gres.conf requires Name=gpu (fixed name), Type to specify GPU model (a100), and File=/dev/nvidia[0-7] to map the 8 GPU device files. This allows jobs to use --gres=gpu:a100:2 syntax.

**Why the others are wrong:**
- Count parameter does not exist in gres.conf — GPU count is determined by the number of device files in the File= parameter
- File=/dev/nvidia0 only exposes one GPU instead of all 8, and Count is invalid syntax
- Name must be 'gpu' not 'a100' — Type is for GPU model differentiation, and Count is not a valid parameter

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q2.** An organization is configuring BCM to display Slurm job statistics in Base View. The Slurm controller uses MySQL for accounting. Which BCM configuration step is REQUIRED to integrate the accounting database?

A) Install the BCM Slurm plugin on all compute nodes and restart slurmd
B) Export Slurm accounting data to CSV hourly and upload to BCM via REST API
C) Install the BCM collector daemon on the Slurm controller node only
D) Configure BCM with the MySQL connection string to the slurmdbd database in the integrations section

<details>
<summary>Answer</summary>

**Correct: D**

BCM reads Slurm job data directly from the slurmdbd accounting database by connecting to MySQL/MariaDB. The connection string (host, port, database name, credentials) must be configured in BCM's workload manager integration settings.

**Why the others are wrong:**
- BCM does not require a plugin on compute nodes — it reads from the centralized accounting database maintained by slurmdbd
- BCM integrates natively with the slurmdbd database — manual CSV export is not the integration method and defeats real-time monitoring
- The collector daemon is not Slurm-specific and installing only on controller misses compute node metrics — database integration is the correct approach

</details>

---

**Q3.** A Kubernetes cluster on NVIDIA DGX nodes needs GPU workload scheduling. The cluster was initialized with BCM, and the control plane is running. What is the NEXT step to enable GPU pod scheduling?

A) Label all worker nodes with nvidia.com/gpu=true using kubectl
B) Deploy the NVIDIA GPU Operator via Helm to install device plugin and monitoring stack
C) Manually install nvidia-docker2 on each worker node and restart kubelet
D) Configure the Kubernetes scheduler to enable GPU-aware scheduling in kube-scheduler.yaml

<details>
<summary>Answer</summary>

**Correct: B**

The NVIDIA GPU Operator automates deployment of all GPU components: device plugin (for GPU resource exposure), container runtime, drivers, monitoring (dcgm-exporter), and feature discovery. It's the standard production deployment method.

**Why the others are wrong:**
- Node labels are automatically applied by the GPU Operator's node-feature-discovery component — manual labeling is not required and doesn't enable GPU scheduling
- nvidia-docker2 is deprecated — the GPU Operator uses the NVIDIA Container Toolkit and manages installation automatically across all nodes
- The default kube-scheduler handles GPU scheduling once the device plugin is running — no scheduler config changes needed, and the device plugin comes from GPU Operator

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q4.** During Slurm installation on BCM, the slurm.conf file must define compute node roles. A cluster has 1 controller node (gpu-ctrl01) and 4 compute nodes (gpu-node[01-04]) with 8 GPUs each. Which slurm.conf NodeName entry is CORRECT?

A) NodeName=gpu-node[01-04] Gres=gpu:8 State=UNKNOWN
B) NodeName=gpu-node[01-04] Sockets=2 CoresPerSocket=64 Gres=gpu:8 State=IDLE
C) NodeName=gpu-node[01-04] CPUs=128 Gres=gpu:a100:8 RealMemory=512000 State=UNKNOWN
D) NodeName=DEFAULT Gres=gpu:8 RealMemory=512000 State=UNKNOWN

<details>
<summary>Answer</summary>

**Correct: C**

NodeName entry requires CPUs (total CPU count), Gres with Type (gpu:a100:8 specifies 8 A100 GPUs), RealMemory (RAM in MB), and State=UNKNOWN (initial state before node registration). This matches exam requirements for complete node definition.

**Why the others are wrong:**
- Missing CPUs and RealMemory parameters which are required — Gres=gpu:8 lacks GPU type specification needed when multiple GPU types exist
- Sockets and CoresPerSocket are optional if CPUs is provided — State=IDLE is incorrect for initial config (nodes start UNKNOWN until slurmd registers)
- NodeName=DEFAULT is for setting defaults across multiple NodeName entries — cannot be used alone, and doesn't specify which nodes are compute nodes

</details>

---

**Q5.** A company needs to deploy Run:ai on a BCM-managed Kubernetes cluster. The GPU Operator is already installed. Which sequence of steps is CORRECT to complete Run:ai installation?

A) Install Run:ai CLI → Create cluster in Run:ai SaaS UI → Run 'runai install' → Register cluster with generated token
B) Add Run:ai Helm repo → Install runai-operator chart → Create cluster in Run:ai SaaS → Copy cluster YAML and apply with kubectl
C) Install Run:ai control plane via Helm → Register cluster in Run:ai SaaS UI → Apply the generated cluster installation YAML
D) Deploy Run:ai agent DaemonSet → Install Run:ai scheduler → Register with Run:ai cloud using runai-adm register

<details>
<summary>Answer</summary>

**Correct: C**

Run:ai installation: (1) Install the control plane components via Helm (runai-cluster chart), (2) Register the cluster in Run:ai SaaS to get cluster credentials, (3) Apply the generated YAML containing cluster-specific config. This is the official documented sequence.

**Why the others are wrong:**
- Run:ai installation is Helm-based, not via 'runai install' CLI command — cluster creation in SaaS happens after Helm install, not before
- Sequence is reversed — control plane must be installed before cluster registration, and the generated YAML is applied via Helm values, not as separate kubectl apply
- Run:ai uses Helm charts, not manual DaemonSet deployment — 'runai-adm register' is not the registration method (uses cluster creation in SaaS UI)

</details>

---

**Q6.** An administrator is initializing a Kubernetes cluster on NVIDIA DGX servers using BCM. The control plane initialization command fails with 'port 6443 already in use'. What is the MOST likely cause and solution?

A) The BCM management service uses port 6443 — reconfigure BCM to use port 8443 in bcm.conf
B) Another Kubernetes cluster is running — run 'kubeadm reset' on all nodes before reinitializing
C) Docker is not installed — install docker-ce and restart the initialization
D) The firewall is blocking port 6443 — open the port with 'firewall-cmd --add-port=6443/tcp' and retry

<details>
<summary>Answer</summary>

**Correct: B**

Port 6443 is the default kube-apiserver port. If 'already in use', a previous Kubernetes installation exists. 'kubeadm reset' cleans up all Kubernetes components and releases the port. This is the standard recovery procedure.

**Why the others are wrong:**
- BCM does not use port 6443 — that port is specifically for Kubernetes API server, and BCM runs on different ports (typically 8000-8080 range)
- Kubernetes 1.24+ uses containerd not Docker — docker-ce is not required, and missing container runtime would show different error
- Firewall blocking would show 'connection refused' not 'port in use' — port in use means a process is already listening on that port

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q7.** A BCM environment has Slurm installed with GPU nodes. Users report that 'srun --gres=gpu:1' jobs are pending with reason '(Resources)'. Slurm logs show 'gres/gpu: no gres.conf file'. Which file is missing and where should it be located?

A) gres.conf missing on compute nodes in /etc/slurm/gres.conf — copy from controller and restart slurmd
B) gres.conf missing on controller node in /etc/slurm/gres.conf — create file with GPU definitions and restart slurmctld
C) gpu.conf missing on all nodes in /etc/slurm/gpu.conf — create file and restart slurm services
D) topology.conf missing on controller in /etc/slurm/topology.conf — define GPU topology and restart slurmctld

<details>
<summary>Answer</summary>

**Correct: B**

gres.conf must exist on the Slurm controller node at /etc/slurm/gres.conf and define GPU resources (Name=gpu Type=... File=...). The controller reads this file when starting slurmctld. Compute nodes reference the gres definitions from the controller.

**Why the others are wrong:**
- gres.conf is read by the controller, not compute nodes — compute nodes need slurm.conf with Gres= parameter, but gres.conf itself lives on controller
- File is named gres.conf not gpu.conf — gpu.conf is not a Slurm configuration file
- topology.conf is for network topology (switches, nodes), not GPU definitions — gres.conf is the correct file for GPU resource configuration

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q8.** After deploying the NVIDIA GPU Operator on Kubernetes, an administrator needs to verify that GPU pods can be scheduled. Which command output confirms the device plugin is running correctly?

A) kubectl get pods -n gpu-operator shows all pods in Running state
B) nvidia-smi on worker nodes shows GPU utilization for running pods
C) kubectl get nodes -o json | jq '.items[].status.capacity' shows 'nvidia.com/gpu: "8"' for GPU nodes
D) kubectl describe node gpu-worker01 shows 'gpu-operator=enabled' label

<details>
<summary>Answer</summary>

**Correct: C**

The device plugin advertises GPU resources to the Kubernetes scheduler by adding 'nvidia.com/gpu' to node capacity. Checking node capacity for this resource confirms the device plugin successfully detected GPUs and registered them with the cluster.

**Why the others are wrong:**
- GPU Operator pods running does not guarantee the device plugin registered GPUs — the device plugin pod could be running but failing to detect GPUs
- nvidia-smi showing utilization proves GPUs work on the host but doesn't confirm Kubernetes can schedule GPU pods — need to verify kubernetes resource registration
- The label 'gpu-operator=enabled' is not automatically added — node labels like 'nvidia.com/gpu.present=true' are added by node-feature-discovery, but capacity check is definitive

</details>

---

