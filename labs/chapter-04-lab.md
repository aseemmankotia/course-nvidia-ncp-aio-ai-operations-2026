# Chapter 4 Lab: Slurm Cluster Administration for AI Workloads

**Estimated time:** 35 minutes

## Exercise

Create a Slurm partition 'ml-team' with 8 A100 nodes, define a QOS 'highprio' with max 16 GPUs per user, add users to the 'ml-research' account with fair-share weight 100, and configure MIG on one A100 to expose seven 1g.10gb slices

## Key takeaway

Slurm partitions, QOS, and MIG integration are heavily tested—know the exact commands (scontrol, sacctmgr) and gres.conf syntax for MIG profiles.
