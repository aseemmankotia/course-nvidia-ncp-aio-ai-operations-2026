# Chapter 6 Cheat Sheet: Multi-Tenant Workload Deployment

## NVIDIA NIM on Kubernetes

### Deployment YAML
```yaml
apiVersion: apps/v1
kind: Deployment
spec:
  template:
    spec:
      containers:
      - name: nim
        image: nvcr.io/nim/<model>:latest
        resources:
          requests:
            nvidia.com/gpu: 2  # MUST SPECIFY
          limits:
            nvidia.com/gpu: 2  # MUST MATCH REQUEST
```
**Exam Traps:** Missing `nvidia.com/gpu` → pod never schedules. Request ≠ limit → error. CPU-only nodes cannot run NIM.

---

## Triton Inference Server

### Model Repository Structure
```
model_repository/
├── bert/
│   ├── config.pbtxt
│   └── 1/
│       └── model.onnx
└── gpt2/
    ├── config.pbtxt
    └── 1/
        └── model.plan
```

### config.pbtxt Example
```protobuf
name: "bert"
platform: "onnxruntime_onnx"
max_batch_size: 16
dynamic_batching {
  max_queue_delay_microseconds: 5000
  preferred_batch_size: [4, 8, 16]
}
instance_group [{ kind: KIND_GPU count: 1 }]
```
**Key Settings:**
- `max_batch_size`: Per-model GPU memory limit
- `max_queue_delay_microseconds`: Latency budget (5000 = 5ms)
- `preferred_batch_size`: Optimal batch targets
- `instance_group count`: Model replicas on GPU

**Exam Tip:** Low latency = low delay (5000μs). High throughput = high delay (50000μs).

---

## Slurm Multi-Node Training

### Batch Script Template
```bash
#!/bin/bash
#SBATCH --nodes=4              # 4 nodes
#SBATCH --gres=gpu:8           # 8 GPUs PER NODE
#SBATCH --ntasks-per-node=8    # 8 tasks PER NODE (= GPU count)
#SBATCH --cpus-per-task=8      # CPUs per task
#SBATCH --time=24:00:00

srun python -m torch.distributed.launch \
  --nproc_per_node=8 \
  --nnodes=4 \
  --node_rank=$SLURM_NODEID \
  train.py
```
**Flag Breakdown:**
- `--nodes=N`: Total nodes
- `--gres=gpu:N`: GPUs per node
- `--ntasks-per-node=N`: MUST = GPU count for DDP
- `srun`: Launches across allocated nodes

**Common Mistakes:**
- `--ntasks=32` (total) instead of `--ntasks-per-node=8` → underutilizes GPUs
- `--gres=gpu:32` (thinks 32 per node) → allocation fails

---

## NCCL Configuration

### InfiniBand Setup
```bash
export NCCL_IB_HCA=mlx5_0           # Explicit device (find: ibstat)
export NCCL_IB_DISABLE=0            # Enable IB
export NCCL_DEBUG=INFO              # Verbose logging
```

### Ethernet Setup
```bash
export NCCL_SOCKET_IFNAME=eth1      # High-speed interface (find: ip a)
export NCCL_IB_DISABLE=1            # Disable IB
export NCCL_DEBUG=INFO
```

### Debug Output Interpretation
| Message | Meaning | Fix |
|---------|---------|-----|
| `Using network Socket` | TCP sockets (slow) | Set `NCCL_SOCKET_IFNAME` to fast interface |
| `Net/IB: No device found` | IB not detected | Set `NCCL_IB_HCA` explicitly |
| `Using network IB` | InfiniBand RDMA (fast) | ✓ Correct |

**Exam Gotcha:** Default may pick 1Gbps mgmt interface → 10x slowdown. Always set `NCCL_SOCKET_IFNAME` explicitly.

---

## Run:ai Distributed Training

### Submit Command
```bash
runai submit train-job \
  --replicas 4 \
  --gpu 8 \
  --gang-scheduling \
  --distributed-launch torch \
  --image pytorch:latest \
  --command 'python train.py'
```
**Flags:**
- `--replicas N`: Worker pods (NOT `--workers`)
- `--gpu N`: GPUs per pod (total = replicas × gpu)
- `--gang-scheduling`: All pods start together or none
- `--distributed-launch torch`: Auto-sets DDP env vars

**Gang Scheduling Behavior:**
- Resources available → all pods start
- Resources insufficient → job pending, NO partial start
- Prevents distributed training hangs from missing workers

---

## Key Comparisons

| Scenario | Tool | Why |
|----------|------|-----|
| LLM inference on K8s | NIM | Optimized containers, NGC registry |
| Multi-model serving | Triton | Dynamic batching, shared GPU |
| HPC multi-node training | Slurm | Fine-grained resource control, srun |
| K8s multi-tenant training | Run:ai | Quotas, gang scheduling, preemption |

---

## Exam Numbers to Memorize

- Triton `max_queue_delay`: Microseconds (5000 = 5ms)
- Slurm `--gres=gpu:N`: Per-node count
- Run:ai `--replicas`: Total worker pods
- NCCL `NCCL_DEBUG`: INFO (verbose), WARN (default), VERSION (minimal)
- Typical IB bandwidth: 200 Gbps (HDR), 100 Gbps (EDR)
- Typical Ethernet: 100 Gbps (high-speed), 10-25 Gbps (standard)

**Latency Impact:**
- No batching: Low latency, low throughput
- `max_queue_delay=5000`: +5ms latency, medium batching
- `max_queue_delay=50000`: +50ms latency, high batching
