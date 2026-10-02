## Chapter 8: Troubleshooting NVIDIA AI Infrastructure — Practice Questions

**Q1.** A data scientist running a containerized training workload receives the error 'nvidia-container-cli: initialization error with device' when starting a Docker container with '--gpus all'. The NVIDIA driver version is 535.104.05 and nvidia-smi works correctly on the host. Which action will MOST likely resolve this issue?

A) Downgrade the NVIDIA driver to version 470.x to match the container runtime requirements
B) Restart the Docker daemon and verify the NVIDIA Container Toolkit is installed with 'nvidia-ctk --version'
C) Add the '--privileged' flag to the docker run command to grant full device access
D) Rebuild the container image with CUDA 12.0 base image instead of CUDA 11.8

<details>
<summary>Answer</summary>

**Correct: B**

The 'nvidia-container-cli: initialization error' typically indicates the NVIDIA Container Toolkit is not properly installed or the Docker daemon hasn't been restarted after toolkit installation. Verifying toolkit installation with 'nvidia-ctk --version' and restarting Docker daemon are standard troubleshooting steps. The daemon must reload /etc/docker/daemon.json to recognize the NVIDIA runtime.

**Why the others are wrong:**
- Driver version 535.x is modern and compatible; downgrading is unnecessary and driver 470.x is outdated for current CUDA versions
- The --privileged flag is a security risk and doesn't address the root cause of container toolkit misconfiguration
- Container image CUDA version mismatch causes different errors (CUDA version incompatibility); this specific error is runtime/toolkit related

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q2.** An 8-GPU DGX A100 system shows degraded multi-GPU training performance. Running 'nvidia-smi nvlink --status' shows 'Link 0: inactive' for several connections. Which step should be taken FIRST to diagnose this NVLink issue?

A) Replace the NVSwitch hardware components connecting the GPUs
B) Check if the nvidia-fabricmanager service is running with 'systemctl status nvidia-fabricmanager'
C) Update the NVIDIA driver to the latest production branch version
D) Reduce the batch size in the training script to lower GPU memory pressure

<details>
<summary>Answer</summary>

**Correct: B**

On DGX/HGX systems with NVSwitch (A100, H100), the fabric manager service is REQUIRED for NVLink to function. Inactive NVLink connections are a primary symptom of fabric manager not running. This is the first diagnostic step before hardware replacement. The service manages NVSwitch configuration and must be active for multi-GPU topologies.

**Why the others are wrong:**
- Hardware replacement is premature without software diagnostics; fabric manager issues are far more common than hardware failures
- Driver updates don't typically resolve fabric manager service issues; the service must be explicitly started and enabled
- Batch size affects memory usage, not NVLink connectivity status; inactive links indicate a connectivity/service issue, not workload sizing

</details>

---

**Q3.** A user attempts to use GPUDirect Storage for fast data loading but receives errors when running a GDS-enabled application. The nvidia-fs kernel module is loaded (verified with 'lsmod | grep nvidia_fs'). Which file must be correctly configured for GDS to function?

A) /etc/modprobe.d/nvidia.conf with NVreg_EnableGpuFirmware=1 parameter
B) /etc/cufile.json with correct 'mountpoint' entries for the storage devices
C) /etc/docker/daemon.json with the default-runtime set to nvidia
D) /usr/local/cuda/version.txt verifying CUDA toolkit 11.4 or later is installed

<details>
<summary>Answer</summary>

**Correct: B**

GPUDirect Storage requires /etc/cufile.json to specify which storage mountpoints are GDS-enabled. Even if nvidia-fs driver is loaded, applications need this configuration to identify GDS-capable paths. The file contains 'allow_compat_mode', 'poll_mode', and 'mountpoint' directives that control GDS behavior. Missing or incorrect mountpoint configuration is a common GDS troubleshooting issue.

**Why the others are wrong:**
- NVreg_EnableGpuFirmware parameter relates to GPU firmware loading, not GPUDirect Storage functionality
- Docker daemon.json configures container runtime, irrelevant to GDS which operates at kernel/driver level for direct storage access
- CUDA toolkit version matters for compilation but cufile.json is the runtime configuration that GDS applications read for storage path mapping

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q4.** During distributed training across 4 nodes with InfiniBand, NCCL operations hang indefinitely. Running 'nvidia-smi topo -m' shows 'NIC0 NODE' connections. Which command will provide the MOST detailed diagnostic information about the NCCL communication failure?

A) Execute 'ibstatus' to verify InfiniBand port state is ACTIVE and LID is assigned
B) Run the training script with 'NCCL_DEBUG=INFO' environment variable and examine the NCCL bootstrap and ring initialization logs
C) Use 'lspci | grep Mellanox' to confirm InfiniBand adapters are detected by the system
D) Run 'nvidia-smi -q -d PERFORMANCE' to check GPU clock throttling status

<details>
<summary>Answer</summary>

**Correct: B**

NCCL_DEBUG=INFO is the primary diagnostic tool for NCCL communication issues. It shows bootstrap exchange, transport selection (IB vs IP), ring formation, and where communication hangs occur. The logs will reveal if NCCL falls back to IP instead of using InfiniBand, timeout errors, or rank coordination failures. This provides the most specific information about WHAT is failing in NCCL.

**Why the others are wrong:**
- ibstatus checks InfiniBand hardware state but doesn't show NCCL-specific communication patterns or where in the NCCL protocol the hang occurs
- lspci confirms hardware detection but doesn't diagnose why NCCL can't use the adapters or where communication breaks down
- GPU clock throttling affects compute performance, not NCCL communication hangs which are network/synchronization related

</details>

---

**Q5.** A container fails to start with 'Error response from daemon: could not select device driver "nvidia" with capabilities: [[gpu]]'. The /etc/docker/daemon.json file exists. Which configuration is REQUIRED in this file to resolve the error?

A) { "exec-opts": ["native.cgroupdriver=systemd"], "storage-driver": "overlay2" }
B) { "gpus": "all", "nvidia-driver-capabilities": "compute,utility" }
C) { "default-runtime": "nvidia", "runtimes": { "nvidia": { "path": "nvidia-container-runtime", "runtimeArgs": [] } } }
D) { "insecure-registries": ["nvcr.io"], "default-ulimits": { "memlock": { "Hard": -1, "Soft": -1 } } }

<details>
<summary>Answer</summary>

**Correct: C**

The error 'could not select device driver "nvidia"' means Docker doesn't recognize the NVIDIA runtime. The daemon.json MUST define the 'nvidia' runtime in the 'runtimes' section pointing to nvidia-container-runtime. Setting 'default-runtime' to 'nvidia' makes it the default, but the key requirement is registering the runtime with the correct path. This exact JSON structure is documented in NVIDIA Container Toolkit installation guides.

**Why the others are wrong:**
- exec-opts and storage-driver configure Docker's general operation, not GPU access; they don't register the NVIDIA runtime
- These aren't valid daemon.json directives; --gpus is a docker run flag, not a daemon configuration option
- insecure-registries and ulimits are unrelated to runtime selection; they configure registry access and resource limits respectively

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q6.** An administrator needs to verify GPUDirect RDMA is working correctly between GPUs and InfiniBand NICs. Which combination of checks confirms GPUDirect RDMA is functional?

A) Check 'nvidia-smi nvlink --status' shows all links active AND run 'ibv_devinfo' to list InfiniBand devices
B) Confirm 'lsmod | grep nv_peer_mem' shows the module loaded AND verify 'NCCL_IB_DISABLE=0' is set (or unset, as 0 is default) before running NCCL tests
C) Verify '/proc/driver/nvidia/gpus/*/information' contains peer memory entries AND restart the nvidia-persistenced daemon
D) Run 'gdsio -f /dev/nvme0n1' to test direct GPU storage access AND confirm InfiniBand subnet manager is running

<details>
<summary>Answer</summary>

**Correct: B**

GPUDirect RDMA requires: (1) nv_peer_mem kernel module to enable peer-to-peer memory access between GPUs and NICs, and (2) NCCL configured to USE InfiniBand (NCCL_IB_DISABLE=0, which is the default). These two checks directly verify the GPUDirect RDMA stack is loaded and NCCL will attempt to use it. NCCL tests with these settings confirm end-to-end functionality.

**Why the others are wrong:**
- NVLink status is GPU-to-GPU connectivity, separate from GPU-to-NIC GPUDirect RDMA; ibv_devinfo only shows IB hardware detection, not GPU integration
- Peer memory proc entries are informational; nvidia-persistenced manages driver persistence, not GPUDirect RDMA functionality
- gdsio tests GPUDirect Storage (different technology); subnet manager is necessary for IB but doesn't confirm GPU-NIC peer memory is working

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q7.** A 2-node training job with 8 GPUs per node experiences NCCL hangs during all_reduce operations. Running 'ibstatus' on both nodes shows 'State: Active' and 'Physical state: LinkUp'. Which diagnostic step will BEST identify the root cause?

A) Execute 'nvidia-smi dmon -s u' to monitor GPU utilization patterns during the hang
B) Check '/var/log/messages' for InfiniBand driver errors or firmware alerts
C) Verify CUDA driver version matches across both nodes with 'cat /proc/driver/nvidia/version'
D) Run 'nccl-tests/build/all_reduce_perf -b 8 -e 128M -f 2 -g 8' with 'NCCL_DEBUG=INFO' and check for 'Ring XX : waiting for connections' timeouts

<details>
<summary>Answer</summary>

**Correct: D**

Since ibstatus confirms basic IB connectivity, the issue is in NCCL layer. Running nccl-tests (specifically all_reduce_perf which tests collective operations) with NCCL_DEBUG=INFO will show WHERE the hang occurs: during bootstrap, ring formation, or data transfer. The 'waiting for connections' message indicates which ranks can't communicate, revealing if it's a network routing, firewall, or NCCL configuration issue. This is the standard NCCL troubleshooting methodology.

**Why the others are wrong:**
- GPU utilization monitoring shows compute activity but won't reveal NCCL communication protocol failures or which ranks are stuck
- System logs may show hardware errors but won't provide NCCL-specific protocol debugging that shows the exact communication failure point
- Driver version mismatch causes startup errors or undefined behavior, not mid-operation hangs on established connections; NCCL_DEBUG is more diagnostic

</details>

---

**Q8.** After installing NVIDIA Container Toolkit on Ubuntu 22.04, a user runs 'docker run --rm --gpus all nvidia/cuda:12.0.0-base-ubuntu22.04 nvidia-smi' but receives 'docker: Error response from daemon: failed to create shim task'. What is the MOST likely cause?

A) The CUDA container image version (12.0.0) is incompatible with the host NVIDIA driver version
B) The Docker daemon was not restarted after installing nvidia-container-toolkit; run 'sudo systemctl restart docker'
C) The '--gpus all' flag requires Docker Engine version 20.10 or later; upgrade Docker
D) SELinux is blocking GPU device access; set 'setenforce 0' to disable SELinux temporarily

<details>
<summary>Answer</summary>

**Correct: B**

The 'failed to create shim task' error after toolkit installation almost always means Docker daemon hasn't reloaded its configuration to recognize the NVIDIA runtime. The installation modifies /etc/docker/daemon.json but Docker must be restarted to read this file and register the nvidia-container-runtime. This is a commonly missed post-installation step explicitly mentioned in NVIDIA's official setup documentation.

**Why the others are wrong:**
- Container CUDA version can be higher than host driver supports specific features, but 'shim task' error is a Docker runtime error, not CUDA compatibility
- The --gpus flag is indeed a Docker 19.03+ feature, but the specific 'shim task' error indicates runtime configuration issue, not missing Docker functionality
- Ubuntu 22.04 uses AppArmor not SELinux by default; even if SELinux were present, this specific error is Docker runtime related, not MAC policy blocking

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

