# Chapter 7: Resource Allocation & NGC Container Management

## Run:ai Project Resource Allocation

| Setting | Purpose | Exam Note |
|---------|---------|----------|
| Guaranteed Quota | Minimum resources ALWAYS available | Reserved - other projects can't use |
| Over-quota Limit | Max resources when cluster has capacity | Fair-share allocation, no preemption |
| Priority Weight | Scheduling order when resources limited | Higher number = higher priority |
| Node Affinity | Pin project to specific nodes | Use for licensed software/hardware |

**Key Commands:**
```bash
# Create project with 4 GPU guarantee, 8 GPU max
runai project create team-a --gpu-quota 4 --over-quota-limit 8

# Set priority weight
runai project set team-a --priority-weight 100
```

## Slurm Fair-Share Configuration

| Component | Purpose | Config Location |
|-----------|---------|----------------|
| Account Shares | Relative priority weights | sacctmgr database |
| PriorityType | Enable multifactor priority | slurm.conf |
| PriorityWeightFairshare | Weight of fair-share in priority | slurm.conf |
| FairShareDampeningFactor | How fast usage affects priority | slurm.conf |

**Setup Commands:**
```bash
# Create account hierarchy with shares (60/40 split)
sacctmgr add account team-a Cluster=cluster1 Shares=60
sacctmgr add account team-b Cluster=cluster1 Shares=40

# View fair-share tree
sshare -a
```

**slurm.conf Required Settings:**
```
PriorityType=priority/multifactor
PriorityWeightFairshare=10000
PriorityDecayHalfLife=7-0  # 7 days
```

## Kubernetes Resource Quotas

| Object | Scope | Use Case |
|--------|-------|----------|
| ResourceQuota | Namespace total | Cap team's aggregate GPU/CPU/memory |
| LimitRange | Per pod/container | Prevent single pod from hogging resources |

**ResourceQuota Example:**
```bash
# Limit namespace to 8 GPUs, 64 CPU cores, 256GB RAM
kubectl create quota team-limits -n ml-team-a \
  --hard=requests.nvidia.com/gpu=8,requests.cpu=64,requests.memory=256Gi
```

**LimitRange Example:**
```yaml
apiVersion: v1
kind: LimitRange
metadata:
  name: gpu-limits
spec:
  limits:
  - max:
      nvidia.com/gpu: "4"  # No pod can request >4 GPUs
    type: Container
```

## NGC Container Management

**Registry Authentication:**
```bash
# Login to NGC registry
docker login nvcr.io
# Username: $oauthtoken (literal string)
# Password: <your_NGC_API_key>
```

**Container Naming Convention:**
```
nvcr.io/nvidia/<framework>:<YY.MM>-py3
         ^^^^^^  ^^^^^^^^^  ^^^^^  ^^^
         org     framework  version python

Example: nvcr.io/nvidia/pytorch:24.01-py3
```

**Common NGC Containers:**
| Framework | Path | Use Case |
|-----------|------|----------|
| PyTorch | nvcr.io/nvidia/pytorch | General deep learning |
| TensorFlow | nvcr.io/nvidia/tensorflow | TensorFlow workloads |
| NeMo | nvcr.io/nvidia/nemo | LLM training (Megatron) |
| RAPIDS | nvcr.io/nvidia/rapidsai/rapids | Data science (cuDF, cuML) |
| Triton | nvcr.io/nvidia/tritonserver | Model serving/inference |

**Exam Tip:** NEVER use `:latest` tag - always pin specific version (YY.MM format)

## GPU Monitoring & Diagnostics

### nvidia-smi Common Commands

```bash
# Basic GPU status
nvidia-smi

# Specific GPU details
nvidia-smi -i 2 -q -d MEMORY,UTILIZATION,POWER,TEMPERATURE

# Monitor GPU usage every 1 second
nvidia-smi dmon -i 0 -s mu

# List running processes
nvidia-smi pmon
```

### DCGM Diagnostics

| Level | Test Duration | What It Tests |
|-------|--------------|---------------|
| -r 1 | ~10 seconds | Quick health check |
| -r 2 | ~2 minutes | Medium stress test |
| -r 3 | ~10-15 min | Comprehensive HW validation |

```bash
# Run level 3 diagnostic on GPU 2
dcgmi diag -r 3 -i 2

# Health check specific GPU
dcgmi health -g 2

# Check group of GPUs
dcgmi health -g 0,1,2,3
```

## Troubleshooting GPU Utilization

| Symptom | Likely Cause | Fix |
|---------|--------------|-----|
| 0% util, job running | CUDA error / CPU fallback | Check stderr for CUDA errors |
| Low util, high power | Memory-bound workload | Optimize data loading |
| Util spikes 0-100% | Small batch size | Increase batch size |
| Process listed, 0MB memory | Process crashed | Check nvidia-smi pmon for exit |

**Exam Pattern:** 0% utilization + running process = application not launching GPU kernels (CUDA error)

## Quick Reference: Allocation Strategies

**Multi-tenancy Best Practice:**
- Run:ai: Use guaranteed quota for SLA teams, over-quota for burst
- Slurm: Fair-share accounts by department, QOS for job classes  
- Kubernetes: ResourceQuota per team namespace + LimitRange per pod

**Priority Scheduling:**
- Run:ai: Priority weight 1-100 (higher = faster scheduling)
- Slurm: Multifactor priority = age + fairshare + QOS + partition
- Kubernetes: PriorityClass (high/medium/low) for pod preemption
