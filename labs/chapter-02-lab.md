# Chapter 2 Lab: Workload Manager Integration: Slurm and Kubernetes on BCM

**Estimated time:** 40 minutes

## Exercise

Use BCM to install Slurm on a four-node cluster with A100 GPUs, configure gres.conf for 8 GPUs per node, verify 'sinfo' shows 32 total GPUs, then install Kubernetes on the same cluster and confirm GPU Operator pods are running

## Key takeaway

BCM streamlines Slurm and Kubernetes installation, but gres.conf (Slurm) and cluster readiness (K8s) are critical configuration steps the exam tests.
