## Chapter 9: Exam Simulation and Test-Taking Strategy — Practice Questions

**Q1.** You have 120 minutes for the NCP-AIO exam consisting of 30 MCQs and 3 hands-on labs. After spending 50 minutes on MCQs, you have completed 2 labs in 55 minutes total. You have 15 minutes remaining and one lab with 4 tasks left. Which approach BEST maximizes your score?

A) Return to review all flagged MCQ questions first, then attempt the final lab with remaining time
B) Attempt all 4 tasks in the final lab sequentially, submitting whatever is completed when time expires
C) Request a time extension from the proctor due to lab environment performance issues
D) Quickly scan all 4 lab tasks, complete the 2 easiest tasks fully with verification, skip the others

<details>
<summary>Answer</summary>

**Correct: D**

With only 15 minutes left and 4 tasks remaining, completing 2 tasks fully with proper verification yields guaranteed partial credit. Labs award partial credit per task completed correctly. Attempting all 4 tasks risks incomplete/unverified work that may score zero.

**Why the others are wrong:**
- MCQs are already submitted (40-minute window is past), and changing answers rarely improves scores when rushed
- Sequential completion risks running out of time with all tasks incomplete or unverified, resulting in zero credit for the entire lab
- Time extensions are not granted for normal exam conditions; lab performance issues must be documented during the exam window

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q2.** During the exam, a lab task requires configuring GPU resource limits in Kubernetes. You are unsure of the exact syntax for nvidia.com/gpu resource requests. Which action is MOST efficient?

A) Search the internet using the exam workstation browser to find GPU resource configuration examples
B) Skip this task and return to it after completing other labs, hoping to remember the syntax
C) Run 'kubectl explain pod.spec.containers.resources' to view the resource field structure and syntax
D) Use 'nvidia-smi' output to determine GPU count and manually write the YAML from memory

<details>
<summary>Answer</summary>

**Correct: C**

'kubectl explain' provides immediate, exam-environment documentation of resource field structure and accepted values (including nvidia.com/gpu syntax). This is faster than external searching and uses permitted documentation.

**Why the others are wrong:**
- Internet access is restricted during the exam; only specified NVIDIA documentation (BCM docs, man pages, kubectl help) is accessible
- Skipping wastes the current context you have loaded; syntax lookup takes 30 seconds vs. potentially forgetting to return
- nvidia-smi shows available GPUs but doesn't provide Kubernetes YAML syntax; manual memory is error-prone under time pressure

</details>

---

**Q3.** An MCQ asks: 'Which component is responsible for GPU-to-GPU communication in a DGX SuperPOD?' Options include NVSwitch NVOS, Fabric Manager, NCCL, and InfiniBand HCAs. You are uncertain between Fabric Manager and NVSwitch NVOS. What is the BEST approach?

A) Select NVSwitch NVOS because it is the operating system running on NVSwitch ASICs for packet routing
B) Flag the question, continue with remaining questions, and return if time permits to reconsider
C) Eliminate NCCL and InfiniBand first, then guess randomly between Fabric Manager and NVSwitch NVOS
D) Select Fabric Manager because it manages NVLink fabric topology and is the userspace control layer

<details>
<summary>Answer</summary>

**Correct: D**

Fabric Manager is the userspace daemon that manages NVLink topology and enables GPU-to-GPU communication. This is a 'which component is responsible' question (not 'which software runs on the switch'), making Fabric Manager correct. When you have domain knowledge to differentiate, use it rather than flagging.

**Why the others are wrong:**
- NVSwitch NVOS runs on the switch hardware but is not the 'component responsible' from a system architecture perspective; this is a common trap
- Flagging wastes the mental work you've already done narrowing to two options; if you have reasoning (Fabric Manager = userspace control), commit
- Random guessing ignores your knowledge that Fabric Manager manages NVLink fabric; educated guessing based on partial knowledge scores better

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q4.** A lab task states: 'Configure a Slurm partition with 4 nodes, each providing 8 A100 GPUs as a consumable resource.' After creating the partition, which verification command confirms the configuration is correct?

A) scontrol show partition | grep -i gpu
B) sinfo -o '%P %D %G' to display partition name, node count, and GRES (GPU count)
C) sacct --format=Partition,AllocGRES to show GPU allocation history
D) srun --gres=gpu:8 -N 4 nvidia-smi to test GPU access across all nodes

<details>
<summary>Answer</summary>

**Correct: B**

'sinfo -o "%P %D %G"' shows partition name, total node count (should be 4), and GRES (GPU) configuration. This directly verifies the task requirements: 4 nodes and GPU resources configured. Fast and definitive.

**Why the others are wrong:**
- 'scontrol show partition' provides verbose output but doesn't concisely show node count and GRES; requires parsing multiple lines
- 'sacct' shows job history, not current partition configuration; no jobs have run yet, so this provides no useful verification
- 'srun' tests GPU access but requires job scheduling; if allocation fails, you don't know if it's config or scheduler issue; takes longer than sinfo

</details>

---

**Q5.** You complete a lab task to deploy a PyTorch pod in Kubernetes requesting 2 GPUs. The pod shows 'Pending' status. You have 8 minutes left in the lab. Which troubleshooting sequence is MOST time-efficient?

A) Run 'kubectl describe pod <name>' to check events, then 'kubectl get nodes -o json | grep nvidia.com/gpu' to verify GPU availability
B) Delete and recreate the pod with different resource requests, then check if it schedules successfully
C) Run 'nvidia-smi' on all nodes to confirm GPUs are detected, then check pod YAML syntax for errors
D) Review Base Command Manager documentation for Kubernetes GPU scheduling requirements and reconfigure the pod

<details>
<summary>Answer</summary>

**Correct: A**

'kubectl describe pod' immediately shows scheduling failure reasons (e.g., 'Insufficient nvidia.com/gpu'). Checking node GPU capacity with grep confirms if GPUs exist. This is a 30-second diagnosis. With 8 minutes left, you can fix and verify.

**Why the others are wrong:**
- Deleting and recreating without diagnosing wastes time; if root cause is no GPUs available, recreation fails identically
- Running nvidia-smi on multiple nodes is slow (SSH/console access per node); kubectl commands are faster; YAML syntax errors would show in describe events
- Reading documentation takes 3-5 minutes; with 8 minutes left, diagnose first, document-read only if describe output is unclear

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q6.** An MCQ asks: 'A company needs to manage a multi-node GPU cluster with job scheduling, resource quotas, and user access control. Which NVIDIA solution is BEST?' Options: Base Command Manager, Mission Control, Run:ai, DGX OS. You recall Run:ai handles quotas but are unsure if BCM does too. What should you consider?

A) Run:ai is better because it specializes in quota management and resource orchestration for Kubernetes environments
B) Mission Control is best because it provides fleet management and monitoring for multiple DGX systems centrally
C) DGX OS is best because it comes pre-installed on DGX systems and includes all necessary cluster management tools
D) Base Command Manager provides comprehensive job scheduling (Slurm/Kubernetes), resource management, and multi-user access control, making it the best enterprise cluster management solution

<details>
<summary>Answer</summary>

**Correct: D**

The question asks for 'multi-node cluster management' with job scheduling, quotas, AND user access control. BCM provides all three via integrated Slurm/K8s, RBAC, and resource policies. This is the comprehensive solution. Watch for 'BEST' wording—it means 'most complete fit.'

**Why the others are wrong:**
- Run:ai excels at quota/orchestration but typically operates within an existing K8s cluster; it doesn't provide the base job scheduling infrastructure that BCM does
- Mission Control monitors/manages DGX fleets but doesn't provide job scheduling or user access control; it's fleet ops, not workload management
- DGX OS is a node-level OS; it doesn't provide cluster-wide job scheduling or multi-user management without additional software like BCM

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

**Q7.** You have 40 minutes allocated for 30 MCQs. After 25 minutes, you have answered 20 questions and flagged 3 uncertain questions. Which strategy BEST uses the remaining 15 minutes?

A) Answer the remaining 10 questions at normal pace (1 min each), then use 5 minutes to review all 3 flagged questions
B) Speed through the remaining 10 questions (30 sec each), then spend 10 minutes researching flagged questions using kubectl help and documentation
C) Answer the remaining 10 questions at normal pace (1.5 min each), leaving no time for flagged questions, to ensure quality answers
D) Return to the 3 flagged questions immediately, spend 3 minutes each reconsidering them, then rush the final 10 questions in 6 minutes

<details>
<summary>Answer</summary>

**Correct: A**

Maintaining 1 min/question pace for the final 10 ensures quality answers (10 minutes), leaving 5 minutes to reconsider flagged questions. You've already done initial analysis on flagged questions; 5 minutes is enough to apply fresh perspective. This balances completion and review.

**Why the others are wrong:**
- MCQs cannot be researched using lab environment tools (kubectl/docs); MCQ and lab sections are separate; documentation doesn't help MCQ recall
- 1.5 min/question uses all 15 minutes with no review time; flagged questions remain guesses; the goal is to improve flagged answers, not just finish
- Reversing priority means rushing fresh questions (higher error rate) to spend more time on questions you've already analyzed; diminishing returns

</details>

---

**Q8.** A lab verification checklist includes: 'Run sinfo to confirm partition exists, run scontrol show node to verify GPU GRES, submit test job with srun, check job completion with sacct.' You complete the first three steps successfully, but 'sacct' shows no output. You have 5 minutes left. What should you do?

A) Resubmit the test job and wait for sacct to populate, as the job may not have completed when you first checked
B) Mark the task as complete since sinfo and scontrol verified the configuration; the test job is supplementary verification
C) Check 'squeue' to see if the job is still running, and if empty, verify job completion with 'scontrol show job <jobid>' instead of sacct
D) Review Slurm documentation to understand why sacct has no output, then reconfigure Slurm accounting database

<details>
<summary>Answer</summary>

**Correct: B**

The core task is configuration verification. sinfo and scontrol confirm partition and GPU GRES are configured correctly. The test job (srun) completed successfully (you observed this). sacct requires accounting database setup, which may not be enabled. With 5 minutes left, the configuration is verified—submit and move on.

**Why the others are wrong:**
- Resubmitting wastes 2-3 minutes; if sacct has no output, it's likely an accounting setup issue, not job timing; the job already completed via srun
- 'scontrol show job' requires the jobid, which you may not have captured; squeue emptiness means job finished; but you already saw srun complete—don't over-verify
- Reconfiguring Slurm accounting takes 10+ minutes and requires database setup; this is out of scope for a 5-minute buffer; accept the primary verification passed

> ⚠️ Frequently missed on the exam — read the options carefully.

</details>

---

