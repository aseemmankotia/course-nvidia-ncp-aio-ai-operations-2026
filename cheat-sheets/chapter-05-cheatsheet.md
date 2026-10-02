# Chapter 5 Cheat Sheet: Kubernetes & Run:ai for AI Infrastructure

## GPU Operator Components

| Component | Function | Exam Focus |
|-----------|----------|------------|
| nvidia-device-plugin | Advertises GPUs to kubelet, schedules pods to GPU nodes | Resource discovery |
| nvidia-dcgm-exporter | Exposes GPU metrics (util, temp, power, memory) in Prometheus format | Monitoring integration |
| nvidia-container-toolkit | Modifies container runtime to enable GPU access in containers | Runtime configuration |
| gpu-feature-discovery | Labels nodes with GPU properties (model, CUDA version, driver) | Node labeling |

## Kubernetes GPU Resource Quotas

```bash
# Create namespace
kubectl create namespace ml-team-a

# Create ResourceQuota limiting to 8 GPUs
kubectl create quota gpu-limit --hard=nvidia.com/gpu=8 -n ml-team-a

# Verify quota
kubectl describe quota gpu-limit -n ml-team-a
```

**Exam tip:** ResourceQuota is separate object, not namespace property. Use `--hard=nvidia.com/gpu=N` syntax.

## Run:ai Core Concepts

| Concept | Definition | Exam Scenario |
|---------|------------|---------------|
| Project | Logical grouping of workloads with quota and policies | Multi-tenant isolation |
| Quota | Guaranteed GPU allocation | Minimum resources always available |
| Over-quota | Maximum burstable allocation when cluster has idle capacity | Cost optimization via sharing |
| Fractional GPU | Allocate portions of GPU (0.01 to 1.0) | Bin-packing small jobs |

**Example:** Project with quota=4, over-quota=8 can always use 4 GPUs, bursts to 8 if available.

## Run:ai CLI Workflow

```bash
# Install and authenticate
runai login

# Submit job with fractional GPU
runai submit train-job --image pytorch:latest --gpu 0.5 --gpu-memory 16G

# Submit distributed training job (gang scheduling)
runai submit dist-train --image nvcr.io/nvidia/pytorch:24.01-py3 --gpu 8 --workers 8

# List jobs
runai list jobs

# View job logs
runai logs train-job
```

**Exam tip:** `--gpu` accepts fractional values (0.25, 0.5). Gang scheduling automatic when `--workers` > 1.

## MIG on Kubernetes

### A100 80GB MIG Profiles (Heavily Tested)

| Profile | Instances per GPU | Memory per Instance | Use Case |
|---------|-------------------|---------------------|----------|
| 1g.10gb | 7 | 10 GB | Small inference |
| 2g.20gb | 3 | 20 GB | Medium training |
| 3g.40gb | 2 | 40 GB | Large training |
| 7g.80gb | 1 | 80 GB | Full GPU equivalent |

### MIG Configuration Steps

```bash
# 1. Enable MIG on GPU (node level, requires reboot)
nvidia-smi -mig 1

# 2. Create MIG instances (example: 3g.20gb profile)
nvidia-smi mig -cgi 3g.20gb,3g.20gb,3g.20gb -C

# 3. Label node with MIG configuration
kubectl label node gpu-node-1 nvidia.com/mig.config=all-3g.20gb

# 4. Deploy device plugin with MIG strategy
# In GPU Operator values:
devicePlugin:
  config:
    name: mig-strategy
    data:
      strategy: single
```

**Exam tip:** 
- MIG strategy `single` = expose MIG instances as separate resources
- MIG strategy `none` = expose full GPUs only (default)
- Pods request `nvidia.com/mig-3g.20gb: 1` NOT `nvidia.com/gpu: 1`

## Run:ai Scheduling Policies

| Policy | Objective | Exam Use Case |
|--------|-----------|---------------|
| Bin-packing | Maximize GPU utilization density | Cost optimization (fewer nodes active) |
| Fairness | Equal resource distribution over time | Prevent starvation, democratic sharing |
| Gang scheduling | Atomic allocation of all resources | Distributed training (all-or-nothing) |

**Example:** Distributed training on 8 GPUs requires gang scheduling with `min_replicas=8` to prevent deadlock.

## Monitoring Stack Integration

```bash
# DCGM exporter metrics endpoint
curl http://<node-ip>:9400/metrics

# Key metrics (exam tested):
# DCGM_FI_DEV_GPU_UTIL - GPU utilization %
# DCGM_FI_DEV_FB_USED - Framebuffer (memory) used MB
# DCGM_FI_DEV_GPU_TEMP - GPU temperature C
# DCGM_FI_DEV_POWER_USAGE - Power usage W
```

**Prometheus scrape config:**
```yaml
- job_name: 'gpu-metrics'
  static_configs:
    - targets: ['<node-ip>:9400']
```

## Common Exam Traps

1. **MIG + device plugin:** Strategy must be `single` to expose MIG resources. Default `none` only exposes full GPUs.
2. **Over-quota != guaranteed:** Over-quota jobs can be preempted if cluster fills up. Only quota is guaranteed.
3. **Fractional GPU ≠ MIG:** Fractional GPU = software partitioning (memory + time-slicing). MIG = hardware partitioning.
4. **Gang scheduling necessity:** Distributed training MUST use gang scheduling to avoid partial allocation deadlock.
5. **ResourceQuota scope:** Namespace-level only. Cannot set cluster-wide GPU quota via ResourceQuota (use Run:ai projects).

## Quick Reference: Resource Names

```yaml
# Full GPU
resources:
  limits:
    nvidia.com/gpu: 1

# MIG instance (strategy=single)
resources:
  limits:
    nvidia.com/mig-3g.20gb: 1

# Run:ai fractional (via CLI, not YAML)
--gpu 0.5
```
