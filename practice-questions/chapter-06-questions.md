## Chapter 6: Multi-Tenant Workload Deployment: Inference and Training — Practice Questions

**Q1.** A data science team needs to deploy a Llama 3.1 70B model for inference on Kubernetes. They require automatic scaling based on GPU utilization and want to use NVIDIA's optimized containers. Which deployment approach BEST meets these requirements?

A) Deploy a custom TensorFlow Serving container and configure horizontal pod autoscaling based on CPU metrics
B) Deploy Triton Inference Server with the model in ONNX format and configure cluster autoscaling only
C) Deploy NVIDIA NIM microservice from NGC with GPU resource requests and HPA configured for nvidia.com/gpu metric
D) Deploy the model using KServe with CPU-only pods and enable request-based autoscaling

<details>
<summary>Answer</summary>

**Correct: C**

NVIDIA NIM (NVIDIA Inference Microservices) are pre-built, optimized containers specifically designed for deploying LLMs on Kubernetes with GPU support. Setting GPU resource requests (nvidia.com/gpu) in the pod spec enables proper GPU allocation, and HPA can scale based on GPU utilization metrics exposed by NVIDIA DCGM.

**Why the others are wrong:**
- TensorFlow Serving is not optimized for Llama models (PyTorch-based) and CPU metrics don't reflect GPU utilization for inference scaling
- Triton can serve the model but ONNX conversion for 70B models is complex and may lose optimizations; cluster autoscaling alone doesn't handle pod-level scaling
- CPU-only deployment cannot efficiently serve a 70B parameter model which requires GPU acceleration; KServe adds unnecessary complexity when NIM provides ready-to-use solution

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q2.** An ML engineer configures Triton Inference Server to serve three models simultaneously. The models have varying batch sizes: Model A (batch 1-8), Model B (batch 1-16), Model C (batch 1-32). Which Triton configuration setting ensures optimal throughput while preventing memory overflow?

A) Configure dynamic_batching with max_queue_delay_microseconds and model-specific max_batch_size in each model's config.pbtxt
B) Set max_batch_size to 32 globally and enable preferred_batch_size delays for all models
C) Use sequence_batching instead of dynamic_batching to control batch formation per model
D) Set instance_group count to 3 with KIND_GPU and let Triton automatically manage batching

<details>
<summary>Answer</summary>

**Correct: A**

Dynamic batching with max_queue_delay_microseconds allows Triton to accumulate requests up to each model's specific max_batch_size before inference, optimizing GPU utilization. Each model's config.pbtxt must specify its own max_batch_size to prevent memory overflow while maximizing throughput through controlled batch formation.

**Why the others are wrong:**
- Global max_batch_size would force smaller models to wait for batches they cannot handle, and preferred_batch_size doesn't prevent the 70B model from exceeding memory with batch 32
- Sequence batching is for stateful models (like RNNs) where requests must maintain order; it doesn't solve the batch size optimization problem for independent inference requests
- Instance groups control model parallelism, not batch formation; Triton won't automatically determine safe batch sizes without explicit configuration in config.pbtxt

</details>

---

**Q3.** A researcher needs to run a distributed training job on 4 nodes, with 8 A100 GPUs per node, using PyTorch DDP and NCCL. Which Slurm sbatch script configuration correctly allocates resources for this multi-node training job?

A) --nodes=4 --gres=gpu:8 --ntasks-per-node=8 --cpus-per-task=8
B) --nodes=4 --gres=gpu:8 --ntasks=4 --cpus-per-task=16
C) --nodes=4 --gres=gpu:32 --ntasks=32 --cpus-per-task=4
D) --nodes=4 --gres=gpu:a100:8 --ntasks=1 --cpus-per-task=128

<details>
<summary>Answer</summary>

**Correct: A**

For PyTorch DDP with NCCL, each GPU runs one process. --nodes=4 allocates 4 nodes, --gres=gpu:8 requests 8 GPUs per node, --ntasks-per-node=8 creates 8 tasks (processes) per node matching the GPU count, and --cpus-per-task=8 assigns CPU cores to each process. This gives 32 total processes (4 nodes × 8 GPUs) for distributed training.

**Why the others are wrong:**
- --ntasks=4 creates only 4 total tasks across all nodes instead of 8 per node (32 total), underutilizing 28 GPUs that would sit idle
- --gres=gpu:32 requests 32 GPUs per node (128 total) which exceeds hardware capacity; --ntasks=32 creates 32 tasks per node instead of total, resulting in 128 tasks for 32 GPUs
- --ntasks=1 creates only one task total across all nodes, leaving 31 GPUs unused; --cpus-per-task=128 is excessive and may exceed node CPU count

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q4.** During a multi-node NCCL training job across 8 nodes with InfiniBand interconnect, the job hangs at initialization. NCCL_DEBUG=INFO shows 'Net/IB: No device found'. Which environment variable configuration resolves this issue?

A) export NCCL_IB_HCA=mlx5_0 and export NCCL_IB_DISABLE=0
B) export NCCL_IB_DISABLE=1 and export NCCL_SOCKET_IFNAME=eth0
C) export NCCL_DEBUG=WARN and export NCCL_P2P_DISABLE=1
D) export NCCL_SOCKET_IFNAME=ib0 and export NCCL_NET_GDR_LEVEL=0

<details>
<summary>Answer</summary>

**Correct: A**

NCCL_IB_HCA=mlx5_0 explicitly specifies the InfiniBand device name (mlx5 is Mellanox/NVIDIA ConnectX HCA), and NCCL_IB_DISABLE=0 ensures InfiniBand is enabled (it's default but explicit here). The 'No device found' error means NCCL cannot auto-detect the IB device, so explicit specification is required.

**Why the others are wrong:**
- NCCL_IB_DISABLE=1 disables InfiniBand entirely and falls back to Ethernet (eth0), which defeats the purpose of having InfiniBand and severely degrades multi-node bandwidth from 200Gbps to 10-25Gbps
- Changing DEBUG level to WARN and disabling P2P doesn't address the device detection issue; P2P_DISABLE affects GPU-Direct RDMA within a node, not inter-node IB connectivity
- ib0 is typically the IP-over-IB interface, not the native RDMA device NCCL needs; NCCL_NET_GDR_LEVEL controls GPU-Direct RDMA but doesn't fix device detection

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q5.** A team wants to submit a distributed training job using Run:ai that requires 4 worker pods with 8 GPUs each, gang scheduling to ensure all pods start together, and PyTorch distributed launch. Which runai submit command correctly configures this job?

A) runai submit train-job --replicas 4 --gpu 8 --gang-scheduling --distributed-launch torch --command 'python train.py'
B) runai submit train-job --workers 4 --gpu 8 --image pytorch:latest --command 'python train.py'
C) runai submit train-job --workers 4 --gpu 32 --gang-scheduling --command 'torchrun train.py'
D) runai submit train-job --workers 4 --gpu-per-worker 8 --scheduling-policy gang --command 'python -m torch.distributed.launch train.py'

<details>
<summary>Answer</summary>

**Correct: A**

--replicas 4 creates 4 worker pods, --gpu 8 allocates 8 GPUs per pod (32 total), --gang-scheduling ensures all pods start together or none start (critical for distributed training), and --distributed-launch torch automatically sets up PyTorch DDP environment variables (MASTER_ADDR, MASTER_PORT, RANK, WORLD_SIZE).

**Why the others are wrong:**
- --workers flag is not a valid Run:ai parameter; the correct flag is --replicas. Without --gang-scheduling, pods start independently which can cause distributed training initialization failures
- --gpu 32 requests 32 GPUs total (not per pod), resulting in uneven GPU distribution. --gang-scheduling flag is correct but the GPU allocation is wrong, and torchrun requires proper environment variables that --distributed-launch sets up
- --gpu-per-worker is not a valid Run:ai flag (correct is --gpu which applies per replica), and --scheduling-policy is incorrect (correct is --gang-scheduling). Manual torch.distributed.launch is unnecessary when --distributed-launch handles it

</details>

---

**Q6.** An organization runs mixed workloads: inference services requiring low latency and training jobs that can tolerate delays. They deploy both on the same Kubernetes cluster with GPUs. Which scheduling strategy BEST optimizes GPU utilization while meeting SLA requirements?

A) Use Run:ai with guaranteed quotas for inference workloads and best-effort quotas for training workloads, enabling preemption for training jobs
B) Deploy inference on dedicated GPU nodes with taints and training on separate nodes without taints, using cluster autoscaling for both
C) Configure all workloads with the same priority class and let Kubernetes default scheduler handle GPU allocation with resource requests
D) Use time-based scheduling: inference jobs run during business hours, training jobs run at night with cron-based job submission

<details>
<summary>Answer</summary>

**Correct: A**

Run:ai's quota system allows guaranteed quotas for inference (ensuring resources are always available for low-latency SLA), best-effort quotas for training (can use idle resources), and preemption (training jobs can be paused when inference needs GPUs). This maximizes utilization by letting training use idle GPUs while protecting inference SLAs through guaranteed allocation and preemption.

**Why the others are wrong:**
- Dedicated nodes with taints provide isolation but waste GPU resources when inference doesn't fully utilize its dedicated GPUs, while training sits idle. This is resource-inefficient and increases cost
- Same priority for all workloads means training jobs can starve inference jobs during contention, violating low-latency SLAs. Default Kubernetes scheduler doesn't understand workload-specific requirements or implement intelligent preemption
- Time-based scheduling assumes predictable load patterns and wastes GPU resources during off-hours for inference (GPUs sit idle at night). Modern inference workloads often have 24/7 requirements, and manual cron scheduling is inflexible

</details>

---

**Q7.** A data scientist launches a Slurm job with srun for multi-node training but notices NCCL performance is 10x slower than expected. NCCL_DEBUG output shows 'Using network Socket'. The cluster has 100Gbps Ethernet. What is the MOST likely cause?

A) The Slurm job did not request exclusive node access, causing network contention with other jobs
B) NCCL_IB_DISABLE was accidentally set to 1, forcing fallback to socket communication
C) NCCL is using TCP sockets instead of RDMA because NCCL_SOCKET_IFNAME is not set to the correct high-speed interface
D) The training script uses DataParallel instead of DistributedDataParallel, limiting NCCL efficiency

<details>
<summary>Answer</summary>

**Correct: C**

'Using network Socket' indicates NCCL defaulted to TCP sockets, likely because it auto-selected a low-speed interface (like 1Gbps management network) instead of the 100Gbps data network. NCCL_SOCKET_IFNAME should be set to the high-speed interface name (e.g., 'eth1' or 'enp94s0f0') to force NCCL to use the correct network for inter-node communication.

**Why the others are wrong:**
- While node sharing can cause contention, 10x slowdown specifically with 'Using network Socket' message indicates wrong network interface selection, not contention. Exclusive access would help but doesn't fix the interface issue
- If NCCL_IB_DISABLE=1 was set (and there's InfiniBand), the message would be similar, but the question states 100Gbps Ethernet (not IB). The issue is socket interface selection on Ethernet, not IB being disabled
- DataParallel vs DistributedDataParallel affects scaling efficiency but wouldn't cause NCCL to report 'Using network Socket' or cause 10x slowdown on network communication. This is a network configuration issue, not a code issue

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q8.** A Triton Inference Server deployment serves a BERT model and a GPT-2 model. BERT averages 10ms per request, GPT-2 averages 100ms. Both receive 100 requests/sec. To minimize p95 latency for BERT without degrading GPT-2 throughput, which configuration should be applied?

A) Enable dynamic batching for BERT with max_queue_delay_microseconds=5000 and GPT-2 with max_queue_delay_microseconds=50000
B) Enable dynamic batching for both models with the same max_queue_delay_microseconds value
C) Configure separate instance groups: BERT with 10 instances, GPT-2 with 1 instance, both on the same GPU
D) Deploy BERT and GPT-2 on separate Triton server instances with dedicated GPUs for each

<details>
<summary>Answer</summary>

**Correct: A**

Different max_queue_delay values optimize for each model's latency characteristics. BERT's 10ms inference allows shorter queue delay (5ms = 5000μs) to minimize latency impact while still batching. GPT-2's 100ms inference can tolerate longer queuing (50ms = 50000μs) to build larger batches for throughput. This balances BERT's latency SLA with GPT-2's throughput needs.

**Why the others are wrong:**
- Same max_queue_delay for both models either degrades BERT latency (if too long for GPT-2's needs) or prevents GPT-2 from effective batching (if too short for BERT's needs). Different models need different batching strategies
- Multiple instances create GPU memory overhead for duplicated model weights. BERT's 10 instances would replicate the model 10x in GPU memory. This wastes memory and doesn't address the actual issue of balancing latency vs throughput through batching
- Separate servers with dedicated GPUs doubles infrastructure cost and prevents GPU sharing benefits. Triton is designed for multi-model serving on shared GPUs precisely to avoid this inefficiency

</details>

---

