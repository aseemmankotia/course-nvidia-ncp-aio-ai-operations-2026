## Chapter 3: Advanced BCM Operations: Firmware, Networking, and Troubleshooting — Practice Questions

**Q1.** A data center engineer needs to update GPU firmware across a 128-node DGX SuperPOD cluster to address a critical security vulnerability. The firmware update must be applied without manual intervention on each node and should allow rollback if issues occur. Which BCM approach BEST meets these requirements?

A) Use 'cmsh' to create a firmware update category, assign nodes to it, and execute 'device update firmware -c <category>' with the --backup flag to enable rollback
B) Manually download firmware files to each node via SSH and run the manufacturer's update utility individually on all 128 nodes
C) Configure a cron job on the head node to push firmware updates using Ansible playbooks and custom scripts
D) Use the GPU vendor's proprietary management software separately from BCM to update all GPUs simultaneously

<details>
<summary>Answer</summary>

**Correct: A**

BCM's category-based firmware management allows cluster-wide GPU firmware updates with a single command. The --backup flag creates a rollback point, and BCM tracks update status across all nodes. This is the exam-tested method for firmware updates in NVIDIA Base Command Manager.

**Why the others are wrong:**
- Manual SSH to 128 nodes violates the requirement for no manual intervention per node and doesn't provide BCM-integrated rollback or tracking
- External Ansible scripts bypass BCM's firmware management features and won't integrate with BCM's node provisioning state or health checks
- Using separate vendor tools creates management silos and doesn't leverage BCM's cluster-wide orchestration or node category system

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q2.** An administrator configures InfiniBand networking for a new compute cluster in BCM. After provisioning nodes, half the nodes show 'IB link down' status. Running 'ibstat' on affected nodes shows 'State: Down, Physical state: Polling'. Which action should the administrator take FIRST?

A) Verify the InfiniBand subnet manager (opensm) is running and configured correctly in BCM network policies, as only one active SM should manage the fabric
B) Replace all InfiniBand cables on affected nodes assuming physical layer failure
C) Reboot all compute nodes simultaneously to reset the InfiniBand adapters
D) Increase the InfiniBand timeout values in the switch configuration files

<details>
<summary>Answer</summary>

**Correct: A**

'Physical state: Polling' indicates the port is attempting to establish a link but cannot complete subnet manager handshake. BCM configures the subnet manager via network policies. Missing or misconfigured SM is the most common cause of this exact symptom. Exam tests this specific troubleshooting sequence.

**Why the others are wrong:**
- Polling state suggests logical configuration issues, not physical cable failure; cables would show 'Physical state: Disabled' or no detection
- Mass reboots don't address SM configuration and may cause cluster-wide disruption; proper troubleshooting starts with network layer checks
- Timeout values affect established links, not initial link establishment; Polling state means links never reached Active state

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q3.** A company needs to install DOCA Services on BlueField-2 DPU Arm cores across 64 nodes to enable DPU-accelerated NVMe-oF storage. The installation must be automated through BCM and ensure all DPUs run identical DOCA versions. Which BCM feature accomplishes this?

A) SSH into each DPU's Arm core individually and run 'apt-get install doca-services' on all 64 DPUs
B) Create a DPU software image in BCM's image repository, assign it to a node category containing the 64 nodes, and provision the DPUs using 'cmsh device provision'
C) Use NVIDIA GPU Cloud (NGC) to pull DOCA containers directly to each DPU, bypassing BCM
D) Configure the x86 host OS to install DOCA on the DPU via PCIe passthrough commands

<details>
<summary>Answer</summary>

**Correct: B**

BCM treats BlueField DPUs as provisionable devices with dedicated software images. Image-based provisioning ensures version consistency and leverages BCM's category system for fleet management. This is the exam-tested workflow for DPU software deployment.

**Why the others are wrong:**
- Manual SSH to 64 DPUs doesn't use BCM automation, can't guarantee version consistency, and bypasses BCM's image management
- NGC containers are application-layer; DOCA Services installation requires OS-level packages deployed via BCM's provisioning system
- DOCA must be installed on the DPU's Arm OS, not passed through from x86 host; this approach is architecturally incorrect

</details>

---

**Q4.** During node provisioning, 12 out of 96 nodes fail with error 'Image sync failed: connection timeout to repository'. The administrator verifies network connectivity from the head node to the image repository server on port 443. Which BCM troubleshooting step should be performed NEXT?

A) Restart the bcm-server service on the head node to reset all provisioning jobs
B) Delete and recreate the entire node category containing the failing nodes
C) Increase the repository connection timeout value in bcm.conf from default 30 seconds to 300 seconds
D) Check if the 12 failing nodes can reach the image repository by examining DHCP/PXE logs and verifying node network configuration in 'cmsh show node' output

<details>
<summary>Answer</summary>

**Correct: D**

Head node connectivity confirms repository is reachable, so the issue is node-level network config. DHCP/PXE logs reveal if nodes received correct IP, gateway, and DNS to reach the repository. 'cmsh show node' shows assigned network parameters. Exam tests this specific troubleshooting progression.

**Why the others are wrong:**
- Restarting bcm-server doesn't fix node network misconfigurations and would interrupt all active provisioning, not just the 12 failures
- Deleting categories doesn't address the root cause (node network config) and destroys provisioning metadata needed for debugging
- Timeout increases mask the underlying network problem; nodes with correct network config sync in under 30 seconds

</details>

---

**Q5.** A cluster administrator needs to generate a monthly report showing GPU utilization percentage, power consumption in watts, and completed job count per node for capacity planning. Which BCM command provides this data?

A) cmsh report generate --type usage --metrics gpu_util,power,jobs --period monthly
B) nvidia-smi dmon -s puc > monthly_report.csv
C) cat /var/log/bcm/job_history.log | grep completed
D) bcm-export --nodes all --data gpu_metrics --format csv

<details>
<summary>Answer</summary>

**Correct: A**

BCM's 'report generate' command aggregates telemetry data collected from nodes over specified periods. The --type usage flag targets utilization/performance metrics. This is the exam-tested reporting mechanism in BCM for historical cluster analytics.

**Why the others are wrong:**
- nvidia-smi dmon provides real-time monitoring, not historical aggregation across the cluster or job correlation
- Raw log parsing doesn't provide GPU utilization or power data, only job completion events without metrics
- bcm-export is not a standard BCM command; the exam tests 'cmsh report generate' for usage reporting

</details>

---

**Q6.** After a BCM upgrade, nodes show 'health: degraded' status in 'cmsh show health'. The administrator runs 'cmsh show health -v' and sees 'bcm-agent: CRITICAL - last heartbeat 180 seconds ago' on 24 nodes. Node SSH access works normally. What is the MOST likely cause?

A) Network latency between head node and compute nodes exceeds the heartbeat threshold
B) Nodes require BMC firmware updates to be compatible with the new BCM version
C) The BCM license expired and disabled monitoring for nodes beyond the license limit
D) The bcm-agent service failed to restart automatically on those nodes after the BCM upgrade; run 'cmsh device execute -c <category> systemctl restart bcm-agent' to recover them

<details>
<summary>Answer</summary>

**Correct: D**

Agent heartbeat failure with working SSH indicates the bcm-agent daemon isn't running, not network or hardware issues. BCM upgrades sometimes require manual agent restart. 'device execute' allows cluster-wide service management. Exam tests this specific post-upgrade troubleshooting scenario.

**Why the others are wrong:**
- 180-second heartbeat gap exceeds normal latency; network issues would affect SSH too, which works normally
- BMC firmware doesn't affect bcm-agent daemon operation; agent runs in the node OS, not BMC
- License expiration would prevent provisioning or show license errors, not selective agent heartbeat failures

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q7.** A data center needs to update BMC firmware on 200 compute nodes to version 3.47 to fix an IPMI vulnerability. The current version is 3.42. BCM must verify BMC accessibility before attempting updates and skip nodes with unreachable BMCs. Which command sequence accomplishes this?

A) cmsh device update bmc --version 3.47 --verify-ipmi --skip-unreachable -c compute_nodes
B) ipmitool -H <each_node_bmc_ip> -U admin -P password fwum update bmc_3.47.bin (manually on all 200 nodes)
C) cmsh device provision --force --image bmc-3.47 -c compute_nodes
D) ansible-playbook update_bmc.yml --extra-vars 'version=3.47' --limit compute_nodes

<details>
<summary>Answer</summary>

**Correct: A**

BCM's 'device update bmc' command handles cluster-wide BMC firmware updates. The --verify-ipmi flag checks IPMI connectivity before update attempts. --skip-unreachable prevents failures on inaccessible BMCs. This is the exam-tested method for safe BMC updates at scale.

**Why the others are wrong:**
- Manual ipmitool usage on 200 nodes violates automation requirement and doesn't integrate with BCM's tracking or rollback features
- 'device provision' is for OS provisioning, not firmware updates; --image expects OS images, not BMC firmware files
- External Ansible bypasses BCM's firmware management, doesn't leverage BCM's IPMI connectivity tracking, and creates management fragmentation

</details>

---

**Q8.** An administrator provisions 32 new nodes with ConnectX-7 NICs. Provisioning completes successfully, but 'cmsh show node' displays 'NIC firmware: NOT_SYNCED' for all 32 nodes. The cluster's existing nodes run ConnectX-7 firmware version 28.35.1012. What should the administrator do to resolve this?

A) Ignore the warning since 'NOT_SYNCED' doesn't affect network functionality
B) Manually download Mellanox Firmware Tools (MFT) and run mlxfwmanager on each node individually
C) Add the ConnectX-7 firmware version 28.35.1012 image to BCM's firmware repository and run 'cmsh device update nic --sync --version 28.35.1012 -c new_nodes' to align firmware versions
D) Reprovision all 32 nodes with the --force-firmware flag to overwrite NIC firmware

<details>
<summary>Answer</summary>

**Correct: C**

NOT_SYNCED indicates NIC firmware doesn't match BCM's expected version for the cluster. BCM's 'device update nic --sync' command synchronizes NIC firmware to a specified version across selected nodes. This ensures cluster-wide firmware consistency. Exam tests this specific firmware sync workflow.

**Why the others are wrong:**
- NOT_SYNCED can cause RoCE compatibility issues, performance degradation, and failed InfiniBand collective operations; must be resolved
- Manual MFT usage bypasses BCM's firmware tracking, doesn't scale to 32 nodes efficiently, and creates version drift over time
- 'device provision' updates OS images; NIC firmware requires separate 'device update nic' commands, not OS reprovisioning

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

