# Chapter 8 Lab: Troubleshooting NVIDIA AI Infrastructure

**Estimated time:** 40 minutes

## Exercise

Fix a Docker container that fails with 'could not select device driver nvidia'; restart fabric manager on a DGX A100 and verify NVLink status with nvidia-smi topo -m; diagnose a NCCL hang by enabling NCCL_DEBUG=INFO and checking ibstatus for InfiniBand link down; verify GPUDirect Storage is working with gdsio benchmark

## Key takeaway

Docker GPU errors: check nvidia-container-toolkit and daemon.json first; NCCL hangs: enable NCCL_DEBUG=INFO and verify IB ports + nv_peer_mem; fabric manager is for NVLink, not RDMA.
