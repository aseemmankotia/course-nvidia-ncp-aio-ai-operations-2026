## Chapter 5: Kubernetes and Run:ai for AI Infrastructure — Practice Questions

**Q1.** A data science team needs to run 12 small training jobs simultaneously on a single A100 GPU to maximize utilization. Each job requires 0.5 GPU and 20GB memory. The cluster uses Run:ai for workload orchestration. Which configuration BEST enables this requirement?

A) Enable MIG with 7x 1g.10gb profile on the A100 and assign one MIG slice per job
B) Deploy NVIDIA device plugin with time-slicing configuration and set replicas=12
C) Create 12 separate Run:ai projects each with 0.08 GPU quota (1/12 of A100)
D) Enable Run:ai fractional GPU allocation with 0.5 GPU quota per job and bin-packing scheduler

<details>
<summary>Answer</summary>

**Correct: D**

Run:ai fractional GPU allocation allows multiple jobs to share a single GPU by allocating portions (0.5 GPU) and using bin-packing to maximize utilization. This is the designed use case for Run:ai's core feature.

**Why the others are wrong:**
- MIG 7x 1g.10gb gives only 7 slices maximum, not 12. Also, each job needs 0.5 GPU (50% of resources) which doesn't map to 1g.10gb profile (1/7th = 14% of GPU).
- Time-slicing is a device plugin feature that enables time-sharing but doesn't provide the memory isolation and quota enforcement that Run:ai fractional GPU provides. Also not the primary Run:ai feature being tested.
- Creating 12 separate projects is administrative overhead and doesn't leverage Run:ai's bin-packing scheduler. Projects are for team isolation, not individual job quotas.

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q2.** An administrator is configuring GPU monitoring for a Kubernetes cluster. After installing GPU Operator, which component exposes GPU metrics in Prometheus format for monitoring GPU utilization, memory, temperature, and power consumption?

A) nvidia-device-plugin
B) nvidia-container-toolkit
C) nvidia-dcgm-exporter
D) gpu-feature-discovery

<details>
<summary>Answer</summary>

**Correct: C**

nvidia-dcgm-exporter is the GPU Operator component that collects GPU telemetry via DCGM (Data Center GPU Manager) and exposes metrics in Prometheus format for monitoring stacks.

**Why the others are wrong:**
- nvidia-device-plugin advertises GPU resources to kubelet and schedules pods to GPU nodes. It does not export metrics.
- nvidia-container-toolkit enables GPU access in containers by modifying container runtime. It does not collect or export metrics.
- gpu-feature-discovery labels nodes with GPU properties (product name, CUDA version, etc.) but does not export runtime metrics like utilization or temperature.

</details>

---

**Q3.** A company runs distributed training jobs on an 8-GPU node. Jobs must start only when all 8 GPUs are available simultaneously to avoid deadlock. Which Run:ai scheduling policy should be configured?

A) Gang scheduling with min_replicas=8 to ensure atomic allocation
B) Fairness scheduling to distribute GPUs equally across jobs
C) Bin-packing to maximize GPU utilization density
D) Over-quota scheduling to allow bursting beyond project limits

<details>
<summary>Answer</summary>

**Correct: A**

Gang scheduling ensures all replicas (GPUs) required by a distributed training job are allocated atomically. If 8 GPUs aren't available, the job waits. This prevents partial allocation and deadlock in distributed training.

**Why the others are wrong:**
- Fairness scheduling distributes resources fairly over time but doesn't guarantee atomic allocation. A job could get 4 GPUs now and wait indefinitely for the remaining 4.
- Bin-packing maximizes node utilization but doesn't address atomic allocation requirement. It could place 8 GPUs across multiple nodes or partially allocate.
- Over-quota allows exceeding project quotas when cluster has spare capacity. It doesn't solve the atomic allocation problem for distributed training.

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q4.** An administrator needs to enable MIG on an A100 GPU in a Kubernetes cluster and expose each MIG instance as a separate schedulable resource. After configuring MIG profiles on the GPU, what is the REQUIRED nvidia-device-plugin configuration?

A) Set MIG strategy to 'mixed' to expose both full GPU and MIG instances
B) Enable time-slicing mode with MIG profile replicas in ConfigMap
C) Set MIG strategy to 'single' and label nodes with nvidia.com/mig.config
D) Deploy separate device plugin DaemonSet for each MIG geometry

<details>
<summary>Answer</summary>

**Correct: C**

MIG strategy 'single' exposes each MIG instance as a separate resource. Nodes must be labeled with MIG configuration (nvidia.com/mig.config) so the device plugin discovers and advertises MIG instances to Kubernetes scheduler.

**Why the others are wrong:**
- 'mixed' strategy is not a valid nvidia-device-plugin MIG strategy. The valid strategies are 'none' (default) and 'single'.
- Time-slicing is a separate feature from MIG. Time-slicing shares GPUs via temporal multiplexing, while MIG provides hardware-level partitioning. They are not combined in this way.
- Only one device plugin DaemonSet is needed. The single instance detects MIG configuration from node labels and advertises resources like nvidia.com/mig-1g.10gb, nvidia.com/mig-2g.20gb, etc.

</details>

---

**Q5.** A team has a Run:ai project with GPU quota of 4.0 and over-quota limit of 8.0. Currently, the team has 4 jobs using 4.0 GPUs total. They submit a 5th job requesting 2.0 GPUs. The cluster has 10 idle GPUs. What happens?

A) Job is rejected because quota is fully consumed at 4.0 GPUs
B) Job is queued and starts only after other jobs release GPU quota
C) Job starts immediately using 2.0 GPUs from the over-quota allowance
D) Job starts with 1.0 GPU (half requested) due to fractional GPU bin-packing

<details>
<summary>Answer</summary>

**Correct: C**

Over-quota allows projects to use more than their guaranteed quota when cluster has idle capacity. Current usage is 4.0 (at quota), over-quota limit is 8.0, so the project can consume 4.0 more. The 2.0 GPU job starts using over-quota allocation.

**Why the others are wrong:**
- Jobs are not rejected at quota limit if over-quota is configured and cluster has capacity. Over-quota exists specifically to allow bursting.
- Jobs don't need to wait at quota limit if over-quota headroom exists. The project has 4.0 GPUs of over-quota headroom remaining (8.0 limit - 4.0 current).
- Fractional GPU doesn't automatically reduce job requests. A job requesting 2.0 GPUs will get 2.0 GPUs (or wait). The fractional feature allows submitting jobs with requests like 0.5 GPU, not automatic resizing.

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q6.** Which command creates a Kubernetes namespace called 'ml-team-a' with a hard resource quota limiting the namespace to consuming a maximum of 8 NVIDIA GPUs?

A) kubectl create namespace ml-team-a --quota=nvidia.com/gpu=8
B) kubectl apply -f namespace.yaml with resourceQuota: nvidia.com/gpu: 8
C) kubectl create namespace ml-team-a && kubectl create quota gpu-limit --hard=nvidia.com/gpu=8 -n ml-team-a
D) runai create project ml-team-a --gpu-quota 8

<details>
<summary>Answer</summary>

**Correct: C**

Creating a namespace and ResourceQuota are two separate operations. First create the namespace, then create a ResourceQuota object with --hard=nvidia.com/gpu=8 in that namespace. This is the correct kubectl syntax.

**Why the others are wrong:**
- kubectl create namespace does not support a --quota flag. Namespace creation and quota creation are separate API objects.
- This describes applying a YAML file but the syntax shown is invalid. ResourceQuota is a separate object, not a field under namespace. Also, 'kubectl apply -f' requires a file path, not inline YAML shown this way.
- This is Run:ai project creation, not Kubernetes namespace/quota creation. While Run:ai projects create namespaces underneath, the question asks specifically for the Kubernetes command.

</details>

---

**Q7.** A production cluster has nodes with A100 GPUs configured with MIG profile 3g.20gb (3 instances per GPU). The nvidia-device-plugin is installed with MIG strategy 'single'. A pod requests 'nvidia.com/gpu: 1'. What happens?

A) Pod is scheduled to one full A100 GPU, ignoring MIG configuration
B) Pod fails to schedule because nvidia.com/gpu resource is not exposed when MIG is enabled
C) Pod is scheduled to one 3g.20gb MIG instance, consuming 1/3 of an A100
D) Pod is scheduled to three 3g.20gb MIG instances spanning the full A100

<details>
<summary>Answer</summary>

**Correct: B**

When nvidia-device-plugin uses MIG strategy 'single', it exposes MIG instances as resources like nvidia.com/mig-3g.20gb, NOT nvidia.com/gpu. Pods must request the specific MIG resource type. Requesting nvidia.com/gpu will fail to schedule because that resource is not advertised.

**Why the others are wrong:**
- MIG strategy 'single' does not expose nvidia.com/gpu. It only exposes MIG-specific resources. The pod cannot be scheduled to a full GPU using this request.
- Pods requesting nvidia.com/mig-3g.20gb would get one MIG instance, but this pod requests nvidia.com/gpu which is not exposed. The pod remains pending.
- The scheduler cannot map nvidia.com/gpu to three MIG instances. Each MIG instance is a separate schedulable resource. A pod requesting one resource gets one MIG instance (if it requested the correct resource type).

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q8.** An administrator installs the Run:ai control plane and wants to submit jobs from the command line. What is the correct sequence of steps?

A) Install kubectl → create Run:ai CRDs → deploy Run:ai operator → submit jobs with kubectl apply
B) Install runai CLI → deploy Run:ai scheduler to cluster → create kubeconfig → submit jobs with runai exec
C) Install runai CLI → authenticate with runai login → configure cluster context → submit jobs with runai submit
D) Deploy Run:ai control plane → install nvidia-device-plugin → configure MIG → submit jobs via Web UI only

<details>
<summary>Answer</summary>

**Correct: C**

The correct Run:ai CLI workflow is: install the runai CLI binary, authenticate to the control plane with 'runai login', configure cluster context (or it auto-detects from kubeconfig), then submit jobs with 'runai submit' command.

**Why the others are wrong:**
- While Run:ai uses CRDs and operators underneath, users do not manually create CRDs or deploy operators when the control plane is already installed. The runai CLI is the primary interface, not kubectl apply for Run:ai jobs.
- 'runai exec' is for executing commands in running jobs, not submitting new jobs. The scheduler is deployed as part of control plane installation, not a separate manual step after CLI install.
- Run:ai provides both CLI and Web UI. The CLI is a primary interface for job submission, not 'Web UI only'. Also, nvidia-device-plugin and MIG configuration are cluster setup steps, not part of the Run:ai CLI job submission flow.

</details>

---

