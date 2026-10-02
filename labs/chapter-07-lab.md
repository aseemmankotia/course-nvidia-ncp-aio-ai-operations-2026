# Chapter 7 Lab: Resource Allocation and NGC Container Management

**Estimated time:** 30 minutes

## Exercise

Create a Run:ai project 'cv-team' with quota 16 GPUs, configure a Slurm account 'nlp-group' with fair-share weight 200, set a Kubernetes namespace quota of 8 GPUs, authenticate to NGC and pull the PyTorch 24.01 container, run nvidia-smi to verify GPU visibility, and use dcgmi diag -r 3 to run a level-3 diagnostic

## Key takeaway

Run:ai quota + over-quota = total capacity; NGC authentication requires API key AND entitlements for private containers; use nvidia-smi for runtime stats, dcgmi diag for hardware health.
