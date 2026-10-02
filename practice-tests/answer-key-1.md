# Answer Key — Practice Test 1

**Q1. Correct: C**

The correct installation sequence requires: (1) Kubernetes cluster deployed via BCM, (2) NVIDIA GPU Operator installed to expose GPU resources to Kubernetes, (3) Run:ai control plane deployed (typically on master/control plane nodes), and (4) Run:ai cluster component installed via Helm chart to manage GPU scheduling and resource allocation across worker nodes.

---

**Q2. Correct: A**

Best practice for firmware updates through BCM includes: organizing nodes into categories for controlled rollout, draining workloads to prevent job failures, staged deployment (verify on subset before full rollout), and maintaining previous firmware versions for rollback capability. This minimizes risk and allows recovery if issues arise.

---

**Q3. Correct: D**

DOCA Services are specifically designed to execute on the BlueField DPU's Arm cores, not the host CPU. This requires the DOCA SDK installed on the DPU's embedded operating system (typically Ubuntu or CentOS for Arm), along with container runtime support if deploying containerized DOCA applications. The DPU runs its own OS instance separate from the host.

---

**Q4. Correct: D**

Node categories in BCM are the primary mechanism for organizing compute nodes by hardware specs, intended use, or any logical grouping. Categories enable administrators to apply software images, firmware updates, configuration changes, and monitoring policies to groups of nodes rather than individually, which is essential for managing 40 nodes across different workload types.

---

**Q5. Correct: B**

Slurm's core architecture requires: slurmctld (controller daemon) on the management/controller node to manage job scheduling and cluster state; slurmd (compute daemon) on each compute node to execute jobs and report status; and slurmdbd (database daemon) connected to a database (typically MariaDB/MySQL) for job accounting and historical data. This is the minimum complete configuration for a production Slurm cluster with accounting.

---

**Q6. Correct: B**

When nodes are network-reachable (pingable) but show offline/degraded in BCM Base View, the issue is typically the BCM agent daemon on those nodes. The agent handles communication between the node and BCM server for inventory, monitoring, and management. Network reconfiguration may have disrupted agent connectivity due to firewall rules, changed network interfaces, or service interruption. Restarting the agent and verifying communication paths is the correct first troubleshooting step.

---

**Q7. Correct: B**

Run:ai requires the NVIDIA GPU Operator to be installed first, as it relies on GPU discovery and resource management capabilities provided by the operator. All GPUs must be visible to Kubernetes before Run:ai can schedule workloads on them.

---

**Q8. Correct: B**

DOCA Services are deployed on the Arm cores within the BlueField DPU itself. The DPU acts as a separate system-on-chip with its own Arm-based compute, where DOCA services run independently from the host.

---

**Q9. Correct: D**

BCM Base View provides real-time monitoring of node health with detailed diagnostics. Checking node health details first allows the administrator to identify the root cause (network issues, failed components, configuration problems) before taking corrective action.

---

**Q10. Correct: A**

Base Command Manager automates the installation and deployment of Slurm across cluster nodes, including configuration management and templating, significantly reducing manual effort and configuration errors in large clusters.

---

**Q11. Correct: B**

Rolling updates in batches—draining nodes, updating firmware, verifying health, then proceeding to the next batch—ensures workloads continue running on unaffected nodes while minimizing downtime and eliminating data loss from interrupted jobs.

---

**Q12. Correct: A**

BCM provides role-based access control (RBAC) that allows administrators to create user accounts with specific permissions tied to node categories. This enables multi-tenant isolation where each group can only access, view, and manage their assigned node pools.

---

**Q13. Correct: C**

DOCA Services are designed to run natively on the BlueField DPU's Arm cores in the embedded Linux environment. This architecture offloads infrastructure services (firewall, telemetry, storage acceleration) from the host CPU, allowing the x86 compute resources to focus entirely on AI workloads. The DPU's Arm cores execute DOCA Services independently, providing true separation and optimal performance.

---

**Q14. Correct: B**

The NVIDIA GPU Operator is a prerequisite for Run:ai installation on Kubernetes. It must be installed first to deploy and manage the GPU drivers, NVIDIA Container Toolkit (nvidia-docker), device plugin, and other components required for GPU workload orchestration. Without the GPU Operator, Kubernetes cannot properly detect, allocate, or monitor GPU resources, making Run:ai functionality impossible. The GPU Operator ensures all nodes have consistent GPU software stack before Run:ai can manage GPU scheduling.

---

**Q15. Correct: A**

PreemptMode=REQUEUE with PreemptParameters=grace_time:120 allows preemption with a 2-minute grace period before jobs are requeued. This gives inference jobs time to checkpoint. QOS priority levels determine which jobs can preempt others.

---

**Q16. Correct: C**

8x H100 at 700W = 5.6kW GPU power alone, plus CPU and other components easily exceed 35kW RDHx capacity. Liquid cooling with direct-to-chip cold plates is the industry-standard solution for high-density H100 deployments, handling 700W TDP efficiently at the source.

---

**Q17. Correct: A**

Pods must specify nvidia.com/gpu in resources.limits (not just requests) for the device plugin to allocate GPUs. The presence of nvidia.com/gpu.product labels confirms device plugin discovery works, but pods won't be scheduled without proper resource limits.

---

**Q18. Correct: D**

A100-40GB supports 7x 1g.10gb MIG instances, each with 1 GPU slice and 10GB memory. This maximizes instance count (7) while meeting the ≥10GB memory requirement. This is the standard max-partition configuration for multi-tenant use cases.

---

**Q19. Correct: A**

Run:ai's Guaranteed quota ensures ML-Research always gets 16 GPUs, Limit=32 allows bursting to 32 when cluster capacity permits. ML-Production's Guaranteed=8 with Preemptible=false ensures their 8 GPUs cannot be preempted by other projects.

---

**Q20. Correct: D**

Slurm's RealMemory reports usable memory after OS/kernel overhead (typically 10-20GB on AI nodes). 512GB physical - ~32GB overhead = ~480GB (~492000 MB) available, which can fall below a 480000 MB threshold. MinMemSize should account for this overhead.

---

**Q21. Correct: D**

QOS (Quality of Service) in Slurm is specifically designed for this use case. By creating QOS entries with PreemptMode=OFF for critical jobs, those jobs cannot be preempted. Setting PreemptMode=REQUEUE for lower-priority QOS allows those jobs to be preempted when higher-priority work needs resources. QOS can then be assigned to users or accounts, providing granular control over preemption behavior while maintaining resource sharing.

---

**Q22. Correct: C**

For large-scale multi-node GPU training with NCCL, InfiniBand HDR provides superior performance: lower latency (sub-microsecond), higher effective throughput for collective operations, native RDMA support, and adaptive routing that optimally handles the all-reduce traffic patterns. NVIDIA's AI infrastructure reference architectures recommend InfiniBand for clusters exceeding 16-32 GPUs where inter-node communication is critical. The fat-tree topology with rail-optimized connections ensures non-blocking bandwidth for GPU-to-GPU communication.

---

**Q23. Correct: D**

In Run:ai, setting a project quota defines the guaranteed allocation, while the Over-quota weight parameter (0.0-1.0) controls how aggressively the project can consume idle GPU resources beyond its quota. With quota=8 and Over-quota weight enabled (typically 0.5-1.0), the project is guaranteed 8 GPUs but can opportunistically use idle GPUs when available. Run:ai's scheduler automatically reclaims over-quota resources when other projects need them, naturally limiting burst during busy business hours when demand is high.

---

**Q24. Correct: B**

The 1g.10gb MIG profile provides 10GB of memory per instance, exactly meeting the workload requirement. On an A100-80GB GPU, this profile allows creation of up to 7 instances (7 × 10GB = 70GB, with remaining capacity for the GPU's reserved memory). This maximizes instance count while meeting the memory requirement. Each instance gets 1/7th of the GPU's compute resources and full memory isolation, ideal for inference workloads.

---

**Q25. Correct: A**

Kubernetes Deployment with HPA is the correct solution for inference workloads requiring auto-scaling. NIM containers expose Prometheus metrics that HPA can consume for custom metric-based scaling. GPU time-slicing allows multiple NIM replicas to share GPUs efficiently during low-load periods while scaling out to dedicated GPUs under high load. This architecture is specifically designed for production inference with latency SLAs.

---

**Q26. Correct: C**

NCCL 'Connection refused' errors during multi-node initialization typically indicate network connectivity issues. When InfiniBand is disabled or unavailable, NCCL falls back to TCP/IP sockets. If firewall rules block the required ports (typically in the ephemeral range) or if network security policies prevent inter-node communication, NCCL initialization fails with connection refused. This is the most common multi-node training issue in NVIDIA AI infrastructure. The fix involves either enabling IB (NCCL_IB_DISABLE=0, ensure IB drivers loaded) or opening required TCP ports.

---

**Q27. Correct: C**

This is the correct workflow for air-gapped environments. First, authenticate to NGC using 'docker login nvcr.io' with username '$oauthtoken' (literal string) and your NGC API key as password. Then pull the container from NGC (nvcr.io/nvidia/tensorflow:24.01-tf2-py3). Finally, tag the image for your private registry and push it. This ensures the optimized NGC container is available in your private registry for nodes without internet access. The $oauthtoken username is NGC's authentication mechanism.

---

**Q28. Correct: B**

This configuration correctly implements the requirement using Run:ai's resource allocation model. 'Deserved GPUs = 8' guarantees minimum allocation through Run:ai's fairness algorithm - the team is guaranteed 8 GPUs even under contention. 'GPU quota = 16' sets the hard maximum the team can use. 'Over-quota enabled' allows borrowing additional resources beyond deserved when available, but quota enforces the 16 GPU ceiling. This is the standard Run:ai pattern for guaranteed minimums with elastic maximums.

---

**Q29. Correct: C**

Nsys (NVIDIA Nsight Systems) with --gpu-metrics-device flag provides detailed profiling of GPU memory allocation events, including exact allocation sizes, memory pool behavior, fragmentation patterns, and temporary allocations that may not show in aggregate utilization. OOM errors often occur due to memory fragmentation or temporary allocation spikes even when average utilization appears low. Nsys captures timeline data showing allocation/deallocation patterns that explain why OOM occurs despite low reported utilization. This is the standard tool for diagnosing GPU memory management issues.

---

**Q30. Correct: A**

This configuration correctly implements fair-share scheduling in Slurm. PriorityType=priority/multifactor enables Slurm's multi-factor priority calculation. PriorityWeightFairshare=10000 makes fair-share the dominant factor in priority calculation. Setting FairShare=100 for Research and FairShare=50 for Development means Research gets higher priority when both have unused shares. Critically, fair-share dynamically adjusts - when Research is idle, their unused share increases Development's effective priority, allowing Development to use idle resources. When Research submits jobs, their priority increases until they receive their fair share.

---

**Q31. Correct: B**

NCCL timeout errors in multi-node jobs with healthy GPUs typically indicate network communication issues. NCCL_SOCKET_IFNAME must specify the high-speed network interface (e.g., InfiniBand or RoCE) for inter-node traffic. If unset or pointing to management/low-speed interfaces, NCCL operations timeout. This is a common deployment issue in multi-node NCCL workloads.

---

**Q32. Correct: D**

When kubectl shows GPUs available in node JSON but pods can't schedule with 'Insufficient nvidia.com/gpu', the issue is with the NVIDIA device plugin not properly advertising GPU resources to the Kubernetes scheduler. The device plugin runs as a DaemonSet in kube-system and is responsible for GPU resource discovery and allocation. Checking if it's running and reviewing logs is the correct first troubleshooting step.

---

**Q33. Correct: A**

Run:ai departments with GPU quotas and over-quota scheduling provide the exact semantics needed. Setting Production's quota to 16 guarantees those GPUs, while allowing Research (quota 20) and Development (quota 8) to use idle resources beyond their quotas when available. The allow-over-quota flag on Research/Development enables elastic scaling; Production doesn't need it since it has guaranteed allocation. This is the standard Run:ai pattern for fair-share with guarantees.

---

**Q34. Correct: D**

High GPU utilization (95%) with minimal memory usage (1.2GB on an 80GB GPU) and near-zero PCIe throughput indicates the GPU is executing operations but not processing real data. This pattern—GPU spinning in compute without data transfers—signals a code bug where training data isn't being moved to GPU memory. The GPU is likely executing operations on empty/uninitialized tensors or in a loop waiting for data that never arrives. DCGM metrics 203/204 (PCIe TX/RX) confirm no data movement.

---

**Q35. Correct: D**

The 'ibv_create_qp failed' error indicates InfiniBand verb queue pair creation failure, which is a GPUDirect RDMA issue. This prevents direct GPU-to-GPU communication over RDMA. Verifying GPUDirect RDMA is enabled and ensuring the nv_peer_mem kernel module is loaded addresses the root cause. The nv_peer_mem module enables peer-to-peer memory access required for GPUDirect RDMA to function.

---

**Q36. Correct: D**

When nodes are stuck in 'Provisioning' state during image sync, the most direct diagnostic is to examine the bcm-provisioning daemon logs. HTTP 503 errors indicate the image repository is unavailable or overloaded. Connection timeouts suggest network issues between BCM and the registry, or the registry itself being down. These logs will show the exact failure point in the image pull/sync process, whether it's authentication, network, or repository availability.

---

**Q37. Correct: C**

The error 'nvidia-container-cli: initialization error' specifically indicates the nvidia-container-toolkit is missing, outdated, or misconfigured. Version 1.14.0+ provides compatibility with modern drivers and container runtimes. Restarting Docker after installation ensures the runtime picks up the new configuration. The 'nvidia-container-cli info' command verifies the toolkit can properly communicate with the driver and enumerate GPUs.

---

**Q38. Correct: C**

NCCL dynamically selects ports for inter-node communication, typically in the 60000-61000 range. 'Connection refused' on port 6379 (or any specific port) indicates a firewall is blocking the connection attempt. The fact that single-node jobs work (no network required) and only specific node pairs fail (not all pairs) strongly suggests firewall rules differ across nodes. This is a common issue in newly deployed clusters where compute node firewall policies weren't coordinated.

---

**Q39. Correct: B**

The problem is metadata-heavy workload (many small files) on NFS, which is optimized for large sequential I/O. Converting to TFRecord/WebDataset packs thousands of small images into large sequential files, drastically reducing metadata operations (open/close/stat calls). Storing on local NVMe provides microsecond latency vs NFS's millisecond latency. Parallel filesystems like BeeGFS stripe data across multiple servers, providing higher aggregate throughput. This addresses both the small-file problem and storage performance.

---

**Q40. Correct: B**

Major version upgrades of Base Command Manager can reset or modify RBAC policies as new permission models are introduced. The specific error 'Access Denied: Insufficient permissions' combined with the system administrator (who typically has full admin role) being unaffected strongly indicates a role/permission issue. BCM uses granular RBAC, and provisioning nodes requires specific permissions that may need to be re-assigned after upgrade. The solution directly addresses permission restoration.

---

**Q41. Correct: A**

NCCL socket connection failures during multi-node training while single-node works indicates network interface selection issues. NCCL_SOCKET_IFNAME must specify the correct high-speed network interface (e.g., ib0, eth1) to avoid NCCL defaulting to slower management networks. This is a common multi-node scaling issue.

---

**Q42. Correct: A**

GPUDirect Storage (GDS) with cuFile API provides the largest performance gain by eliminating CPU bottlenecks and enabling direct GPU-to-storage data paths. Standard Python file I/O cannot leverage GDS even when installed. The cuFile API is specifically designed for this use case and can achieve near-local-storage performance over supported network storage.

---

**Q43. Correct: C**

When a node is stuck in Provisioning state after successful OS deployment, the issue is typically failed registration with the BCM management plane. The bcm-node-agent running on the compute node is responsible for registering with BCM and reporting ready status. SSH access proves the OS is running, so checking the agent service and logs is the correct first troubleshooting step.

---

**Q44. Correct: C**

NVML Driver/library version mismatch errors occur when the container's CUDA runtime libraries are incompatible with the host driver version. Driver 550.xx requires CUDA 12.4+, while pytorch:23.10 containers ship with CUDA 12.2. Upgrading to a newer NGC container image with compatible CUDA libraries is the correct solution. NGC versioning (YY.MM format) indicates 24.01+ will support driver 550.xx.

---

