# Chapter 4 Cheat Sheet: Slurm Cluster Administration for AI Workloads

## Node State Management

| State | Meaning | Use Case | Command |
|-------|---------|----------|----------|
| **IDLE** | Available, no jobs running | Normal operation | Auto-set by Slurm |
| **ALLOCATED** | Running jobs | Normal operation | Auto-set by Slurm |
| **DRAIN** | No new jobs, current jobs finish | Planned maintenance | `scontrol update NodeName=node01 State=DRAIN Reason='Maintenance'` |
| **DOWN** | Offline, kills running jobs | Hardware failure | `scontrol update NodeName=node01 State=DOWN Reason='PSU failed'` |
| **RESUME** | Return to service after DRAIN/DOWN | Post-maintenance | `scontrol update NodeName=node01 State=RESUME` |

**Exam Tip:** DRAIN preserves jobs, DOWN kills them. Always use DRAIN for maintenance.

---

## Partition Configuration

```bash
# slurm.conf examples
PartitionName=a100_partition Nodes=node[01-04] Default=NO PriorityTier=2
PartitionName=h100_partition Nodes=node[05-08] Default=YES PriorityTier=1
PartitionName=spot Nodes=node[01-08] PriorityTier=1 OverSubscribe=YES
```

**Key Parameters:**
- `PriorityTier`: Higher = higher priority (tier 2 preempts tier 1)
- `Default=YES`: Jobs without --partition go here
- `OverSubscribe=YES`: Multiple jobs can share nodes
- `Nodes=node[01-04]`: Node range for partition

**Exam Trap:** Partitions can overlap nodes (e.g., spot + guaranteed on same hardware).

---

## GPU Resource Configuration (gres.conf)

```bash
# Standard GPU config
NodeName=node[01-04] Name=gpu Type=a100 File=/dev/nvidia[0-3] Count=4

# MIG configuration (A100-80GB, 7× 1g.10gb slices)
NodeName=node01 Name=gpu Type=a100-1g.10gb File=/dev/nvidia0 Cores=0-7 Count=7
```

**Critical:** Must set `GresTypes=gpu` in slurm.conf BEFORE gres.conf works.

**MIG Workflow:**
1. Create MIG instances: `nvidia-smi mig -cgi 1g.10gb -C` (repeat 7×)
2. Declare in gres.conf: `Count=7`
3. Restart slurmd on node
4. Request: `sbatch --gres=gpu:a100-1g.10gb:2 job.sh`

**Exam Note:** gres.conf DECLARES MIG slices, doesn't CREATE them.

---

## QOS (Quality of Service) Policies

```bash
# Create QOS with GPU limits
sacctmgr add qos interactive_qos
sacctmgr modify qos interactive_qos set Priority=10000 MaxWall=04:00:00 MaxTRESPerUser=gres/gpu=2

# Create QOS with cumulative GPU-hour quota
sacctmgr add qos training_qos
sacctmgr modify qos training_qos set Priority=5000 GrpTRESMins=gres/gpu=60000
```

**QOS Limit Types:**

| Limit | Scope | Example Use |
|-------|-------|-------------|
| **MaxTRESPerUser** | Per-user concurrent | Max 4 GPUs per user at once |
| **GrpTRES** | Account/QOS total concurrent | Max 10 GPUs for entire team |
| **GrpTRESMins** | Cumulative over time | 1000 GPU-hours/quarter (60000 mins) |
| **MaxWall** | Single job time limit | Max 4-hour interactive jobs |
| **Priority** | Job priority boost | Interactive=10000, batch=5000 |

**Exam Key:** GrpTRESMins enforces quotas, GrpTRES enforces concurrency.

---

## Accounting Setup (sacctmgr)

```bash
# Add accounts
sacctmgr add account ml_team Cluster=ai-cluster
sacctmgr add account research Cluster=ai-cluster

# Set account GPU limits
sacctmgr modify account ml_team set GrpTRES=gres/gpu=8 FairShare=100

# Add users to accounts
sacctmgr add user alice Account=ml_team
sacctmgr add user bob Account=research

# Set user-specific limits
sacctmgr modify user alice set GrpTRESMins=gres/gpu=60000
```

**Fair-Share Configuration (slurm.conf):**
```bash
PriorityType=priority/multifactor
PriorityWeightFairshare=10000  # Dominant factor
PriorityWeightAge=1000
PriorityWeightJobSize=500
```

**Priority Calculation:** `Total = (FairShare×10000) + (Age×1000) + (JobSize×500) + QOS_Priority`

---

## Monitoring and Debugging

```bash
# Check job priority breakdown
squeue -o "%.18i %.9P %.8j %.8u %.10Q %.10Y %.10S %.10L"
# %Q = Priority, %Y = Nice, %S = Start time, %L = Time left

# See why job is pending
squeue -j 12345 --start  # Shows expected start time and blocking reason

# Check node gres availability
sinfo -o "%N %.6D %.6t %.14C %.8z %.6m %.8d %.6w %.8f %20G"
# %G = Gres (shows available GPUs)

# View account usage
sreport cluster AccountUtilizationByUser Start=2026-01-01 End=2026-03-31
```

**Common PENDING Reasons:**
- `Resources`: Not enough GPUs/CPUs available
- `Priority`: Higher priority jobs blocking
- `ReqNodeNotAvail`: Node DRAIN/DOWN or gres mismatch
- `QOSMaxGRESPerUser`: User hit QOS GPU limit
- `AssocGrpGRES`: Account hit GrpTRES limit

---

## Preemption Setup

```bash
# slurm.conf for partition-based preemption
PreemptType=preempt/partition_prio
PreemptMode=REQUEUE  # or CANCEL

PartitionName=guaranteed PriorityTier=2  # Preempts tier 1
PartitionName=spot PriorityTier=1
```

**QOS-based preemption:**
```bash
sacctmgr add qos high_priority Preempt=low_priority Priority=10000
sacctmgr add qos low_priority Priority=1000
```

**Exam:** PriorityTier (partition) vs Preempt/Priority (QOS)—know both methods.

---

## Critical Numbers for Exam

- **A100-80GB MIG:** 7× 1g.10gb or 2× 3g.40gb max slices
- **Fair-share range:** 0.0 (heavy user) to 1.0 (new user)
- **Default PriorityMaxAge:** 7 days (10080 minutes)
- **GPU hour quota example:** 1000 hours = 60000 minutes (GrpTRESMins)
- **Partition PriorityTier:** Higher number = higher priority (opposite of nice values)

---

## Exam Gotchas

1. **GresTypes=gpu missing** → gres requests ignored, jobs PENDING forever
2. **MIG slices declared but not created** → ReqNodeNotAvail error
3. **DRAIN vs DOWN** → DRAIN for maintenance (preserves jobs), DOWN kills jobs
4. **GrpTRESMins vs MaxTRESMins** → Grp=cumulative account total, Max=per-job limit
5. **State=RESUME required** → DRAIN nodes don't auto-return, must explicitly RESUME
6. **Fair-share needs PriorityType=priority/multifactor** → Default PriorityType doesn't use fair-share
