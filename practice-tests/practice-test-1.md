# Practice Test 1: NCP-AIO: NVIDIA AI Operations Professional Certification

- **Questions:** 44
- **Time limit:** 90 minutes
- **Passing score:** 70%%

> Answers are in `answer-key-1.md`. Track results in `score-tracker.md`.

---

**Q1.** An administrator needs to install Run:ai on a Kubernetes cluster managed by Base Command Manager. The cluster has 8 DGX nodes with NVIDIA A100 GPUs. Which sequence of steps correctly deploys Run:ai?

A) Deploy Run:ai control plane first, then install Kubernetes through BCM, configure GPU passthrough, and finally deploy the Run:ai agent
B) Install Slurm via BCM, convert Slurm jobs to Kubernetes pods, deploy Run:ai operator, and configure GPU resource sharing
C) Install Kubernetes via BCM, deploy NVIDIA GPU Operator, install Run:ai control plane on master nodes, install Run:ai cluster component using Helm chart
D) Deploy DOCA Services on DPUs, install Run:ai directly on each DGX node, configure the Run:ai controller, and sync with BCM

---

**Q2.** A data center operations team uses Base Command Manager to deploy a firmware update across 24 compute nodes. The firmware image is staged in BCM's image repository. What is the BEST practice to minimize downtime and ensure rollback capability?

A) Create a node category for the target nodes, drain running jobs, apply the firmware update to the category, verify functionality on one node before applying to all, and maintain the previous firmware image in the repository
B) Use BCM to push the firmware update simultaneously to all 24 nodes during peak hours to ensure consistency across the cluster
C) Schedule the firmware update through Slurm as a maintenance job, reboot all nodes at once, and delete the old firmware image to free repository space
D) Apply the update manually via SSH to each node while monitoring with BCM Base View, then synchronize BCM's inventory database after completion

---

**Q3.** An organization is deploying DOCA Services on BlueField-2 DPUs in a 16-node cluster. Each DPU has dual Arm cores. Which deployment consideration is MOST critical for successful DOCA Services installation?

A) DOCA Services should be installed on the host CPU and communicate with the DPU through PCIe passthrough for optimal performance
B) The DPU firmware version must match the host kernel version exactly, and DOCA Services run in a shared memory space between host and DPU
C) DOCA Services require Kubernetes to be installed on the host system first, then deployed as pods that offload networking to the DPU hardware
D) DOCA Services must run on the DPU Arm cores, not the host x86 CPUs, and require the DOCA SDK and appropriate container runtime installed on the DPU operating system

---

**Q4.** A Base Command Manager administrator needs to organize 40 compute nodes across three workload types: training (16 nodes with A100 80GB), inference (16 nodes with A100 40GB), and interactive development (8 nodes with mixed GPUs). Which BCM feature should be used to manage these groups effectively?

A) User roles and permissions, assigning each workload type to different user groups with separate access controls
B) Slurm partitions defined in BCM's workload manager configuration, creating hardware-based queue separation
C) BCM Base View custom dashboards, creating separate monitoring views for each GPU model and workload pattern
D) Node categories, which allow grouping nodes by hardware characteristics or intended workload, enabling targeted software updates, monitoring, and resource allocation policies

---

**Q5.** During Slurm installation via Base Command Manager on a new 12-node cluster, the administrator must configure the controller node. The cluster will support multi-GPU jobs with MPI and requires job accounting. What is the minimum set of Slurm daemons that must be configured?

A) slurmd on all nodes including the controller, with slurmctld and slurmdbd both running on the same controller node
B) slurmctld on the controller node, slurmd on all compute nodes, and slurmdbd on a database server for accounting
C) slurmctld on the controller node and slurmd on compute nodes only (slurmdbd is optional and not required for basic job accounting)
D) slurmrestd on the controller for API access, slurmd on compute nodes, and slurmctld replicated across three controller nodes for high availability

---

**Q6.** An administrator notices that Base Command Manager Base View shows three compute nodes in a degraded state after a network reconfiguration. The nodes are pingable from the BCM server but show offline in the cluster inventory. What is the MOST likely cause and resolution?

A) The nodes require a firmware update to match the cluster baseline; use BCM to push the latest firmware image to the degraded nodes
B) The BCM agent service on the affected nodes is not running or cannot communicate with the BCM server; restart the BCM agent service on those nodes and verify firewall rules allow BCM communication ports
C) The network reconfiguration changed the nodes' IP addresses; manually update the BCM inventory database with the new IP addresses via SQL
D) The nodes' GPU drivers are out of sync with BCM's expected version; run a software synchronization job from BCM to update the NVIDIA driver stack

---

**Q7.** Before installing Run:ai on a Kubernetes cluster managed by Base Command Manager (8 DGX nodes, NVIDIA A100 GPUs), which prerequisite must be met?

A) Base Command Manager must be upgraded to Enterprise Edition with Run:ai license
B) NVIDIA GPU Operator must be installed and all GPUs must be visible to Kubernetes
C) Each DGX node must have Slurm scheduler removed and disabled
D) BlueField DPU firmware must be at version 24.04 or higher on all nodes

---

**Q8.** A data center team is deploying DOCA Services on BlueField-3 DPUs across 16 nodes. Using Base Command Manager, what is the correct deployment target for DOCA Services installation?

A) The host x86 CPUs on each node where the DPU is installed
B) The Arm cores on the BlueField DPU itself
C) The Base Command Manager server as containerized services
D) The Kubernetes control plane nodes in the cluster

---

**Q9.** An organization is initializing Kubernetes on 12 NVIDIA-certified servers using Base Command Manager. After initialization, 3 nodes show 'Not Ready' status in BCM Base View. Which BCM diagnostic step should the administrator perform FIRST?

A) Reimage all 3 nodes using BCM's software provisioning feature with the latest NVIDIA-certified image
B) SSH into each node and manually restart the kubelet service, then check /var/log/syslog for errors
C) Remove the nodes from the cluster using kubectl delete node and re-add them through BCM initialization
D) Check the node health details in BCM Base View to identify specific component failures or network connectivity issues

---

**Q10.** A site administrator needs to install Slurm workload manager on a new 20-node AI cluster using Base Command Manager. The cluster will run mixed CPU and GPU workloads. What is the PRIMARY role of Base Command Manager in the Slurm installation process?

A) BCM provides automated Slurm installation, configuration templating, and deployment across all cluster nodes
B) BCM monitors Slurm job queue performance but does not participate in the installation process
C) BCM replaces Slurm's slurmctld daemon with BCM's native scheduler for GPU workloads
D) BCM generates the slurm.conf file but requires manual installation of Slurm packages on each node

---

**Q11.** During a firmware update operation using Base Command Manager, an administrator needs to update NVIDIA ConnectX-7 NIC firmware across 24 compute nodes. The cluster is running production workloads. Which BCM approach ensures zero data loss and minimal disruption?

A) Schedule firmware updates during the next maintenance window and update all 24 nodes simultaneously using BCM's bulk update feature
B) Use BCM to drain workloads from nodes in rolling batches, update firmware on drained nodes, verify health, then move to next batch
C) Update firmware on all nodes immediately; BCM automatically migrates running jobs to available nodes during the update
D) Disable network interfaces on all nodes, apply firmware updates via BCM, then re-enable interfaces and restart Slurm

---

**Q12.** An administrator is configuring user access control in Base Command Manager for a multi-tenant AI research cluster. The organization has three research groups that should have isolated access to specific node pools. Which BCM feature should be configured to enforce this access isolation?

A) Create separate BCM user accounts with role-based permissions and assign each group access only to their designated node categories
B) Configure network VLANs for each research group and map them to node pools in BCM network settings
C) Install separate instances of Base Command Manager for each research group on dedicated management servers
D) Use Kubernetes namespaces to isolate workloads; BCM does not provide user-level access control features

---

**Q13.** An AI operations team is deploying DOCA Services on BlueField-3 DPUs in a 16-node BCM-managed cluster. Each DPU has dual Arm cores available. The team needs to ensure DOCA Firewall and DOCA Telemetry services run on the DPU Arm cores while maintaining separation from the host x86 compute workloads. Which deployment approach should the team implement?

A) Install DOCA Services directly on the x86 host OS and configure PCIe passthrough to the DPU
B) Use BCM to provision DOCA Services through the host's Kubernetes cluster with node affinity settings
C) Deploy DOCA Services as containers on the DPU Arm cores using the embedded Linux environment on the BlueField DPU
D) Configure DOCA Services to run as SRIOV virtual functions shared between host and DPU

---

**Q14.** A research facility is installing Run:ai on their NVIDIA GPU cluster managed by Base Command Manager. The cluster has 8 DGX A100 nodes with 8 GPUs each, running Kubernetes 1.28. During the Run:ai installation process, the administrator needs to ensure proper integration with the existing cluster authentication and resource monitoring. What is the FIRST component that must be installed before deploying Run:ai workloads?

A) Run:ai CLI on each DGX node to enable GPU fractional allocation
B) NVIDIA GPU Operator to install and manage GPU drivers and the NVIDIA Container Toolkit
C) Run:ai Administrator UI as a standalone application on the management server
D) Run:ai Scheduler that replaces the default Kubernetes scheduler

---

**Q15.** A Slurm cluster administrator needs to ensure that high-priority AI training jobs from the research team can preempt lower-priority inference jobs, but only after a 2-minute grace period for checkpointing. Which configuration achieves this requirement?

A) Set PreemptMode=REQUEUE with PreemptParameters=grace_time:120 in slurm.conf and configure QOS priority levels
B) Set PreemptMode=REQUEUE in slurm.conf and configure QOS with PreemptExemptTime=120 for inference jobs
C) Set PreemptMode=SUSPEND in slurm.conf and configure the research QOS with GraceTime=120
D) Set PreemptMode=CANCEL and configure PartitionConfig with PreemptDelay=120 for the inference partition

---

**Q16.** An AI data center is experiencing thermal hotspots in racks running 8x NVIDIA H100 SXM5 GPUs per node. Each H100 draws 700W TDP. The facility uses rear-door heat exchangers (RDHx) rated at 35kW per rack. Which solution BEST addresses the cooling challenge?

A) Reduce GPU power limit to 600W per H100 using nvidia-smi and maintain current rack density
B) Increase data center CRAC unit capacity and improve hot aisle/cold aisle containment
C) Upgrade to liquid cooling with direct-to-chip cold plates for GPU and CPU thermal management
D) Redistribute nodes across more racks to reduce power density below 30kW per rack

---

**Q17.** A Kubernetes cluster with NVIDIA GPU Operator installed shows nodes labeled with nvidia.com/gpu.product=NVIDIA-A100-SXM4-40GB but pods requesting nvidia.com/gpu resources remain in Pending state. The nvidia-device-plugin-daemonset pods are running. What is the MOST likely cause?

A) The pods are missing the resources.limits.nvidia.com/gpu specification in their manifest
B) The GPU Operator did not install the NVIDIA driver on worker nodes
C) RuntimeClass is not configured for nvidia container runtime
D) Node Feature Discovery (NFD) is not deployed in the cluster

---

**Q18.** An administrator needs to configure MIG on NVIDIA A100 GPUs to provide isolated GPU instances for multiple data science teams. Which MIG profile configuration maximizes the number of independent instances while providing at least 10GB memory per instance?

A) 4x 2g.20gb profiles per A100-80GB GPU
B) 3x 3g.20gb profiles per A100-40GB GPU
C) 2x 3g.20gb + 1x 1g.5gb profiles per A100-40GB GPU
D) 7x 1g.10gb profiles per A100-40GB GPU

---

**Q19.** A Run:ai administrator needs to ensure that the ML-Research department can use up to 16 GPUs guaranteed, burst to 32 GPUs when available, but never prevent the ML-Production department from accessing their guaranteed 8 GPUs. Which quota configuration achieves this?

A) ML-Research: Guaranteed=16, Limit=32; ML-Production: Guaranteed=8, Preemptible=false
B) ML-Research: GPU Quota=16, Over-Quota=enabled; ML-Production: GPU Quota=8, Over-Quota Priority=High
C) ML-Research: GPU Quota=32, Guaranteed-Quota=16; ML-Production: GPU Quota=8, Priority=1
D) ML-Research: deserved-gpus=16, limit=32; ML-Production: deserved-gpus=8, prevent-preemption=true

---

**Q20.** A Slurm administrator notices nodes transitioning to DRAIN state automatically after jobs complete. Investigation shows "RealMemory < MinMemSize" in the node's Reason field. The nodes have 512GB physical RAM but MinMemSize in slurm.conf is set to 480000 (MB). What is the issue?

A) slurm.conf MinMemSize should be 480 (GB) not 480000 (MB) - unit specification error
B) RealMemory in slurm.conf is configured in MB but should be in GB to match MinMemSize
C) NUMA configuration is splitting memory allocation causing per-socket memory to appear below threshold
D) The slurmd daemon reports available memory excluding kernel/OS overhead, falling below 480000 MB threshold

---

**Q21.** An AI research team reports that their Slurm jobs are being preempted unexpectedly during critical training runs. Investigation reveals jobs from different departments are competing for the same GPU nodes. The administrator needs to prevent preemption for high-priority research jobs while still allowing lower-priority jobs to run when resources are available. Which Slurm configuration approach BEST achieves this requirement?

A) Configure separate partitions for each department and set PriorityType=priority/multifactor with different weights
B) Enable job arrays for all submitted jobs and configure MaxSubmitJobs to limit concurrent submissions per user
C) Set GraceTime=UNLIMITED on high-priority jobs and configure OverSubscribe=FORCE on the partition
D) Create QOS (Quality of Service) entries with PreemptMode=OFF for high-priority jobs and PreemptMode=REQUEUE for lower-priority jobs, then assign QOS to users

---

**Q22.** A data center architect is designing network fabric for a 256-GPU AI training cluster running multi-node distributed workloads with NVIDIA Collective Communications Library (NCCL). The workloads require all-reduce operations across all GPUs with minimal latency. Current options include 100GbE Ethernet with RoCE v2 or InfiniBand HDR (200Gb/s). Power budget allows either option. Which network architecture BEST meets the performance requirements?

A) 100GbE Ethernet with RoCE v2, using a spine-leaf topology with lossless Ethernet (PFC and ECN) configured
B) Hybrid approach: InfiniBand for compute interconnect and Ethernet for storage traffic, with separate fabrics
C) InfiniBand HDR in a fat-tree topology with adaptive routing enabled and all GPUs connected via rail-optimized topology
D) 100GbE Ethernet with standard TCP/IP and jumbo frames (MTU 9000) in a two-tier architecture

---

**Q23.** A Run:ai administrator needs to ensure that a specific research project can burst beyond its allocated GPU quota during off-peak hours when other projects are idle, but must be limited to exactly 8 GPUs during business hours (9 AM - 5 PM). The cluster has 32 GPUs total. Which Run:ai configuration achieves this requirement?

A) Create two separate projects with different quotas and migrate workloads between them based on time of day
B) Configure the project with deserved-gpus=8 and set a node pool affinity that changes based on a cron schedule
C) Set quota=8 GPUs with allow-over-quota=true, then configure time-based scheduling policies in the cluster settings to control burst behavior
D) Set the project quota to 8 GPUs and enable the Over-quota weight parameter to allow opportunistic scheduling during idle periods

---

**Q24.** An administrator is configuring MIG (Multi-Instance GPU) on NVIDIA A100 GPUs to support multiple small inference workloads in a Kubernetes cluster. Each workload requires approximately 10GB of GPU memory. The A100-80GB GPUs should be partitioned to maximize the number of isolated instances while meeting the memory requirement. Which MIG profile configuration should be applied?

A) 2g.20gb profile, creating 4 instances per A100-80GB GPU
B) 1g.10gb profile, creating 7 instances per A100-80GB GPU
C) 3g.40gb profile, creating 2 instances per A100-80GB GPU
D) MIG 1g.5gb profile with two instances allocated per workload to meet the 10GB requirement

---

**Q25.** A data scientist needs to deploy a Llama-2-70B inference service on your NVIDIA AI cluster using NVIDIA Inference Microservices (NIM). The deployment must support auto-scaling based on request load and maintain sub-200ms P95 latency. Which deployment approach BEST meets these requirements?

A) Deploy NIM as a Kubernetes Deployment with HorizontalPodAutoscaler configured to scale based on custom metrics from Triton's prometheus endpoint, using GPU time-slicing for efficient resource sharing
B) Deploy NIM container directly using docker run with --gpus all flag and configure manual container restarts based on CloudWatch metrics
C) Deploy NIM using Slurm with sbatch --gres=gpu:8 and configure a cron job to submit additional jobs when queue depth exceeds threshold
D) Deploy NIM using Run:ai Submit with --gpu-fraction 0.5 flag and enable Run:ai's built-in inference auto-scaling feature

---

**Q26.** Your team is running a multi-node distributed training job using NCCL for gradient synchronization across 4 DGX A100 nodes (32 GPUs total). The job hangs during initialization with 'NCCL WARN Call to connect returned Connection refused'. What is the MOST likely cause?

A) The PyTorch version is incompatible with the CUDA version installed on the system
B) Insufficient CPU memory allocated in the Slurm job script causing NCCL buffer allocation failures
C) InfiniBand fabric is not configured or NCCL_IB_DISABLE=1 is set, forcing NCCL to attempt TCP/IP communication which is being blocked by firewall rules between nodes
D) GPU compute mode is set to EXCLUSIVE_PROCESS instead of DEFAULT mode

---

**Q27.** An administrator needs to pull an optimized TensorFlow container from NGC to run a training job on a cluster without direct internet access. The cluster has a private registry configured. Which command sequence correctly authenticates and pulls the container for offline use?

A) ngc registry image pull nvidia/tensorflow:24.01-tf2-py3 --org nvidia, then docker save to create tar archive for manual transfer to cluster nodes
B) docker pull nvcr.io/nvidia/tensorflow:24.01-tf2-py3 (no authentication required for public NGC containers), then docker push to private registry
C) docker login nvcr.io --username=$oauthtoken --password=<NGC_API_KEY>, then docker pull nvcr.io/nvidia/tensorflow:24.01-tf2-py3, then docker tag and push to private registry
D) kubectl create secret docker-registry ngc-secret --docker-server=nvcr.io --docker-username=$oauthtoken --docker-password=<NGC_API_KEY>, then reference secret in pod spec imagePullSecrets

---

**Q28.** A Run:ai administrator needs to ensure that the ML-Training team can use up to 16 GPUs when resources are available, but must guarantee at least 8 GPUs are always available even when the cluster is fully utilized by other teams. Which Run:ai resource allocation configuration achieves this?

A) Create a project with GPU quota = 16 and over-quota enabled, assign ML-Training team to the project
B) Create a project with deserved GPUs = 8, GPU quota = 16, and over-quota enabled, assign ML-Training team to the project
C) Set node affinity rules to reserve 8 specific GPU nodes for ML-Training project and allow pod scheduling on any node up to 16 GPUs total
D) Configure a Kubernetes ResourceQuota with requests.nvidia.com/gpu: '8' and limits.nvidia.com/gpu: '16' in the ML-Training namespace

---

**Q29.** While troubleshooting a training job that reports 'CUDA out of memory' errors, an engineer runs nvidia-smi on the compute node and sees GPU memory utilization at only 45%. Which tool provides the MOST detailed insight into actual GPU memory allocation patterns to diagnose this issue?

A) dcgmi dmon -e 203,204 to monitor both framebuffer usage and retired pages
B) nvidia-smi --query-compute-apps=pid,process_name,used_memory --format=csv to list per-process allocations
C) nsys profile --gpu-metrics-device=all to capture detailed memory allocation events
D) nvidia-smi dmon -s mu to monitor memory utilization metrics over time

---

**Q30.** A Slurm administrator needs to configure fair-share scheduling to prioritize the Research team's jobs over the Development team when both teams submit jobs simultaneously, but allow Development to use idle Research allocation when Research has no pending jobs. Which Slurm configuration achieves this?

A) Set PriorityType=priority/multifactor, PriorityWeightFairshare=10000, and configure accounts with FairShare=100 for Research, FairShare=50 for Development
B) Create separate partitions for Research (Priority=100) and Development (Priority=50) and enable partition-based preemption
C) Set PriorityType=priority/basic and assign all Research users a higher base priority value using PriorityWeightQOS
D) Configure MaxSubmitJobs=unlimited for Research account and MaxSubmitJobs=10 for Development account

---

**Q31.** A research team is deploying a multi-node distributed training job across 4 DGX nodes using Slurm. The job requires all-reduce communication patterns with NCCL. After submitting with `sbatch --nodes=4 --ntasks-per-node=8 --gres=gpu:8 train.sh`, the job fails with NCCL timeout errors. Running `dcgmi diag -r 1` on each node shows all GPUs are healthy. What is the MOST likely cause?

A) The Slurm partition does not have enough GPU resources available and the job is partially allocated
B) The NCCL_SOCKET_IFNAME environment variable is not set correctly, causing NCCL to use the wrong network interface for inter-node communication
C) The training script is using NCCL_DEBUG=INFO which causes performance degradation and timeouts
D) The DCGM health check level 1 does not test NVLink topology which is required for multi-node jobs

---

**Q32.** An ML engineer needs to deploy a NVIDIA NIM microservice for Llama-3.1-70B inference on a Kubernetes cluster. The cluster has nodes with 4x A100 80GB GPUs. After pulling the NIM container from NGC and creating a deployment with resource requests of `nvidia.com/gpu: 2`, pods remain in Pending state. Running `kubectl describe pod` shows 'Insufficient nvidia.com/gpu'. However, `kubectl get nodes -o json | grep nvidia.com/gpu` shows 4 GPUs available on target nodes. What should be done FIRST?

A) Reduce the GPU request to 1 because Llama-3.1-70B NIM only requires a single A100 80GB GPU for inference
B) Create a RuntimeClass for the NIM container and specify it in the pod spec to enable GPU passthrough
C) Pull the NIM container using a different NGC API key with enterprise registry access permissions
D) Verify the NVIDIA device plugin is running in the kube-system namespace and check its logs for errors

---

**Q33.** A Run:ai administrator needs to configure fair-share resource allocation for three teams: Research (priority 100), Production (priority 200), and Development (priority 50). The cluster has 32 GPUs total. Research submitted jobs requesting 20 GPUs, Production needs 16 GPUs guaranteed, and Development needs 8 GPUs. Which Run:ai configuration achieves the requirement that Production always gets its 16 GPUs while allowing over-quota usage for other teams when resources are idle?

A) Create departments with GPU quotas: Research=20, Production=16, Development=8, and enable over-quota scheduling with allow-over-quota=true on Research and Development departments only
B) Set guaranteed quotas: Production=16 GPUs with deserved-gpus=16, Research and Development with deserved-gpus=0, and configure node-pools to reserve 16 specific GPUs for Production workloads
C) Create projects with GPU limits: Production with gpu-limit=16 and allow-over-quota=false, Research and Development with gpu-limit=20 and gpu-limit=8 respectively, all with allow-over-quota=true
D) Configure a single department with 32 GPUs and use workload priorities (100, 200, 50) to ensure Production jobs preempt lower-priority jobs when needed

---

**Q34.** A data scientist is running nvidia-smi on a DGX node and sees GPU 3 showing 'Volatile GPU-Util: 95%' but 'Memory-Usage: 1204MiB / 81920MiB'. A training job is running on GPU 3 but training speed is 10x slower than expected. Running `dcgmi dmon -e 203,204 -i 3` shows PCIe TX/RX throughput near zero. What is the issue?

A) The training job is CPU-bound and waiting on data preprocessing, so GPU compute is high but memory and PCIe transfers are minimal
B) GPU 3 has entered a thermal throttling state and reduced clock speeds, causing high utilization percentage but low absolute throughput
C) The NVIDIA driver is outdated and causing incorrect nvidia-smi utilization reporting; actual GPU usage is much lower
D) The training script is running in a tight compute loop without transferring data to/from GPU memory, indicating a code bug where data is not being loaded onto the GPU

---

**Q35.** A deep learning team reports that their multi-node PyTorch training job is experiencing severe performance degradation on an HGX A100 system. The job completes successfully on a single node but takes 4x longer than expected when scaled to 4 nodes. You check NCCL_DEBUG=INFO logs and see repeated 'Call to ibv_create_qp failed' errors. Which action will MOST effectively resolve this issue?

A) Increase the Docker container's shared memory size using --shm-size=16g and restart the training job
B) Restart the Fabric Manager service on all nodes using 'systemctl restart nvidia-fabricmanager' and verify NVSwitch connectivity with 'nvidia-smi nvlink --status'
C) Rebuild the NGC PyTorch container with NCCL_P2P_DISABLE=1 environment variable to force socket-based communication
D) Verify that GPUDirect RDMA is enabled by checking 'cat /proc/driver/nvidia/gpudirect_rdma/version' returns a valid version, then ensure the nv_peer_mem kernel module is loaded with 'lsmod | grep nv_peer_mem'

---

**Q36.** During Base Command Manager cluster provisioning, 8 new DGX A100 nodes remain stuck in 'Provisioning' state for over 30 minutes. The BCM UI shows 'Image sync in progress' but never completes. You SSH to the BCM manager node and run 'bcm images list' which shows the correct OS image. Which troubleshooting step will BEST identify the root cause?

A) Verify network connectivity by pinging each DGX node from the BCM manager and checking for packet loss exceeding 5%
B) Restart all DGX nodes using IPMI to force a fresh PXE boot and re-initiate the provisioning workflow
C) Check disk space on the BCM manager node with 'df -h /var/lib/bcm' to ensure at least 500GB is available for image caching
D) Check the BCM provisioning daemon logs with 'journalctl -u bcm-provisioning -n 200 --no-pager' and look for HTTP 503 or connection timeout errors to the image repository

---

**Q37.** A data science team reports that their NGC TensorFlow container fails to start on a newly installed DGX system with the error: 'docker: Error response from daemon: failed to create shim task: OCI runtime create failed: runc did not terminate successfully: nvidia-container-cli: initialization error'. The host has driver version 535.129.03 installed. Which solution will resolve this issue?

A) Downgrade the NVIDIA driver to version 470.x to match the container's expected CUDA version
B) Add the --runtime=nvidia flag to the docker run command and increase container memory limits to 32GB
C) Install or update nvidia-container-toolkit to version 1.14.0 or newer using 'apt-get install -y nvidia-container-toolkit && systemctl restart docker', then verify with 'nvidia-container-cli info'
D) Pull a newer version of the NGC TensorFlow container that includes the updated CUDA libraries

---

**Q38.** An AI training cluster experiences intermittent NCCL 'NCCL WARN Connect to [IP]:6379 failed : Connection refused' errors during multi-node jobs. Single-node jobs complete successfully. You verify that the Slurm scheduler is allocating resources correctly and all nodes can ping each other. NCCL_DEBUG=INFO shows the errors occur only on specific node pairs. What is the MOST likely cause?

A) GPUDirect Storage is not configured, forcing NCCL to use fallback network paths with insufficient bandwidth
B) The NCCL version compiled into the NGC container is incompatible with the installed CUDA driver version
C) The firewall on compute nodes is blocking NCCL's dynamic port range (typically 60000-61000); configure firewalld or iptables to allow TCP traffic on these ports between compute nodes
D) Slurm is not properly pinning NUMA domains, causing cross-socket memory access penalties that manifest as connection timeouts

---

**Q39.** A research team stores training datasets on a shared NFS mount and reports that data loading is the bottleneck in their training pipeline—GPU utilization stays below 30% while storage I/O wait is high. They are training a computer vision model with small files (average 200KB per image) on a DGX A100 system. Which optimization will provide the GREATEST performance improvement?

A) Enable GPUDirect Storage (GDS) to allow direct GPU access to the NFS storage, bypassing the CPU for I/O operations
B) Convert the dataset to TFRecord or WebDataset format to reduce the number of file operations and enable sequential reading patterns, then store on local NVMe or a parallel filesystem like BeeGFS
C) Increase the NFS mount options to use rsize=1048576,wsize=1048576 for larger read/write buffer sizes
D) Configure NCCL to use GPUDirect RDMA to accelerate data movement from storage to GPU memory

---

**Q40.** After upgrading Base Command Manager to version 4.1, several admin users report they can no longer provision new nodes, receiving an 'Access Denied: Insufficient permissions' error in the UI. The BCM system administrator can provision nodes successfully. Checking 'bcm users list' shows all users are still present with 'active' status. What is the MOST likely cause and solution?

A) The upgrade changed the default authentication backend from LDAP to local database; reconfigure LDAP integration in BCM settings
B) The BCM upgrade reset role-based access control (RBAC) policies to defaults; re-assign the 'Provisioning Administrator' role to affected users using 'bcm user update <username> --role provisioning-admin' or through the BCM UI under Access Control
C) User sessions were invalidated during the upgrade; have users log out completely, clear browser cache, and log back in to obtain new session tokens
D) The upgrade introduced a new license requirement for multi-user provisioning; contact NVIDIA support to update the BCM license file

---

**Q41.** A data scientist reports that their PyTorch training job using NCCL fails with "NET/Socket : Connect to <peer_ip> failed : Connection refused" errors when scaling to 8 nodes. The job runs successfully on a single node. NCCL_DEBUG=INFO shows successful GPU detection but socket connection failures during ring initialization. Network connectivity tests show all nodes can ping each other. What is the MOST likely cause?

A) The NCCL_SOCKET_IFNAME environment variable is not set, causing NCCL to attempt communication over the management network instead of the high-speed InfiniBand fabric
B) The fabric manager service is not running on one or more nodes, preventing NVSwitch communication between GPUs
C) The nvidia-container-toolkit is misconfigured and not exposing GPU devices to the container runtime
D) The NGC container version is incompatible with the installed NVIDIA driver version

---

**Q42.** An administrator notices that AI training jobs reading datasets from a shared NFS mount achieve only 800 MB/s aggregate throughput across 4 nodes, while local NVMe storage achieves 24 GB/s per node. The NFS server has 100 GbE connectivity and shows low CPU utilization. GPUDirect Storage (GDS) is installed but training scripts use standard Python file I/O. Which action will provide the GREATEST performance improvement?

A) Modify training scripts to use cuFile API calls to enable GPUDirect Storage, bypassing CPU and system memory for direct GPU-to-storage transfers
B) Increase the NFS rsize and wsize mount options from default 1MB to 8MB to reduce protocol overhead
C) Configure GPUDirect RDMA to reduce latency between storage network adapters and GPUs
D) Replace NFS with a distributed filesystem like Lustre or BeeGFS optimized for parallel workloads

---

**Q43.** Base Command Manager shows a compute node stuck in "Provisioning" state for 45 minutes. The node PXE booted successfully and the installation log shows the OS image was deployed. The BCM daemon log on the management node shows repeated "Waiting for node registration" messages. Manual SSH to the node succeeds using root credentials. What should the administrator verify FIRST?

A) Verify that the node's IPMI interface is accessible from the BCM management node for out-of-band monitoring
B) Confirm that the NGC container registry credentials are correctly configured in Base Command Manager settings
C) Check if the bcm-node-agent service is running on the compute node and review its logs for registration failures or network connectivity issues to the BCM management node
D) Restart the fabric manager service on the compute node to re-establish GPU fabric connectivity

---

**Q44.** A containerized inference service fails to start with the error "Failed to initialize NVML: Driver/library version mismatch" after upgrading NVIDIA drivers on the host from 525.xx to 550.xx. The container is based on nvcr.io/nvidia/pytorch:23.10-py3. Docker runtime logs show nvidia-container-cli successfully detected the new driver version. Which solution will resolve this issue?

A) Restart the Docker daemon to reload the nvidia-container-toolkit with the updated driver library paths
B) Downgrade the host driver back to 525.xx to match the container's expected driver version
C) Pull and deploy a newer NGC container image (e.g., pytorch:24.01-py3 or later) that includes CUDA libraries compatible with driver 550.xx
D) Set the NVIDIA_DISABLE_REQUIRE environment variable to true in the container to bypass driver version checks

---

