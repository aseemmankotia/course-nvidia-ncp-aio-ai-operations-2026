## Chapter 1: Base Command Manager: Installation and Cluster Initialization — Practice Questions

**Q1.** A data center administrator needs to deploy Base Command Manager for a new AI cluster with 64 A100 nodes and 32 H100 nodes. The cluster requires separate job scheduling for each GPU type and real-time monitoring of GPU temperature and power consumption. Which BCM architecture component BEST enables viewing GPU metrics across all nodes simultaneously?

A) Cluster Management Shell (cmsh) with custom monitoring scripts executed via cron
B) NVIDIA GPU Operator deployed on each compute node with Prometheus exporters
C) Head node syslog aggregation with GPU metrics forwarded from NVIDIA DCGM agents
D) Base View dashboard configured to display GPU utilization, temperature, and power metrics

<details>
<summary>Answer</summary>

**Correct: D**

Base View dashboard is the built-in BCM web interface specifically designed for real-time cluster monitoring including GPU utilization, power consumption, temperature, and job metrics across all nodes from a single pane of glass. This is the native BCM solution for the requirement.

**Why the others are wrong:**
- cmsh is the command-line interface for cluster configuration and management, not a real-time monitoring dashboard. Custom scripts would require significant development effort and wouldn't provide the integrated view Base View offers.
- GPU Operator is a Kubernetes-native component for managing GPU resources in containerized environments, not the primary monitoring solution for BCM clusters. This adds unnecessary complexity when Base View already provides the required functionality.
- Syslog aggregation is a logging mechanism, not a real-time dashboard. While DCGM agents collect GPU metrics, this approach doesn't provide the integrated visualization that Base View delivers out-of-the-box.

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q2.** An organization provisioned 48 compute nodes with BCM and needs to organize them for targeted workload scheduling: 24 nodes with A100-80GB GPUs for training workloads and 24 nodes with H100 GPUs for inference workloads. Training jobs should NEVER run on H100 nodes due to cost optimization policies. Which BCM feature accomplishes this requirement?

A) Configure separate VLANs for each GPU type and use network isolation to prevent cross-scheduling
B) Create two node categories (A100-training and H100-inference) and configure job scheduler policies to target specific categories
C) Set different SSH keys for each node group and restrict job submission based on authentication
D) Deploy separate BCM head nodes for each GPU type with independent cluster databases

<details>
<summary>Answer</summary>

**Correct: B**

Node categories in BCM allow administrators to logically group nodes (e.g., by GPU type, purpose, or hardware generation) and configure schedulers like Slurm to target jobs to specific categories. This is the intended BCM mechanism for workload targeting and resource partitioning.

**Why the others are wrong:**
- VLAN segregation provides network isolation but doesn't integrate with BCM's job scheduling system. The scheduler would still see all nodes as available unless explicitly configured, and network isolation alone doesn't prevent job submission to wrong nodes.
- SSH key-based authentication controls user access to nodes, not job scheduling decisions. The job scheduler (Slurm/PBS) uses its own mechanisms for node selection, making this approach ineffective for workload targeting.
- Running separate BCM instances defeats the purpose of centralized cluster management and creates operational overhead. BCM is designed to manage heterogeneous clusters with different node types through categories, not multiple head nodes.

</details>

---

**Q3.** A BCM cluster running firmware version 22.05 on compute nodes needs to be updated to firmware version 24.01 to support new GPU features. The cluster has 96 nodes across 4 racks. Operations policy requires zero downtime for running jobs. What is the CORRECT procedure using BCM sync capabilities?

A) Execute 'cluster sync --force' to immediately update all nodes regardless of job status
B) Manually SSH to each node and run firmware update commands, then reboot nodes individually
C) Drain all running jobs, shut down the entire cluster, apply firmware updates simultaneously, then restart all services
D) Use cmsh to create a new software image with firmware 24.01, assign it to nodes, and execute 'device update' command which applies updates only to idle nodes

<details>
<summary>Answer</summary>

**Correct: D**

BCM's image sync and device update workflow allows administrators to stage firmware updates in software images and apply them selectively. The 'device update' command respects running jobs and updates idle nodes first, enabling rolling updates without disrupting active workloads. This is the proper BCM-native approach.

**Why the others are wrong:**
- 'cluster sync --force' would attempt to update all nodes immediately, potentially disrupting running jobs. BCM provides granular control over update timing; forcing synchronous updates violates the zero-downtime requirement.
- Manual SSH updates bypass BCM's centralized management and tracking capabilities. This approach is error-prone at scale (96 nodes), doesn't leverage BCM's image management, and requires significantly more operational effort.
- Full cluster shutdown is unnecessary with BCM's rolling update capability and violates the zero-downtime requirement. This approach maximizes disruption when BCM is designed specifically to enable updates without full outages.

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q4.** During BCM installation, the initialization process fails with 'database connection timeout' error. The head node hardware is NVIDIA-certified and meets all specifications. Network connectivity tests show the management network is operational. Which component is MOST likely causing the initialization failure?

A) Compute nodes not yet added to the cluster causing database schema validation to fail
B) Base View web service not started, preventing database access from cmsh
C) Management network MTU size mismatch between head node and switches
D) PostgreSQL database service not running or cluster database not properly initialized before BCM setup

<details>
<summary>Answer</summary>

**Correct: D**

BCM requires a functioning PostgreSQL database to store cluster configuration, node states, and job information. The initialization process creates database schemas and must connect to PostgreSQL before proceeding. 'Database connection timeout' specifically indicates the database service is unreachable or not running.

**Why the others are wrong:**
- Compute nodes are added AFTER BCM initialization, not before. The cluster database is initialized with just the head node; node additions happen through cmsh after BCM is operational. Missing compute nodes wouldn't cause database connection failures during init.
- Base View is a web interface component that uses the database; it doesn't provide database access to cmsh. Both cmsh and Base View are clients of the PostgreSQL database. Base View status doesn't affect database connectivity for initialization.
- While MTU mismatches can cause network issues, the scenario states network connectivity tests passed. Database connection timeout is a specific error indicating PostgreSQL service issues, not general network problems that would affect connectivity tests.

</details>

---

**Q5.** An administrator needs to grant a new team of 15 data scientists access to a BCM cluster. The team should be able to submit jobs, view their own job status, and access compute nodes for debugging, but should NOT be able to modify cluster configuration, add nodes, or view other users' job details. Which BCM role assignment accomplishes this requirement?

A) Assign the 'user' role to all 15 accounts with default permissions
B) Assign the 'operator' role which provides job management and node access capabilities
C) Assign the 'admin' role with custom ACLs to restrict configuration changes
D) Create accounts without role assignment and rely on POSIX permissions for access control

<details>
<summary>Answer</summary>

**Correct: A**

The 'user' role in BCM is designed for standard cluster users who submit and manage their own jobs, access compute resources for their workloads, and view their own job information. This role explicitly does NOT grant permissions for cluster configuration, node management, or viewing other users' data, matching all requirements.

**Why the others are wrong:**
- The 'operator' role has elevated privileges including the ability to view and manage other users' jobs, cancel jobs cluster-wide, and perform some administrative functions. This exceeds the requirement and violates the principle of least privilege.
- The 'admin' role has full cluster management capabilities including node provisioning, configuration changes, and user management. Even with custom ACLs, this approach is unnecessarily complex and creates security risks. The standard 'user' role already provides exactly what's needed.
- BCM uses role-based access control (RBAC), not just POSIX permissions. Accounts without role assignment would have undefined permissions and likely wouldn't be able to submit jobs through the scheduler or use BCM-managed features. This approach bypasses BCM's security model.

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q6.** A company operates a BCM cluster with 128 compute nodes. After a power outage, 12 nodes show status 'unknown' in Base View dashboard while 116 nodes show 'ready'. The management network is confirmed operational. Using cmsh, what is the FIRST diagnostic command to identify why the 12 nodes are not reporting status?

A) device list -s to display detailed status of all devices and identify nodes with communication failures
B) cluster restart to reinitialize all node connections and force status updates
C) category show to verify the unknown nodes are still assigned to valid categories
D) image sync to push the latest software image and restore node configurations

<details>
<summary>Answer</summary>

**Correct: A**

'device list -s' (or 'device list --status') in cmsh shows detailed status information for all cluster devices including connection state, last heartbeat, and communication errors. This is the primary diagnostic command for investigating why specific nodes aren't reporting to the head node after events like power outages.

**Why the others are wrong:**
- 'cluster restart' is a disruptive operation that restarts BCM services on the head node and would affect all 128 nodes, not just the 12 with issues. Diagnostic commands should be run first to identify the problem before taking disruptive actions. This also wouldn't help if nodes have hardware or boot issues.
- 'category show' displays category definitions and membership but doesn't provide node communication status or help diagnose why nodes are unreachable. Category assignment doesn't affect whether nodes can communicate with the head node after a power event.
- 'image sync' pushes software images to nodes, but requires nodes to be reachable first. If nodes show 'unknown' status, they likely can't communicate with the head node to receive synced images. This addresses a symptom, not the underlying communication issue that needs diagnosis.

</details>

---

**Q7.** A research institution needs to provision 200 newly racked compute nodes in their existing BCM cluster. The nodes require specific firmware version 23.11 and a custom Ubuntu 22.04 image with pre-installed AI frameworks. The existing cluster has 300 nodes running firmware 23.09. What is the MOST efficient BCM workflow to provision the new nodes with correct firmware and software?

A) Add all 200 nodes to the cluster first, then manually SSH to each node to install firmware 23.11 and the Ubuntu image
B) Use cmsh to upgrade all 500 nodes (existing 300 + new 200) to firmware 23.11 simultaneously, then provision with custom image
C) Create a separate BCM cluster for the 200 new nodes to avoid firmware version conflicts with existing nodes
D) Create a new software image with Ubuntu 22.04 and AI frameworks, assign firmware 23.11 to the image, add nodes via cmsh with the new image, then sync

<details>
<summary>Answer</summary>

**Correct: D**

BCM's image-based provisioning allows administrators to create software images that bundle OS, applications, and firmware versions. Assigning the custom image to new nodes during addition (via cmsh 'device add' with image specification) and then syncing ensures all 200 nodes get the correct configuration in one operation. This is the designed BCM workflow for heterogeneous provisioning.

**Why the others are wrong:**
- Manual SSH provisioning bypasses BCM's centralized management and doesn't scale efficiently to 200 nodes. It's error-prone, time-consuming, and loses the benefits of BCM's image tracking and configuration management. BCM provides automated sync specifically to avoid manual node-by-node operations.
- Upgrading all 500 nodes to firmware 23.11 is unnecessary and disruptive to the existing 300 nodes that are working fine on 23.09. BCM supports heterogeneous firmware versions across different node groups through image assignments. This creates unnecessary risk and downtime.
- Running separate BCM clusters for different firmware versions adds operational complexity and defeats the purpose of centralized management. BCM is designed to manage heterogeneous clusters with different hardware, firmware, and software configurations through categories and image assignments.

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q8.** Base View dashboard shows node 'gpu047' with status 'ready' but GPU utilization displays 'N/A' for all 8 GPUs while other nodes show GPU metrics correctly. The node is running jobs successfully and users report no issues. What is the MOST likely cause and solution?

A) Base View web service cache is stale; restart the Base View service on the head node
B) NVIDIA DCGM (Data Center GPU Manager) service not running on gpu047; restart dcgm service on the node
C) Node gpu047 is in the wrong category; reassign to a category with GPU monitoring enabled
D) GPUs on gpu047 have failed hardware sensors; replace the GPUs to restore monitoring

<details>
<summary>Answer</summary>

**Correct: B**

Base View relies on NVIDIA DCGM agents running on each compute node to collect GPU metrics (utilization, temperature, power). If DCGM service isn't running on a specific node, that node won't report GPU telemetry to Base View, resulting in 'N/A' displays. Since jobs run successfully, the GPUs work but monitoring is broken. DCGM service restart is the appropriate fix.

**Why the others are wrong:**
- Base View web service runs on the head node and serves the dashboard to all nodes. If it had issues, no nodes would show GPU metrics, not just gpu047. The fact that other nodes display metrics correctly indicates the Base View service itself is functioning properly.
- Node categories are logical groupings for job scheduling and management; they don't control whether GPU monitoring is enabled. DCGM runs independently of category assignment. If categories controlled monitoring, BCM would require explicit configuration per category, which isn't how GPU telemetry works.
- If GPU hardware sensors failed, the GPUs likely wouldn't function for compute workloads either. The scenario states jobs run successfully, indicating the GPUs are healthy. 'N/A' for all 8 GPUs simultaneously points to a monitoring service issue (DCGM), not hardware failure of all sensors at once.

</details>

---

