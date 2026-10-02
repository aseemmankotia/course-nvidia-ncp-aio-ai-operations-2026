## Chapter 7: Resource Allocation and NGC Container Management — Practice Questions

**Q1.** A research team needs guaranteed access to 8 GPUs for production training jobs, but should be able to use up to 16 GPUs when the cluster is idle. Which Run:ai project configuration BEST meets these requirements?

A) Set GPU quota to 16 and enable over-quota to false
B) Set GPU quota to 8, enable over-quota to true, and set node affinity for 16 nodes
C) Set guaranteed quota to 8 GPUs and over-quota limit to 16 GPUs
D) Create two projects: one with 8 GPUs guaranteed and another with 8 GPUs best-effort

<details>
<summary>Answer</summary>

**Correct: C**

Run:ai projects support guaranteed quota (resources always available) and over-quota limit (maximum resources when cluster has capacity). Setting guaranteed to 8 ensures production access, over-quota to 16 allows burst usage.

**Why the others are wrong:**
- Setting quota to 16 without over-quota would guarantee 16 GPUs always, wasting resources when team doesn't need them
- Node affinity controls placement, not resource limits. Over-quota is a limit setting, not a boolean flag
- Two projects adds management overhead and doesn't provide seamless bursting - user would need to submit to different projects

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q2.** An administrator needs to ensure that namespace 'ml-team-a' cannot exceed 32 GB GPU memory total across all pods. Which Kubernetes object should be created?

A) LimitRange with container GPU memory limit of 32Gi
B) ResourceQuota with nvidia.com/gpu: 4 assuming 8GB GPUs
C) ResourceQuota with requests.nvidia.com/gpu-memory: 32Gi
D) PodSecurityPolicy with GPU memory constraints

<details>
<summary>Answer</summary>

**Correct: B**

ResourceQuota enforces aggregate resource limits per namespace. GPU memory is controlled by limiting total GPU count. With 8GB GPUs, quota of 4 GPUs = 32GB total. GPU memory is not a separate resource quota type in standard Kubernetes.

**Why the others are wrong:**
- LimitRange sets default/max limits per pod/container, not total namespace limits. Would allow unlimited pods each with 32GB
- requests.nvidia.com/gpu-memory is not a valid Kubernetes resource quota field - GPUs are counted as discrete devices
- PodSecurityPolicy controls security context, not resource allocation, and is deprecated in Kubernetes 1.25+

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q3.** A data scientist needs to pull the latest NVIDIA PyTorch container with CUDA 12.2 support. After authenticating with 'docker login nvcr.io', which command should they run?

A) docker pull nvcr.io/nvidia/pytorch:latest
B) docker pull nvcr.io/nvidia/pytorch:24.01-py3
C) docker pull pytorch/pytorch:cuda12.2
D) docker pull nvcr.io/pytorch:cuda12.2-cudnn8

<details>
<summary>Answer</summary>

**Correct: B**

NGC containers use versioned tags (YY.MM format like 24.01) with Python version suffix. Tag 'latest' is not recommended for reproducibility. The nvcr.io/nvidia/pytorch repository contains official NVIDIA-optimized images. Version 24.01 includes CUDA 12.2.

**Why the others are wrong:**
- Using 'latest' tag is not recommended - non-reproducible and exam emphasizes version pinning for production workloads
- pytorch/pytorch is Docker Hub repository, not NGC. NGC images have NVIDIA optimizations (DALI, Apex, etc.) that Docker Hub versions lack
- Incorrect registry path - missing 'nvidia' organization. NGC format is nvcr.io/nvidia/<framework>

</details>

---

**Q4.** A Slurm cluster has three research groups. Group A should get 50% priority share, Group B 30%, and Group C 20%. Which configuration implements fair-share scheduling correctly?

A) Set FairShareWeight=50,30,20 in slurm.conf and enable PriorityType=priority/multifactor
B) Set PriorityWeight=50,30,20 per account and enable FairShareDampeningFactor=1
C) Configure partition weights with Weight=50,30,20 and set PriorityFlags=FAIR_TREE
D) Create accounts A, B, C with Shares=50,30,20 in sacctmgr and set PriorityType=priority/multifactor

<details>
<summary>Answer</summary>

**Correct: D**

Fair-share is configured via sacctmgr by creating accounts with Shares values. PriorityType=priority/multifactor must be enabled in slurm.conf to use fair-share. Shares are relative weights (50:30:20 ratio).

**Why the others are wrong:**
- FairShareWeight is not a valid slurm.conf parameter - fair-share configuration is done via sacctmgr database, not config file
- PriorityWeight affects how factors combine in multifactor priority, not fair-share allocation. FairShareDampeningFactor is a tuning parameter, not the primary config
- Partitions have Weight for backfill priority, not fair-share. FAIR_TREE is a flag for hierarchical fair-share calculation, but Shares must still be set via sacctmgr

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q5.** GPU utilization shows 0% in nvidia-smi but the training job reports it is running. What is the MOST likely cause?

A) The GPU is in P8 power state and needs to be set to P0 performance mode
B) DCGM is blocking GPU access and needs to be restarted
C) The GPU is in exclusive process mode and the second process cannot access it
D) The application is using CPU-only path or GPU kernels are not launching due to a CUDA error

<details>
<summary>Answer</summary>

**Correct: D**

0% utilization with job running indicates compute kernels are not executing on GPU. Common causes: code defaulting to CPU, CUDA OOM preventing kernel launch, misconfigured device IDs. Check application logs for CUDA errors.

**Why the others are wrong:**
- P8 is idle power state, but even in P8 nvidia-smi would show utilization when kernels run - GPU auto-boosts to P0. P-state is result of low utilization, not cause
- DCGM is a monitoring tool that runs alongside nvidia-smi - it does not block GPU access or affect workload execution
- Exclusive mode would prevent process from starting (process creation fails), not allow it to run with 0% utilization. You'd see 'device busy' error on launch

</details>

---

**Q6.** An NGC catalog search needs to find all containers optimized for LLM training that support multi-node scaling. Which NGC catalog filter combination is MOST appropriate?

A) Category: Training, Search: 'transformer', Filter: Distributed training
B) Category: Inference, Tag: Triton, Filter: Model parallelism
C) Category: HPC, Tag: MPI, Search: 'language model'
D) Category: AI/ML Frameworks, Tag: NeMo, Filter: Multi-GPU support

<details>
<summary>Answer</summary>

**Correct: D**

NeMo framework containers are specifically optimized for LLM training with built-in multi-node support via Megatron-LM. AI/ML Frameworks category contains training-optimized containers. NeMo supports data and model parallelism for scaling.

**Why the others are wrong:**
- NGC doesn't have a top-level 'Training' category - frameworks are under AI/ML Frameworks. 'Distributed training' is a feature tag but less specific than NeMo
- Triton is for inference serving, not training. While inference can use model parallelism, question asks for training workloads
- HPC category contains simulation/modeling containers, not LLM training. MPI is low-level communication, not a framework - NeMo/Megatron use MPI internally but framework containers are better match

</details>

---

**Q7.** After running 'dcgmi diag -r 3', the output shows 'Fail' for GPU 2 memory test. What should be the NEXT troubleshooting step?

A) Run nvidia-smi -r to reset the GPU and re-run the diagnostic
B) Increase GPU power limit with nvidia-smi -pl 350 and retry the test
C) Run 'dcgmi health -g 2' to check if GPU is still responsive before filing support ticket
D) Check 'dcgmi diag -r 3 -i 2' output for specific error codes and GPU serial number to file RMA

<details>
<summary>Answer</summary>

**Correct: D**

Memory test failure indicates hardware issue. Next step: isolate GPU with -i flag, capture specific error codes and serial number for RMA. Level 3 diagnostic is comprehensive - failure indicates hardware replacement needed.

**Why the others are wrong:**
- nvidia-smi -r resets GPU but doesn't fix hardware memory errors. Memory test failures are not transient - reset won't help and wastes time
- Power limit affects performance/throttling, not memory errors. Memory test failure is hardware defect, not power-related issue
- dcgmi health checks basic responsiveness. If diag level 3 failed, health will likely fail too. Need error details and serial number, not another test

</details>

---

**Q8.** A Run:ai administrator needs to ensure that when multiple teams submit jobs simultaneously, Team Alpha's jobs always start before Team Beta's jobs if resources are limited. Both teams have guaranteed quotas. Which configuration achieves this?

A) Set Team Alpha project priority weight to 100 and Team Beta to 50
B) Set Team Alpha guaranteed quota higher than Team Beta
C) Enable preemption on Team Beta project with Team Alpha as higher priority
D) Set Team Alpha node affinity to exclusive and Team Beta to shared

<details>
<summary>Answer</summary>

**Correct: A**

Run:ai project priority weight determines scheduling order when multiple projects compete for resources above guaranteed quota. Higher weight = higher priority. Weight 100 vs 50 ensures Team Alpha jobs schedule first.

**Why the others are wrong:**
- Guaranteed quota ensures minimum resources, doesn't affect scheduling priority when quotas are met. Both teams get their guaranteed amount regardless of quota size
- Preemption allows evicting running jobs, which is more aggressive than question requires. Question asks for scheduling order, not job eviction
- Node affinity controls placement on specific nodes, not scheduling priority between teams. Doesn't ensure Team Alpha schedules first

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

