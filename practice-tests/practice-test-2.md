# Practice Test 2: NCP-AIO: NVIDIA AI Operations Professional Certification

- **Questions:** 44
- **Time limit:** 90 minutes
- **Passing score:** 70%%

> Answers are in `answer-key-2.md`. Track results in `score-tracker.md`.

---

**Q1.** A data center team needs to deploy DOCA Services on BlueField-2 DPUs to enable storage acceleration and network security features. The DPUs are installed in host servers, and the team wants to use Base Command Manager to orchestrate the installation. What is the FIRST step required before DOCA Services can be deployed to the DPU Arm cores?

A) Install the NVIDIA Network Operator on the host system and enable SR-IOV on all network interfaces
B) Deploy the DOCA container runtime on the host x86 system and configure pass-through access to the DPU
C) Update the host BIOS to enable IOMMU and configure the DPU in embedded mode for host-managed deployment
D) Configure the DPU in separated mode and ensure the Arm cores have network connectivity to Base Command Manager

---

**Q2.** An organization is initializing a new Kubernetes cluster on NVIDIA DGX systems using Base Command Manager. The infrastructure team has completed hardware provisioning and now needs to bootstrap Kubernetes across 12 compute nodes. Which BCM workflow correctly sequences the Kubernetes initialization process?

A) Create a node category for Kubernetes workers, assign nodes to the category, provision the OS image with container runtime dependencies, then use BCM's Kubernetes deployment template to initialize the control plane and join worker nodes
B) Deploy the NVIDIA GPU Operator first to ensure driver compatibility, then manually run kubeadm init on the master node and distribute join tokens through BCM's job submission interface
C) Use BCM Mission Control to push a pre-configured Kubernetes image to all nodes simultaneously, designate one node as control plane through the web UI, then enable auto-join for remaining nodes
D) Configure Slurm workload manager on all nodes first to handle resource allocation, then install Kubernetes components on top of Slurm using BCM's software stack layering feature

---

**Q3.** A cluster administrator needs to update NVIDIA GPU driver versions across 50 compute nodes to address a critical security vulnerability (CVE-2025-XXXX). The cluster is running mixed workloads with active jobs that cannot be interrupted. Using Base Command Manager, what is the BEST approach to apply this security patch while minimizing workload disruption?

A) Create a maintenance window in BCM, configure a rolling update policy with drain-and-update strategy limited to 5 nodes at a time, and let BCM coordinate job migration before patching each batch
B) Use BCM to push the updated driver package to all nodes simultaneously during off-peak hours, then schedule a cluster-wide reboot for the following weekend
C) Manually update the driver on a single node to verify compatibility, create a golden image with the new driver, then use BCM's image sync feature to force-update all nodes in parallel
D) Configure BCM's automatic patch deployment to update nodes as jobs complete, with a timeout set to force updates after 72 hours regardless of job status

---

**Q4.** An AI infrastructure team is installing Run:ai on an existing Kubernetes cluster managed by Base Command Manager. The cluster has 8 DGX A100 nodes with 8 GPUs each. After installing the Run:ai operator and control plane, GPU resources are not appearing in the Run:ai dashboard. Investigation shows that the NVIDIA device plugin is installed but GPUs are not being advertised to Kubernetes. What is the MOST likely root cause and resolution?

A) The container runtime is not configured with the nvidia-container-runtime; update the containerd or Docker configuration to use nvidia as the default runtime and restart the runtime service on all GPU nodes
B) The NVIDIA GPU Operator was installed before Run:ai, causing a conflict between device plugin versions; uninstall the GPU Operator components and allow Run:ai to manage the device plugin installation
C) Run:ai requires GPU nodes to have the 'nvidia.com/gpu' label and the time-slicing configuration has not been applied; label the nodes and configure time-slicing quotas in the Run:ai UI
D) Base Command Manager's resource management conflicts with Run:ai's scheduler; disable BCM's GPU tracking feature in Mission Control and restart the Kubernetes kubelet service on all nodes

---

**Q5.** A research organization needs to configure user access controls in Base Command Manager for a new project team of 15 researchers who will submit AI training jobs. The team should be able to view cluster status, submit jobs to specific GPU partitions, and monitor their own jobs, but should NOT be able to modify system configurations or view other teams' job details. Which BCM configuration achieves these requirements?

A) Assign all researchers the built-in 'User' role, enable partition-based access control in Slurm configuration, and use BCM's job isolation feature to prevent cross-team visibility
B) Create individual user accounts with 'Operator' role permissions, configure ACLs in the BCM database to restrict configuration access, and use Kubernetes namespaces to isolate team workloads
C) Use BCM's LDAP integration to sync the research team, assign 'Administrator' role with restricted scope to the GPU nodes only, and enable audit logging to track configuration changes
D) Create a custom role with permissions for job submission, job monitoring (own jobs only), and cluster view; create a user group for the research team, assign the custom role to the group, and map the group to designated GPU partitions using resource policies

---

**Q6.** An enterprise is deploying Slurm as the workload manager for their NVIDIA AI cluster using Base Command Manager. The Slurm installation must support GPU scheduling with GRES (Generic Resource Scheduling), integrate with BCM for centralized monitoring, and use a dedicated management node separate from the compute nodes. During the installation process using BCM's Slurm deployment template, what configuration is REQUIRED to enable BCM to monitor Slurm job metrics and resource utilization?

A) Install the BCM agent on all Slurm compute nodes and configure the slurm.conf file to forward accounting data to the BCM database using the AccountingStorageType=accounting_storage/bcm plugin
B) Enable the Slurm REST API on the slurmctld node and configure BCM with the API endpoint and authentication credentials so BCM can query job and resource data
C) Configure Slurm to export metrics to Prometheus, then configure BCM to scrape the Prometheus endpoint for Slurm-specific metrics and job telemetry
D) Deploy the NVIDIA Data Center GPU Manager (DCGM) exporter on compute nodes and configure Slurm's epilog scripts to send job completion data to BCM's Mission Control API

---

**Q7.** An administrator needs to deploy DOCA Services on a cluster with BlueField-2 DPUs. The DPUs are already installed and detected by BCM, but DOCA Services installation fails with a provisioning error. Which step is MOST likely missing?

A) DOCA Services require Kubernetes to be installed on the host x86 nodes first
B) The administrator must manually SSH into each DPU Arm core and run the DOCA installation script
C) BCM Base View must be in Expert Mode to enable DPU provisioning capabilities
D) The DPU firmware must be updated to a DOCA-compatible version through BCM before installing services

---

**Q8.** A site is initializing a new NVIDIA AI cluster with BCM. The cluster has 64 DGX H100 nodes across 4 racks with InfiniBand networking. During BCM cluster initialization, which configuration task must be completed BEFORE compute nodes can be added to workload categories?

A) Define network topology in BCM including switch connections and InfiniBand fabric layout
B) Create admin and user accounts with role-based permissions in BCM
C) Install and configure the Slurm or Kubernetes workload manager on all compute nodes
D) Apply a synchronized software image to all nodes using BCM's image management

---

**Q9.** During Run:ai installation on a Kubernetes cluster managed by BCM, the installation fails with an error indicating insufficient cluster permissions. The administrator used the default Kubernetes service account. What is the BEST solution?

A) Disable Kubernetes RBAC temporarily, complete the Run:ai installation, then re-enable RBAC
B) Install Run:ai components individually using kubectl apply instead of the Helm chart
C) Configure BCM to bypass Kubernetes authentication for Run:ai namespace operations
D) Create a dedicated service account with cluster-admin role binding and use it for Run:ai installation

---

**Q10.** A cluster administrator needs to update GPU driver versions across 128 compute nodes. The cluster is running active workloads on Slurm. Using BCM's image management, which approach minimizes workload disruption while ensuring driver consistency?

A) Create a rolling update schedule in BCM that drains nodes one rack at a time, applies the new image, and returns nodes to service
B) Use BCM to create a new software image with updated drivers, then apply it to all nodes simultaneously during a maintenance window
C) Manually update drivers on a test node, create an image from that node in BCM, then synchronize to remaining nodes
D) Configure BCM to apply the driver update only to nodes in IDLE state, waiting for all nodes to become idle naturally

---

**Q11.** An organization is installing Slurm on an NVIDIA cluster using BCM. The cluster has heterogeneous node types: 32 DGX A100 nodes and 32 DGX H100 nodes. How should the administrator configure Slurm partitions through BCM to optimize job placement?

A) Create a single partition containing all nodes and rely on Slurm's automatic GPU detection for job placement
B) Configure all nodes in one partition but use BCM categories to limit A100 nodes to training jobs and H100 nodes to inference jobs
C) Create separate partitions for A100 and H100 nodes, configure node features in BCM, and allow users to request specific GPU types
D) Install separate Slurm instances for each node type and use BCM to route jobs between instances

---

**Q12.** After installing Kubernetes on NVIDIA hosts using BCM, the administrator notices that GPU device plugin pods are in CrashLoopBackOff state. BCM shows all nodes healthy with GPUs detected. What is the MOST likely cause?

A) The NVIDIA GPU Operator was not installed; the device plugin requires the operator framework to manage GPU resources
B) Docker runtime is configured instead of containerd; NVIDIA device plugins require containerd for GPU passthrough
C) BCM's Kubernetes integration is using an incompatible API version for the installed Kubernetes release
D) The nodes need to be rebooted after Kubernetes installation for GPU PCIe enumeration to complete

---

**Q13.** An AI operations team is preparing to deploy DOCA Services on BlueField-2 DPUs across a 64-node cluster. The DPUs will handle infrastructure services including DOCA Firewall and DOCA Telemetry Service. Base Command Manager shows all DPUs are detected but not yet initialized with the DOCA runtime. Which sequence of steps must be performed to properly deploy DOCA Services on the DPU Arm cores?

A) Use BCM to provision the BFB (BlueField Boot Image) to each DPU, reboot the DPUs to load the DOCA-enabled OS, then deploy DOCA Services as containers on the DPU Arm cores using the DOCA deployment tools
B) Flash the DPU firmware using BCM's firmware update mechanism, then deploy DOCA Services container images directly to the host x86 nodes, and configure BCM to route traffic to the DPUs
C) Install DOCA Services on the host x86 CPU first, then use BCM's node categorization feature to tag DPU-enabled nodes, and enable automatic DPU offload in the BCM workload manager settings
D) Deploy a Kubernetes cluster on the host nodes using BCM, label the DPU nodes with doca-runtime=enabled, then use kubectl to deploy DOCA Services as DaemonSets across all DPU nodes

---

**Q14.** A data center is installing Run:ai on a new NVIDIA DGX SuperPOD managed by Base Command Manager. The infrastructure uses Kubernetes 1.28 with NVIDIA GPU Operator already deployed. The Run:ai administrator needs to ensure proper integration with BCM for unified monitoring and resource reporting. During Run:ai installation, which configuration step is required to maintain visibility of Run:ai workloads within Base Command Manager's monitoring dashboard?

A) Install the Run:ai cluster component with the --bcm-integration flag enabled, then configure the BCM API endpoint and authentication token in the Run:ai values.yaml file before helm deployment
B) Deploy Run:ai with Prometheus metrics enabled and configure BCM's monitoring service to scrape the Run:ai metrics endpoint, ensuring GPU utilization and job metadata are exported in a BCM-compatible format
C) Use BCM to create a dedicated node category for Run:ai workloads, install Run:ai using the BCM software deployment feature, and enable the Run:ai plugin in BCM Mission Control settings
D) Configure Run:ai to use Slurm as its underlying scheduler instead of native Kubernetes scheduling, then register the Slurm cluster with BCM's workload manager integration to enable unified monitoring

---

**Q15.** A data center architect is designing a new AI cluster with 256 NVIDIA H100 GPUs. The cluster will run large-scale distributed training jobs requiring all-reduce operations across all GPUs. Network latency must be minimized. Which fabric architecture BEST meets these requirements?

A) 400GbE Ethernet with RDMA over Converged Ethernet (RoCE) v2 in a leaf-spine topology
B) 100GbE Ethernet with TCP/IP and NVLink switches for GPU-to-GPU communication
C) HDR InfiniBand (200Gb/s) with a single-tier switched architecture
D) NDR InfiniBand (400Gb/s) in a fat-tree topology with rail-optimized routing

---

**Q16.** An administrator needs to configure MIG on an NVIDIA A100 GPU to provide isolated GPU instances for multiple tenants. The requirement is to create seven equal GPU instances to maximize tenant isolation while maintaining compute capability. Which MIG profile configuration should be used?

A) Seven 1g.10gb instances (1 GPU slice, 10GB memory each)
B) Seven 1g.5gb instances (1 GPU slice, 5GB memory each)
C) One 3g.20gb instance and four 1g.5gb instances for mixed workloads
D) Seven 2g.10gb instances (2 GPU slices, 10GB memory each)

---

**Q17.** A Slurm cluster administrator observes that high-priority jobs are experiencing longer queue times than expected despite having PreemptMode=REQUEUE configured. Investigation shows that low-priority jobs with long runtimes are occupying all nodes. Which configuration change will MOST effectively reduce queue times for high-priority jobs?

A) Set PreemptMode=SUSPEND and configure PreemptExemptTime to allow grace periods for low-priority jobs
B) Increase the PriorityWeightQOS parameter and enable bf_max_job_start in the scheduler
C) Configure MaxTime limits on low-priority partitions and set PreemptType=preempt/qos with PreemptMode=CANCEL
D) Enable gang scheduling with OverSubscribe=EXCLUSIVE on high-priority partitions

---

**Q18.** A Run:ai administrator needs to configure a department that has purchased 16 GPUs but wants to allow dynamic bursting up to 32 GPUs when cluster resources are idle. The department must be guaranteed their 16 GPUs even during peak usage. Which quota configuration achieves this?

A) Set GPU quota to 16 and enable over-quota scheduling with a limit of 32 GPUs in the project settings
B) Set GPU quota to 32 and configure fairness policies to guarantee 16 GPUs minimum
C) Configure GPU quota to 16, set over-quota to true, and configure a node-pool with 32 total GPUs
D) Configure deserved GPUs to 16 and set over-quota to true with no GPU limit specified

---

**Q19.** An administrator is troubleshooting a Kubernetes cluster where GPU workloads are failing to schedule despite available GPU nodes. Running 'kubectl describe node' shows '0' allocatable GPUs even though 'nvidia-smi' on the nodes reports 8 A100 GPUs per node as healthy. Which component failure is MOST likely causing this issue?

A) The GPU Operator's driver validation container failed and GPUs are not initialized
B) The kubelet on GPU nodes is not configured with the --feature-gates=DevicePlugins=true flag
C) The NVIDIA Device Plugin daemonset is not running or has crashed on the GPU nodes
D) MIG mode is enabled on the GPUs but the MIG strategy is not configured in the GPU Operator

---

**Q20.** A data center operations team is planning power and cooling for a new rack containing 8 NVIDIA HGX H100 8-GPU systems. Each HGX H100 system has a maximum power draw of 10.2 kW. The facility requires 20% overhead for power distribution and N+1 cooling redundancy. Cooling efficiency is 1.15 kW cooling per kW IT load. What is the MINIMUM total facility power capacity required for this rack?

A) 94.4 kW (IT load only, no overhead)
B) 113.3 kW (IT load + 20% power distribution overhead)
C) 130.3 kW (IT load + 20% overhead + cooling at 1.15 ratio)
D) 156.3 kW (IT load + 20% overhead + cooling + N+1 redundancy)

---

**Q21.** An administrator needs to configure a Slurm partition to ensure that high-priority research jobs can preempt lower-priority training jobs, but only if the research jobs have been queued for more than 5 minutes. The preempted jobs should be requeued automatically. Which combination of Slurm configuration parameters achieves this requirement?

A) Set PreemptMode=REQUEUE in slurm.conf, configure two QOS levels with PreemptMode=CLUSTER for research QOS, and set GraceTime=300 in the research QOS definition
B) Set PreemptType=preempt/qos and PreemptMode=REQUEUE in slurm.conf, create a high-priority QOS for research with Preempt=low-priority-qos, and set PreemptExemptTime=00:05:00 in the preemptable QOS
C) Configure PriorityType=priority/multifactor in slurm.conf, set different PriorityTier values for each QOS, and enable PreemptMode=SUSPEND with a 300-second delay
D) Set PreemptType=preempt/partition_prio in slurm.conf, assign different Priority values to partitions, and configure OverSubscribe=FORCE with a 5-minute threshold

---

**Q22.** A data center architect is designing the network fabric for a 256-GPU H100 AI cluster that will run large-scale distributed training workloads with all-reduce collective operations. The cluster requires rail-optimized topology for maximum bisection bandwidth. Assuming each compute node has 8 H100 GPUs, which InfiniBand configuration provides the optimal fabric architecture?

A) 2-tier fat-tree topology using NDR200 (200 Gb/s) switches with 8:1 oversubscription ratio, connecting each node with dual-port HDR100 adapters
B) 3-tier Clos topology using NDR400 (400 Gb/s) leaf and spine switches with 1:1 oversubscription, connecting each node with single-port NDR200 adapters per 4-GPU rail
C) Rail-optimized topology with 8 separate NDR switches (one per GPU rail across all nodes), each node connected via 8x single-port NDR200 adapters (one per GPU), utilizing GPU-direct RDMA
D) Single-tier topology using 4x NDR400 switches in a mesh configuration with each 8-GPU node connected via a single NDR800 adapter using adaptive routing

---

**Q23.** An administrator managing a Run:ai cluster needs to implement a scheduling policy where Project A can use up to 16 GPUs with guaranteed access to 8 GPUs, while allowing Project B to borrow unused GPUs from Project A when idle, but Project A workloads must immediately reclaim resources when submitted. Which Run:ai configuration implements this policy?

A) Configure Project A with deserved-gpus=8 and limit-gpus=16. Configure Project B with deserved-gpus=0 and allow-preemption=true. Enable node-pools with bin-packing scheduler and set Project A priority higher than Project B.
B) Set Project A with gpu-quota=8 and gpu-limit=16. Set Project B with gpu-quota=0 and enable reclaim-resources=true. Configure interactivity mode for Project A and batch mode for Project B with preemption enabled globally.
C) Set Project A quota to 16 GPUs with guaranteed-quota at 8 GPUs and over-quota enabled. Set Project B quota to 8 GPUs with over-quota enabled and allow-preemption disabled. Configure cluster-wide preemption policy to favor guaranteed quota.
D) Configure Project A with guaranteed=8 and max=16 in the project YAML. Set Project B with guaranteed=0 and configure it as a tenant with lower priority. Enable fairness scheduling and set Project A as non-preemptible with immediate reclaim policy.

---

**Q24.** A Kubernetes administrator needs to enable MIG partitioning on A100-80GB GPUs to allow multiple AI workloads to share GPU resources. The cluster should support workloads requesting 1g.10gb, 2g.20gb, and 3g.40gb MIG profiles simultaneously on the same physical GPU. Which MIG configuration strategy and GPU Operator settings are required?

A) Configure MIG strategy 'mixed' in GPU Operator, create MIG devices using nvidia-smi with profiles 1g.10gb, 2g.20gb, 3g.40gb, then label nodes with nvidia.com/mig.config=all-balanced and deploy workloads requesting nvidia.com/mig-1g.10gb resources
B) Set MIG mode to enabled on A100 GPUs, configure GPU Operator with mig.strategy=single, create ConfigMap defining MIG profiles, apply node labels nvidia.com/mig.config=custom-config, and use nvidia.com/gpu resource requests with MIG profile annotations
C) Configure A100 GPUs in MIG mode, set GPU Operator migManager.strategy=mixed, create a MIG partitioning config (e.g., balanced profile with 1x3g.40gb + 2x2g.20gb + 2x1g.10gb totaling 7 instances), label nodes with nvidia.com/mig.config=<config-name>, and request resources as nvidia.com/mig-3g.40gb in pod specs
D) Enable MIG on physical GPUs using nvidia-smi, configure GPU Operator with mig.strategy=mixed, apply node label nvidia.com/mig.config specifying desired profile mix (e.g., all-1g.10gb, all-2g.20gb), and configure device plugin to expose MIG devices as nvidia.com/mig-<profile> resources

---

**Q25.** A data science team reports that their multi-node NCCL training job running across 4 DGX nodes consistently hangs during the allreduce operation after 2-3 epochs. You run 'dcgmi diag -r 3' and observe intermittent PCIe link width degradation on GPU 2 of node dgx-03. The Slurm job was submitted with '--ntasks-per-node=8 --gpus-per-task=1'. Which action will MOST effectively resolve this issue?

A) Set NCCL_P2P_DISABLE=1 and NCCL_SHM_DISABLE=1 in the job environment to force network-based communication bypassing the degraded PCIe link
B) Modify the Slurm job script to use '--exclusive --ntasks-per-node=7' to avoid scheduling tasks on the problematic GPU while keeping the node in service
C) Increase NCCL_SOCKET_NTHREADS to 8 and set NCCL_NSOCKS_PERTHREAD=4 to provide additional bandwidth for the allreduce collective operation
D) Drain node dgx-03 with 'scontrol update NodeName=dgx-03 State=DRAIN Reason="PCIe degradation GPU2"', physically reseat the affected GPU or check PCIe riser card, then run hardware diagnostics before returning to service

---

**Q26.** Your organization uses Run:ai to manage GPU resources across three departments. The ML Research team (department-a) has a guaranteed quota of 16 GPUs but frequently submits jobs requesting 32 GPUs during off-peak hours. The Platform team wants to allow over-quota usage when resources are idle but ensure department-b's guaranteed 8 GPUs are immediately available when needed. Which Run:ai configuration achieves this?

A) Create a node-pool for department-b with node affinity rules and set department-a's jobs to use runai submit --preemptible flag when requesting more than 16 GPUs
B) Configure department-a with deserved-gpus=16 and allow-over-quota=true, set department-b with deserved-gpus=8 and over-quota-priority=high, then enable the fairness algorithm globally
C) Set department-a GPU limit to 32 during off-peak using runai update project department-a --gpu-over-quota-weight=0.5, then schedule a cron job to reset to 16 during business hours
D) Set department-a deserved-gpus=16 with over-quota-priority=1 and department-b deserved-gpus=8 with over-quota-priority=0, enabling preemption on department-a's project

---

**Q27.** You are deploying Triton Inference Server on Kubernetes to serve 3 different models: model-a (ONNX, 2GB), model-b (TensorRT, 8GB), and model-c (PyTorch, 12GB). The cluster has nodes with A100-40GB and A100-80GB GPUs. Model-c requires CUDA 12.2 features, while model-a and model-b run on CUDA 11.8. To minimize GPU memory waste while ensuring all models have dedicated GPU access, which deployment strategy is BEST?

A) Deploy model-a and model-b together in one Triton pod on an A100-40GB node with CUDA 11.8, and deploy model-c in a separate pod on an A100-80GB node with CUDA 12.2, both using whole GPU allocation
B) Deploy 3 separate Triton pods each with nodeSelector for A100-80GB nodes, setting model-a and model-b pods to request 0.25 GPU using MIG (3g.20gb profile) and model-c pod to request 1 whole GPU with CUDA 12.2 container image
C) Deploy a single Triton pod on an A100-80GB node loading all 3 models in the model repository, using Triton's dynamic batching to share the GPU, and set instance_group count=2 for each model
D) Deploy model-c on an A100-80GB node with CUDA 12.2, and deploy model-a and model-b in separate Triton pods on A100-40GB nodes with CUDA 11.8, using Kubernetes GPU time-slicing to allow both pods to share one A100-40GB GPU

---

**Q28.** A researcher submits a Slurm job using 'sbatch --partition=gpu --gres=gpu:4 --nodes=2 train.sh' that launches a PyTorch DistributedDataParallel training job. The job starts but only utilizes GPUs on the first allocated node. Checking the Slurm output shows both nodes allocated (node-[05-06]), and 'srun --jobid=12345 hostname' returns both hostnames. The train.sh script uses 'torchrun --nproc_per_node=4 train.py'. What is the MOST likely cause?

A) The --gres=gpu:4 flag allocates 4 GPUs total across both nodes (2 per node), but torchrun --nproc_per_node=4 attempts to spawn 4 processes per node, causing processes on node-06 to fail without allocated GPUs
B) The torchrun command must be launched with srun to distribute across Slurm-allocated nodes; using torchrun directly only launches processes on the node executing train.sh (the first allocated node)
C) The PyTorch NCCL backend requires NCCL_SOCKET_IFNAME to be set to the high-speed network interface (e.g., ib0) in multi-node jobs; without this, NCCL initialization on node-06 times out
D) Slurm --nodes=2 allocates nodes but --ntasks was not specified, defaulting to 1 task total; this single task runs on node-05 only, and torchrun spawns local processes unaware of the second node allocation

---

**Q29.** Your team is deploying NVIDIA NIM microservices for Llama-2-70B from NGC on a Kubernetes cluster. After pulling the NIM container and creating a deployment with 'resources.limits.nvidia.com/gpu: 2', the pod remains in Pending state. Running 'kubectl describe pod' shows 'insufficient nvidia.com/gpu'. You verify 4 A100-80GB GPUs are available on the node using 'nvidia-smi'. Which configuration issue is MOST likely preventing the pod from scheduling?

A) The NVIDIA device plugin is not installed or not running on the node; install it using 'kubectl apply -f https://raw.githubusercontent.com/NVIDIA/k8s-device-plugin/v0.14.0/nvidia-device-plugin.yml' to expose GPUs as schedulable resources
B) NIM containers require privileged security context to access GPU hardware; add 'securityContext: privileged: true' to the pod spec
C) The NGC NIM container requires an NGC API key mounted as a secret; without valid authentication, Kubernetes refuses to schedule pods using NGC private registry images
D) The resource limit key should be 'nvidia.com/gpu: 2' not 'resources.limits.nvidia.com/gpu: 2'; the full path is only used in the pod spec YAML structure, not as a flat key

---

**Q30.** You need to run a multi-node benchmark using NCCL tests (nccl-tests/build/all_reduce_perf) across 8 DGX A100 nodes with 8 GPUs each to validate InfiniBand fabric performance before deploying production training workloads. The Slurm cluster uses 'gpu' partition with nodes dgx-[01-16]. Which sbatch script configuration will correctly execute the all_reduce_perf test using all 64 GPUs with proper MPI and NCCL settings?

A) #!/bin/bash
#SBATCH --partition=gpu
#SBATCH --nodes=8
#SBATCH --ntasks=64
#SBATCH --gres=gpu:8
#SBATCH --exclusive

export NCCL_DEBUG=INFO
mpirun -np 64 --map-by ppr:8:node --bind-to none ./all_reduce_perf -b 8 -e 8G -f 2 -g 1
B) #!/bin/bash
#SBATCH --partition=gpu
#SBATCH --nodes=8
#SBATCH --ntasks-per-node=8
#SBATCH --gpus-per-task=1
#SBATCH --exclusive

srun --mpi=pmix ./all_reduce_perf -b 8 -e 8G -f 2 -g 1
C) #!/bin/bash
#SBATCH --partition=gpu
#SBATCH --nodes=8
#SBATCH --gpus=64
#SBATCH --exclusive

export NCCL_IB_DISABLE=0
export NCCL_NET_GDR_LEVEL=5
srun --mpi=pmi2 -n 64 ./all_reduce_perf -b 8 -e 8G -f 2 -g 1
D) #!/bin/bash
#SBATCH --partition=gpu
#SBATCH --nodes=8
#SBATCH --ntasks-per-node=1
#SBATCH --gres=gpu:8
#SBATCH --exclusive

export NCCL_IB_DISABLE=0
srun bash -c 'CUDA_VISIBLE_DEVICES=0,1,2,3,4,5,6,7 ./all_reduce_perf -b 8 -e 8G -f 2 -g 8'

---

**Q31.** A data science team is running distributed training jobs on a 4-node DGX A100 cluster managed by Slurm. Jobs frequently fail with NCCL timeout errors during the all-reduce phase. The team has verified network connectivity between nodes using ping and iperf3, and nvidia-smi shows all GPUs are healthy. Which diagnostic step will MOST effectively identify the root cause?

A) Increase the NCCL_TIMEOUT environment variable from 600 to 3600 seconds in the sbatch script
B) Switch from NCCL_IB_DISABLE=0 to NCCL_IB_DISABLE=1 to force NCCL to use TCP/IP instead of InfiniBand
C) Run dcgmi diag -r 3 on all nodes to check GPU interconnect topology and NVLink bandwidth
D) Restart the slurmd daemon on the compute nodes to refresh GPU enumeration

---

**Q32.** An AI platform team needs to deploy a Triton Inference Server instance on Kubernetes that serves three different model formats: TensorRT engines built for A100 GPUs, PyTorch models requiring CUDA 12.2, and ONNX models. The deployment must minimize container image size and ensure all models can load simultaneously. Which approach BEST meets these requirements?

A) Deploy the tritonserver:24.01-py3 container from NGC with all backends enabled and use a shared PersistentVolume to mount model repositories for all three formats
B) Build a custom minimal Triton container including only the TensorRT, PyTorch, and ONNX backends, then use an InitContainer to download models from NGC private registry into an emptyDir volume
C) Deploy three separate Triton pods, each with a backend-specific container (tritonserver:24.01-trtonly, pytorch-only, onnxruntime-only) behind a single Kubernetes Service with session affinity
D) Use the tritonserver:24.01-py3-min container from NGC, install only required backends via pip at runtime in the pod's entrypoint script, and mount models from an NFS share

---

**Q33.** A research organization uses Run:ai to manage GPU resources across five teams. Team A requires guaranteed access to 8 A100 GPUs for production inference workloads. Team B runs batch training jobs that can tolerate preemption. The admin has configured a Project for Team A with a quota of 8 GPUs and deservedGPUs=8. Team B jobs are frequently stuck in Pending state even when Team A is using only 2 GPUs. What is the MOST likely cause?

A) The cluster does not have over-quota enabled, preventing Team B from using unused GPUs from Team A's allocation
B) Team A's workloads are using node selectors that reserve specific nodes, preventing scheduling of Team B jobs on those nodes
C) The Run:ai scheduler is configured with fairness=true, which enforces strict quota boundaries to prevent starvation
D) Team B's Project has allowOverQuota=false set, blocking it from borrowing idle GPUs

---

**Q34.** A company is deploying NVIDIA NIM microservices for Llama-3.1-70B inference on a Kubernetes cluster with 4 nodes (each with 8x A100 80GB GPUs). The NIM container requires tensor parallelism across 4 GPUs and must serve requests with <500ms P99 latency. During load testing with 100 concurrent requests, latency spikes to 2.3 seconds and kubectl logs show 'CUDA out of memory' errors. DCGM metrics show GPU utilization at 45% and GPU memory at 92%. Which solution will MOST effectively resolve the latency issue?

A) Increase the NIM container's --max-batch-size parameter from the default 8 to 32 to improve throughput and reduce per-request memory allocation
B) Deploy multiple NIM replicas with 4 GPUs each using a Deployment with anti-affinity rules, and place a load balancer (Istio or NGINX) in front to distribute requests across replicas
C) Enable CUDA Unified Memory (cudaMallocManaged) in the NIM configuration to allow GPU memory to spill to system RAM when GPU memory is exhausted
D) Switch from the fp16 NIM profile to the int8 quantized profile to reduce memory footprint from 72GB to 42GB per replica

---

**Q35.** A multi-node DGX A100 cluster is experiencing severe performance degradation during large-scale distributed training. NCCL benchmarks show expected bandwidth on intra-node communication but 10x slower than expected bandwidth on inter-node AllReduce operations. The fabric manager service is running on all nodes. Inspecting `/var/log/fabricmanager.log` reveals repeated "NVSwitch port flapping detected on switch 0, port 12" messages on multiple nodes. Which action will MOST effectively resolve the inter-node communication bottleneck?

A) Restart the NCCL communication by setting NCCL_IB_DISABLE=1 to force fallback to TCP/IP sockets until the NVSwitch issue is diagnosed
B) Increase the NCCL timeout values by setting NCCL_TIMEOUT=7200 to allow more time for the unstable links to establish
C) Physically inspect and reseat the NVLink cables connected to the failing NVSwitch ports, then restart fabric manager on affected nodes
D) Disable fabric manager and configure static NVLink topology using nvidia-smi nvlink --setcontrol to bypass NVSwitch

---

**Q36.** An AI training job repeatedly fails when pulling the `nvcr.io/nvidia/pytorch:24.03-py3` container image with the error "error pulling image configuration: unknown blob". The NGC API key is correctly configured in `/etc/nvcr.io/config.json`. Running `docker pull nvcr.io/nvidia/cuda:12.3.1-base-ubuntu22.04` succeeds without errors. Which single action is the BEST next troubleshooting step to isolate the root cause?

A) Check available disk space on the Docker data directory (`/var/lib/docker`) and expand if usage exceeds 85%
B) Inspect the Docker daemon logs (`journalctl -u docker`) for blob digest mismatches or registry connection timeout errors specific to the pytorch image
C) Verify network connectivity to nvcr.io by testing `curl -I https://nvcr.io/v2/` and checking for HTTP 200 response
D) Remove all cached layers with `docker system prune -a` and retry the pull to eliminate corrupted layer cache

---

**Q37.** A Base Command Manager cluster has 8 DGX nodes, but only 6 appear in the BCM UI node inventory. SSH access to all 8 nodes works correctly. Running `bcm-agent status` on the two missing nodes shows "bcm-agent is not running". Checking `/var/log/bcm-agent.log` on one missing node shows "Certificate validation failed: unable to get local issuer certificate" repeated every 30 seconds. What is the MOST likely cause and correct resolution?

A) The node's system clock is out of sync causing certificate expiration validation errors; synchronize time with `ntpdate -u <bcm-manager-ip>` and restart bcm-agent
B) The bcm-agent service has incorrect firewall rules blocking HTTPS egress to the BCM manager; configure UFW to allow outbound connections on port 443 and restart bcm-agent
C) The node was imaged with an outdated BCM agent version incompatible with the current manager; run `bcm-node-upgrade` on the missing nodes to pull the latest agent version from the manager
D) The BCM cluster root CA certificate is missing from the node's trusted certificate store; copy `/etc/bcm/ca-bundle.crt` from a working node to `/usr/local/share/ca-certificates/` on missing nodes and run `update-ca-certificates`, then restart bcm-agent

---

**Q38.** A containerized training workload using GPUDirect Storage (GDS) to read training data from a local NVMe RAID array is achieving only 3 GB/s read throughput per GPU, while the storage array is capable of 28 GB/s aggregate. The container was launched with `--runtime=nvidia --gpus all -v /mnt/nvme:/data`. Running `cat /proc/driver/nvidia/capabilities/gds/status` inside the container shows "GDS Version: N/A". What must be added to the container launch configuration to enable GDS?

A) Add `--cap-add=SYS_ADMIN` to grant the container kernel module loading capabilities required for GDS cuFile drivers
B) Add `--device=/dev/nvidia-fs0:/dev/nvidia-fs0` to expose the GDS device node inside the container namespace
C) Add `--privileged` flag to grant full device access and disable security constraints preventing GDS kernel driver access
D) Mount `/run/udev` into the container with `-v /run/udev:/run/udev:ro` to allow dynamic GDS device discovery

---

**Q39.** Multiple users report that training jobs running on a shared AI cluster experience intermittent 50% performance drops for 10-15 second periods. Monitoring shows GPU utilization dropping from 98% to 45% during these periods, while GPU memory usage remains constant. The jobs use a shared NFS-mounted dataset (`/nfs/datasets`) for training data. Running `nfsstat -c` during a performance drop shows the "retrans" counter increasing rapidly. Which configuration change will MOST effectively eliminate the performance drops?

A) Increase the NFS read size by remounting with `rsize=1048576,wsize=1048576` to reduce the number of RPC calls and network overhead
B) Enable NFS async mode by remounting with `async` flag to eliminate client blocking on write acknowledgments during checkpointing
C) Increase the NFS retransmission timeout by remounting with `timeo=600,retrans=2` to reduce spurious retransmissions during network congestion
D) Switch from NFS to a parallel filesystem (BeeGFS or Lustre) to eliminate single-server bottleneck and provide aggregate bandwidth scaling

---

**Q40.** A new NGC container (`nvcr.io/nvidia/tensorflow:24.05-tf2-py3`) fails to start on a DGX A100 node with the error "Failed to initialize NVML: Driver/library version mismatch". The host is running NVIDIA driver version 535.104.05 and CUDA 12.2. Running `nvidia-smi` on the host works correctly and shows all 8 GPUs. Inspecting the container's build metadata shows it was built with CUDA 12.4. What is the correct action to resolve the container startup failure?

A) Downgrade the container to `nvcr.io/nvidia/tensorflow:24.01-tf2-py3` which uses CUDA 12.2 matching the host CUDA version
B) Rebuild the container from source using `docker build --build-arg CUDA_VERSION=12.2` to match the host CUDA version
C) Upgrade the host NVIDIA driver to version 545.23.08 or newer to support CUDA 12.4 runtime compatibility required by the container
D) Set the environment variable `NVIDIA_DISABLE_REQUIRE=true` when launching the container to bypass CUDA version requirement checks

---

**Q41.** A multi-node DGX system using NVLink and NVSwitch shows degraded collective communication performance in NCCL benchmarks. The nvidia-smi output shows all GPUs healthy, but fabric manager logs show repeated 'fm_cmd_ctrl: fabric manager service unresponsive' errors on node dgx-03. GPU topology queries via nvidia-smi topo -m show incomplete NVLink connectivity from GPUs on dgx-03. Which action will MOST likely restore full NVSwitch fabric performance?

A) Restart the nvidia-fabricmanager service on dgx-03 and verify all NVLink connections are operational via nvidia-smi nvlink -s
B) Restart the NCCL_DEBUG=INFO environment variable and re-run the benchmark to capture detailed topology errors
C) Update the CUDA driver on dgx-03 to match the newest available version in the NGC catalog
D) Increase the PCIe bandwidth allocation in the system BIOS for GPUs on dgx-03

---

**Q42.** An AI training workload using the NGC PyTorch container fails at startup on a Base Command Manager-provisioned cluster with the error 'docker: Error response from daemon: could not select device driver "nvidia" with capabilities: [[gpu]]'. The node shows nvidia-smi working correctly, NVIDIA driver version 550.54.15 installed, and Docker version 24.0.7. The container is pulled successfully. Which component is MOST likely misconfigured?

A) The NVIDIA Container Toolkit (nvidia-container-toolkit) is not installed or the Docker daemon has not been restarted after toolkit installation
B) The NGC container image version is incompatible with CUDA 12.4 present on the host system
C) The Base Command Manager image synchronization service failed to propagate the container runtime configuration to the node
D) The Docker storage driver is set to overlay2 instead of devicemapper, preventing GPU device passthrough

---

**Q43.** A distributed training job across 8 nodes using GPUDirect RDMA for inter-node communication shows significantly lower throughput than expected (measured via NCCL bandwidth tests). Each node has 8 A100 GPUs and Mellanox ConnectX-6 NICs. The nv_peer_mem kernel module is loaded, and 'nvidia-smi topo -m' shows expected GPU-NIC affinity. GPUDirect Storage tests show normal performance. Running 'cat /sys/class/infiniband/mlx5_0/device/roce_mode' returns 'RoCE v2', but NCCL tests still underperform. Which configuration change will MOST likely resolve the throughput issue?

A) Switch the InfiniBand fabric mode from RoCE v2 to native InfiniBand mode by reconfiguring the Mellanox adapters with 'mstconfig set LINK_TYPE_P1=IB'
B) Verify the GPU memory pinning limit is sufficient by running 'ulimit -l unlimited' before launching the training job
C) Enable GPUDirect RDMA environment variable 'NCCL_IB_GID_INDEX=3' to use a different GID table entry for RDMA operations
D) Increase the NCCL socket timeout with 'NCCL_SOCKET_IFNAME=ib0' and 'NCCL_IB_TIMEOUT=25' to allow longer RDMA setup times

---

**Q44.** A Base Command Manager cluster shows multiple nodes stuck in 'Provisioning' state for over 30 minutes. The BCM UI logs show 'image sync pending' for these nodes. An administrator runs 'bcm-admin image list' and sees the target OS image status as 'available' with checksum verified. Running 'bcm-admin node status node-07' shows 'last heartbeat: 32 minutes ago'. The PXE boot logs on node-07 show successful DHCP lease and TFTP kernel download. Which troubleshooting step will MOST directly identify the provisioning failure cause?

A) Review the node-07 console output or serial logs to check if the kernel booted but failed to mount the root filesystem or reach the BCM agent init process
B) Check the Base Command Manager image server daemon status with 'systemctl status bcm-image-server' and verify the HTTP service is listening on port 8080 for image downloads
C) Verify network connectivity between the node's out-of-band management interface (BMC/iDRAC) and the Base Command Manager server using 'ipmitool' to confirm IPMI access
D) Restart the Base Command Manager primary controller service with 'systemctl restart bcm-controller' to reset the provisioning state machine

---

