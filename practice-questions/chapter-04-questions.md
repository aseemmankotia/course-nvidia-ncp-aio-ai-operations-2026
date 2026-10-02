## Chapter 4: Slurm Cluster Administration for AI Workloads — Practice Questions

**Q1.** A research lab runs mixed workloads: 10 data scientists need interactive Jupyter sessions on A100 GPUs, while 5 ML engineers run multi-day training jobs on H100 GPUs. The admin needs to prevent training jobs from blocking interactive work. Which Slurm configuration BEST achieves this?

A) Create one partition with Priority=1000 for data scientists and Priority=500 for ML engineers in slurm.conf
B) Create two partitions (interactive_a100 and training_h100), set interactive_a100 to OverSubscribe=EXCLUSIVE and training_h100 to OverSubscribe=YES
C) Create two QOS policies: qos_interactive with Priority=10000 and MaxWall=04:00:00, qos_training with Priority=5000 and MaxWall=168:00:00, assign to respective user groups
D) Use scontrol to set all A100 nodes to State=RESERVED for data scientists during business hours

<details>
<summary>Answer</summary>

**Correct: C**

QOS policies directly control job priority and time limits per user group. Priority=10000 ensures interactive jobs preempt training jobs, MaxWall=04:00:00 prevents interactive users from hogging GPUs, and MaxWall=168:00:00 allows week-long training. This is the standard approach for multi-tenant GPU clusters.

**Why the others are wrong:**
- Partition Priority affects node selection, not job preemption between user groups. Both user types would still compete for resources within their partition.
- OverSubscribe controls whether multiple jobs share a node, not job priority. EXCLUSIVE on interactive would waste GPU resources, and doesn't guarantee priority over training jobs.
- State=RESERVED requires manual scontrol commands on schedule and removes nodes from scheduling entirely. Not scalable and doesn't provide dynamic preemption.

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q2.** An admin configures MIG on an A100-80GB node to provide fractional GPU access. The gres.conf contains: 'Name=gpu Type=a100-1g.10gb File=/dev/nvidia0 Cores=0-7 Count=7'. A user submits 'sbatch --gres=gpu:a100-1g.10gb:2 train.sh' but the job stays PENDING with reason 'ReqNodeNotAvail, Reserved for maintenance'. What is the issue?

A) The node is in DRAIN state; run 'scontrol update NodeName=node01 State=RESUME' to return it to service
B) The gres.conf uses 'Count=7' but A100-80GB only supports 7 MIG instances; change to Count=8 to match hardware limit
C) MIG slices must be pre-created with 'nvidia-smi mig -cgi 1g.10gb,1g.10gb,...' before Slurm can allocate them; gres.conf only declares what exists
D) The job requested 2 MIG slices but cores 0-7 are insufficient; change Cores=0-15 to provide enough CPU cores per GPU slice

<details>
<summary>Answer</summary>

**Correct: C**

gres.conf is declarative—it tells Slurm what MIG instances exist, but does NOT create them. The admin must run 'nvidia-smi mig -cgi 1g.10gb -C' seven times (or use nvidia-mig-parted) to physically create the MIG slices on the GPU before Slurm can allocate them. The error 'Reserved for maintenance' is misleading; Slurm sees 0 available MIG resources.

**Why the others are wrong:**
- State=RESUME fixes DRAIN state, but the error 'ReqNodeNotAvail, Reserved for maintenance' here indicates Slurm cannot find matching gres resources, not node state. If node were DRAIN, error would explicitly say 'NodeState=DRAIN'.
- A100-80GB supports 7×1g.10gb MIG slices (correct), so Count=7 is accurate. The issue is the slices don't physically exist on the GPU yet.
- Cores in gres.conf specify CPU affinity for NUMA optimization, not a requirement. The job would run with any core assignment; missing MIG instances is the blocker.

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q3.** A Slurm cluster has 20 users across 3 departments. The admin wants to enforce: Marketing can use max 4 GPUs simultaneously, Engineering max 12 GPUs, Research unlimited. Fair-share must prioritize users who haven't run jobs recently. Which sacctmgr commands achieve this?

A) sacctmgr add account marketing,engineering,research Cluster=ai-cluster; sacctmgr add qos marketing_qos MaxTRESPerUser=gres/gpu=4; sacctmgr add qos eng_qos MaxTRESPerUser=gres/gpu=12; sacctmgr modify account marketing set QOS=marketing_qos; sacctmgr modify account engineering set QOS=eng_qos
B) sacctmgr add account marketing,engineering,research; sacctmgr modify account marketing set GrpTRES=gres/gpu=4 FairShare=100; sacctmgr modify account engineering set GrpTRES=gres/gpu=12 FairShare=100; sacctmgr modify account research set FairShare=100; set PriorityType=priority/multifactor and PriorityWeightFairshare=50000 in slurm.conf
C) sacctmgr add account marketing set GrpTRES=gres/gpu=4; sacctmgr add account engineering set GrpTRES=gres/gpu=12; sacctmgr add account research set GrpTRES=gres/gpu=9999; sacctmgr modify cluster ai-cluster set FairShare=1
D) sacctmgr add qos marketing_qos set GrpTRES=gres/gpu=4 Priority=1000; sacctmgr add qos eng_qos set GrpTRES=gres/gpu=12 Priority=2000; sacctmgr add user dept=marketing set QOS=marketing_qos; PriorityType=priority/multifactor in slurm.conf with PriorityWeightFairshare=10000

<details>
<summary>Answer</summary>

**Correct: B**

GrpTRES limits total simultaneous GPU use per account (4 for marketing, 12 for engineering). FairShare=100 for all accounts ensures equal starting priority, then PriorityWeightFairshare=50000 in slurm.conf makes fair-share the dominant factor—users who haven't run jobs recently get higher priority. This is the standard multi-tenant fair-share setup.

**Why the others are wrong:**
- MaxTRESPerUser limits per-user, not total account usage. One marketing user could use 4 GPUs, blocking all others. Also, assigning QOS to account affects all users equally, no fair-share between users.
- GrpTRES is correct for account limits, but 'sacctmgr modify cluster set FairShare=1' is invalid syntax. Fair-share is configured in slurm.conf with PriorityType=priority/multifactor and requires per-account FairShare values.
- GrpTRES belongs on accounts, not QOS. Priority on QOS sets static priority, not dynamic fair-share. 'sacctmgr add user dept=marketing' is invalid—users are added to accounts, not filtered by department field.

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q4.** During a maintenance window, an admin needs to drain node12 (currently running 3 jobs) and node13 (idle). After maintenance, both nodes must return to service automatically when jobs finish. What is the correct sequence of scontrol commands?

A) scontrol update NodeName=node12,node13 State=DOWN Reason='Maintenance'; wait for jobs to finish; scontrol update NodeName=node12,node13 State=RESUME
B) scontrol update NodeName=node12 State=DRAIN Reason='Maintenance'; scontrol update NodeName=node13 State=DOWN Reason='Maintenance'; after maintenance, scontrol update NodeName=node12,node13 State=IDLE
C) scontrol update NodeName=node12,node13 State=DRAIN Reason='Maintenance'; nodes auto-resume when jobs complete, no further action needed
D) scontrol update NodeName=node12,node13 State=DRAIN Reason='Maintenance'; after maintenance, scontrol update NodeName=node12,node13 State=RESUME

<details>
<summary>Answer</summary>

**Correct: D**

State=DRAIN prevents new jobs from starting but allows running jobs to complete—perfect for node12 with active jobs. node13 goes DRAIN immediately (it's idle). After maintenance, State=RESUME clears the DRAIN flag and returns both to IDLE/ALLOCATED state. This is the standard maintenance workflow.

**Why the others are wrong:**
- State=DOWN kills running jobs immediately—data loss for the 3 jobs on node12. DOWN is for emergency hardware failures, not planned maintenance.
- node13 should also be DRAIN (not DOWN) unless hardware is failed. State=IDLE is not valid with scontrol update—nodes transition to IDLE automatically when RESUME is issued and no jobs are running.
- DRAIN nodes do NOT auto-resume. They stay DRAINED until explicitly set to RESUME. Jobs complete, but node remains unavailable for new work—a common mistake causing nodes to stay offline after maintenance.

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q5.** A company allocates GPU hours quarterly: each user gets 1000 GPU-hours, unused hours don't roll over. The admin must enforce this with Slurm accounting and prevent users from exceeding their quota. Which configuration is correct?

A) Create QOS with MaxTRESMins=gres/gpu=60000 (1000 hours × 60 min); reset with 'sacctmgr modify qos normal set MaxTRESMins=gres/gpu=60000' quarterly
B) Configure MaxWall=1000:00:00 per user in QOS; track usage with 'sreport cluster AccountUtilizationByUser Start=2026-01-01 End=2026-03-31'
C) Set GrpTRESRunMins=gres/gpu=60000 on each account; reset quarterly by deleting and recreating accounts with 'sacctmgr delete account' and 'sacctmgr add account'
D) Use GrpTRESMins=gres/gpu=60000 on the user account; run 'sacctmgr modify user name=alice set GrpTRESMins=gres/gpu=60000' quarterly for each user

<details>
<summary>Answer</summary>

**Correct: D**

GrpTRESMins on user account enforces total accumulated GPU-minutes over time (60000 min = 1000 hours). Once a user consumes 1000 GPU-hours, new jobs are held. Quarterly reset via 'sacctmgr modify user' updates the limit. This is the standard approach for time-based GPU quotas.

**Why the others are wrong:**
- MaxTRESMins limits the duration of a SINGLE job, not cumulative usage. A user could submit 100 jobs of 10 hours each and exceed 1000 total hours.
- MaxWall limits individual job walltime, not cumulative hours. sreport only reports usage; it doesn't enforce limits. User could run unlimited jobs under 1000 hours each.
- GrpTRESRunMins limits CONCURRENT running GPU-minutes (e.g., max 10 GPUs running at once), not cumulative over quarter. Deleting accounts loses historical accounting data and disrupts users.

</details>

---

**Q6.** An admin runs 'squeue -o %Q' and sees job priorities: job1=50000, job2=120000, job3=85000. Job2 is from a new user who has never run jobs; job1 and job3 are from heavy users. The slurm.conf has PriorityWeightAge=1000, PriorityWeightFairshare=10000, PriorityWeightJobSize=500. Job2 has been pending for 5 minutes. What is the PRIMARY contributor to job2's high priority?

A) PriorityWeightAge: job2 has been pending longer than other jobs in the queue
B) PriorityWeightJobSize: job2 requests more GPUs than job1 and job3, increasing priority
C) PriorityWeightFairshare: new user has high fair-share value (near 1.0) because they haven't consumed resources
D) Job2's QOS has Priority=100000 set, overriding multifactor priority calculation

<details>
<summary>Answer</summary>

**Correct: C**

PriorityWeightFairshare=10000 is 10× higher than Age (1000) and 20× higher than JobSize (500), making fair-share the dominant factor. A new user has fair-share near 1.0 (perfect share), contributing ~10000 priority points. Heavy users have low fair-share (<0.5), contributing <5000 points. The 70000-point gap (120000 vs 50000) is primarily from fair-share.

**Why the others are wrong:**
- Age contributes PriorityWeightAge × (minutes_pending / PriorityMaxAge). At 5 minutes and PriorityMaxAge default (7 days), Age contributes ~5 points—negligible compared to 70000-point gap.
- JobSize contributes PriorityWeightJobSize × (requested_cpus / total_cpus). Even requesting 100% of cluster resources only adds 500 points, far less than fair-share contribution.
- QOS Priority adds a fixed value to total priority but doesn't replace multifactor. If QOS Priority=100000, job2 priority would be ~110000+ (100000 + multifactor), not exactly 120000. Fair-share explains the observed value.

</details>

---

**Q7.** A cluster has 8 nodes: 4×A100 (80GB each), 4×H100 (80GB each). The admin creates partitions: 'partition=a100_partition Nodes=node[01-04] Default=NO' and 'partition=h100_partition Nodes=node[05-08] Default=YES'. A user submits 'sbatch --partition=a100_partition --gres=gpu:2 train.sh' but the job stays PENDING with 'Resources'. The gres.conf has 'NodeName=node[01-04] Name=gpu Type=a100 File=/dev/nvidia[0-3] Count=4'. What is missing?

A) The slurm.conf must include 'GresTypes=gpu' before Slurm can allocate gres resources
B) The partition must be set to State=UP; run 'scontrol update PartitionName=a100_partition State=UP'
C) The user must specify the GPU type explicitly: 'sbatch --partition=a100_partition --gres=gpu:a100:2 train.sh'
D) The partition configuration must include 'DefMemPerGPU=80000' to match the A100 80GB memory

<details>
<summary>Answer</summary>

**Correct: A**

GresTypes=gpu in slurm.conf is MANDATORY—it tells Slurm to enable gres scheduling. Without this line, Slurm ignores gres.conf entirely and cannot allocate GPUs, even if gres.conf is correct. This is the #1 mistake when setting up GPU clusters. After adding GresTypes=gpu, restart slurmctld.

**Why the others are wrong:**
- Partitions are State=UP by default when created. State=DOWN would show 'PartitionDown' error, not 'Resources'. The partition is UP but gres scheduling is disabled globally.
- Specifying 'gpu:a100:2' is optional; 'gpu:2' works if partition only has one GPU type. The real issue is GresTypes=gpu is missing, so Slurm doesn't recognize gres requests at all.
- DefMemPerGPU is for memory scheduling optimization, not gres functionality. Jobs can run without it; Slurm just won't track GPU memory as a schedulable resource.

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q8.** An admin needs to configure a partition for spot/preemptible workloads where jobs can be killed at any time, and a partition for guaranteed workloads. Spot partition should have 10× lower priority but access to all nodes. Which slurm.conf entries are correct?

A) PartitionName=spot Nodes=node[01-32] Priority=100 PreemptMode=REQUEUE Default=NO; PartitionName=guaranteed Nodes=node[01-32] Priority=1000 PreemptMode=OFF Default=YES
B) PartitionName=spot Nodes=node[01-32] Priority=100 Shared=FORCE:1 Default=NO; PartitionName=guaranteed Nodes=node[01-32] Priority=1000 Shared=NO Default=YES; PreemptType=preempt/qos in global config
C) PartitionName=spot Nodes=node[01-32] PriorityTier=1 OverSubscribe=YES Default=NO; PartitionName=guaranteed Nodes=node[01-32] PriorityTier=2 OverSubscribe=NO Default=YES; enable preemption with PreemptType=preempt/partition_prio
D) PartitionName=spot Nodes=node[01-32] QOS=spot_qos Default=NO; PartitionName=guaranteed Nodes=node[01-32] QOS=guaranteed_qos Default=YES; configure QOS with 'sacctmgr add qos spot_qos Preempt=guaranteed_qos Priority=100'

<details>
<summary>Answer</summary>

**Correct: C**

PriorityTier is the partition-level priority (higher tier = higher priority). PreemptType=preempt/partition_prio enables partition-based preemption: jobs in tier 2 (guaranteed) preempt tier 1 (spot). Both partitions can share nodes (OverSubscribe=YES on spot allows packing). This is the standard setup for spot/guaranteed workloads.

**Why the others are wrong:**
- Priority in partition config is deprecated (replaced by PriorityTier). PreemptMode is a QOS/job setting, not partition setting. PreemptMode=REQUEUE on partition is invalid syntax.
- Shared=FORCE:1 limits jobs per node (resource management), not preemption. PreemptType=preempt/qos requires QOS-based preemption setup, but no QOS is assigned to partitions here—won't enable preemption.
- QOS assigned to partitions is non-standard (QOS is assigned to users/accounts). The example is conceptually right but 'sacctmgr add qos spot_qos Preempt=guaranteed_qos' is backwards—guaranteed_qos should preempt spot_qos, not vice versa. Also, partition-level QOS doesn't integrate with PreemptType cleanly.

</details>

---

