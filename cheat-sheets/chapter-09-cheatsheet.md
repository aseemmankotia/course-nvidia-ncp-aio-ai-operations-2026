# NCP-AIO Exam Simulation Cheat Sheet

## Time Management

| Exam Section | Time Allocation | Strategy |
|--------------|----------------|----------|
| 30 MCQs | 40 minutes (1.3 min/question) | Finish early if possible—extra time rolls to labs |
| 3 Hands-on Labs | 80 minutes (25-30 min/lab) | Budget 25 min/lab, 5 min total buffer |
| Total | 120 minutes | MCQ time doesn't roll back; prioritize completion |

**Golden Rule**: If at 50 minutes you've finished MCQs, you have 70 minutes for labs. If at 50 minutes you've done 20 MCQs, you're 10 minutes behind—speed up.

## MCQ Strategy

| Tactic | Approach |
|--------|----------|
| **Flag Uncertain** | Mark questions you narrow to 2 options; return if time permits |
| **No Penalty** | Answer every question (no penalty for guessing); never leave blank |
| **BEST vs CORRECT** | BEST = most complete solution; CORRECT = only accurate option |
| **Educated Guessing** | Eliminate obviously wrong answers first, then choose based on partial knowledge |
| **Time per Question** | Average 1.3 min; if spending >2 min, flag and move on |

### Common MCQ Traps

| Trap | Wrong Answer | Correct Answer | Why |
|------|--------------|----------------|-----|
| BCM vs Mission Control | Mission Control (fleet ops) | Base Command Manager (job scheduling + quotas + RBAC) | Cluster workload management = BCM |
| MIG vs Fractional GPU | Fractional GPU (time-slicing) | MIG (hardware partitioning for isolation) | Security/isolation requirement = MIG |
| Fabric Manager vs NVSwitch NVOS | NVSwitch NVOS (runs on switch) | Fabric Manager (manages NVLink topology) | 'Component responsible' = Fabric Manager |
| Run:ai Quotas | Node GPU count limits user | Project quota limits user | Quotas enforce at project level, not node |

## Lab Execution Strategy

### Prioritization
1. **Scan all tasks** in the lab (30 seconds)
2. **Identify easiest tasks** (config file edits, simple commands)
3. **Complete easy tasks first** (guaranteed partial credit)
4. **Attempt complex tasks** if time remains
5. **Move to next lab** if stuck >5 minutes on one task

### Using Documentation Efficiently

| Resource | When to Use | Example |
|----------|-------------|----------|
| `kubectl --help` | Quick syntax check | `kubectl create --help` for resource creation |
| `kubectl explain` | Field structure/syntax | `kubectl explain pod.spec.containers.resources` for GPU syntax |
| `man <command>` | Slurm command options | `man sinfo` for partition verification flags |
| BCM Docs | Multi-step procedures | Node provisioning workflow, partition creation |

**Time Rule**: If lookup takes >2 minutes, skip task and return later. Don't read docs linearly—search for exact command/config.

## Verification Checklist

### Slurm Tasks
| Task | Verification Command | Expected Output |
|------|---------------------|------------------|
| Partition created | `sinfo -o '%P %D %G'` | Partition name, node count, GRES |
| GPU GRES configured | `scontrol show node <name> \| grep Gres` | `Gres=gpu:8` (or specified count) |
| Job submitted | `squeue` or `sacct` | Job ID listed, state=RUNNING or COMPLETED |

### Kubernetes Tasks
| Task | Verification Command | Expected Output |
|------|---------------------|------------------|
| Pod deployed | `kubectl get pods` | STATUS=Running |
| GPU allocated | `kubectl describe pod <name>` | Limits: nvidia.com/gpu: 2 |
| Node GPU capacity | `kubectl get nodes -o json \| grep nvidia.com/gpu` | Capacity/allocatable values |

### BCM Tasks
| Task | Verification Command | Expected Output |
|------|---------------------|------------------|
| Node provisioned | BCM UI: Nodes tab, or API call | Node status=Ready |
| Workload deployed | BCM UI: Workloads tab | Workload status=Running |

**Verification Rule**: Spend 30 seconds verifying each task. If output matches requirements, submit and move on. Don't over-verify.

## Handling Lab Failures

| Scenario | Action | Time Limit |
|----------|--------|------------|
| Command returns error | Check syntax with `--help`, retry once | 1 minute |
| Pod pending | `kubectl describe pod`, check events | 2 minutes |
| Stuck on task 2 of 4 | Move to task 3/4, return if time permits | 5 minutes max per task |
| Can't find documentation | Use command help (`kubectl explain`, `man`), skip if >2 min | 2 minutes |
| Lab environment slow | Continue with next task, report issue post-exam | N/A (no time extension) |

**Partial Credit Rule**: Labs award partial credit per task. One fully completed task > two half-done tasks.

## Final 15 Minutes Strategy

| Time Remaining | Action |
|----------------|--------|
| 15 min, 1 lab left (4 tasks) | Scan tasks, complete 2 easiest tasks fully |
| 10 min, MCQs done, 1 lab left | Attempt easiest lab task, verify, submit |
| 5 min, lab in progress | Finish current task, verify, submit (don't start new task) |
| <3 min | Submit all work immediately; don't start anything new |

**Never**: Leave tasks unsubmitted. Partial/unverified work > no submission.
