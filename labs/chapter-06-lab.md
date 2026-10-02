# Chapter 6 Lab: Multi-Tenant Workload Deployment: Inference and Training

**Estimated time:** 42 minutes

## Exercise

Deploy a Triton Inference Server pod on Kubernetes serving two models, submit a Slurm sbatch script for 8-GPU PyTorch training on one node, launch a 4-node 32-GPU NCCL job with srun, and submit a Run:ai distributed training job with gang scheduling for 2 workers × 4 GPUs each

## Key takeaway

NIM and Triton are Kubernetes patterns; Slurm needs --ntasks-per-node for multi-node; Run:ai gang scheduling is mandatory for distributed training—know the exact flags for each.
