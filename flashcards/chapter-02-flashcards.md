## Chapter 2: Workload Manager Integration: Slurm and Kubernetes on BCM — Flashcards

| # | Front (Question) | Back (Answer) |
|---|-----------------|---------------|
| 1 | Slurm gres.conf GPU definition syntax for 8x A100 GPUs | Name=gpu Type=a100 File=/dev/nvidia[0-7] Exam tip: Name must be 'gpu', Type is GPU model, File lists device files (no Count parameter exists) |
| 2 | BCM Slurm integration: what database connection is required? | MySQL/MariaDB connection to slurmdbd accounting database. Configure in BCM integrations with host:port, database name, credentials. Exam tip: BCM reads directly from slurmdbd database, no plugin installation needed on compute nodes |
| 3 | NVIDIA GPU Operator: what components does it deploy? | Device plugin (GPU resource exposure), container toolkit, GPU drivers, DCGM exporter (monitoring), node-feature-discovery. Exam tip: Single Helm chart installs everything needed for GPU workloads — no manual nvidia-docker2 needed |
| 4 | Slurm slurm.conf NodeName entry: required parameters | NodeName, CPUs, Gres (with Type like gpu:a100:8), RealMemory, State=UNKNOWN. Exam tip: Gres must include GPU type when multiple types exist; State=UNKNOWN for initial config before node registration |
| 5 | Kubernetes control plane default API server port | 6443 (kube-apiserver). Exam tip: 'Port already in use' error → previous K8s install exists, run 'kubeadm reset' before reinit |
| 6 | Run:ai installation sequence on existing K8s cluster | 1) Install control plane via Helm (runai-cluster chart), 2) Register cluster in Run:ai SaaS UI, 3) Apply generated cluster YAML. Exam tip: GPU Operator must be installed first; registration happens after control plane install |
| 7 | Slurm gres.conf location and which node reads it | /etc/slurm/gres.conf on controller node, read by slurmctld. Exam tip: Controller reads gres.conf; compute nodes reference via slurm.conf Gres= parameter, don't need local gres.conf |
| 8 | Verify GPU device plugin running in Kubernetes | kubectl get nodes -o json \| jq '.items[].status.capacity' → check for 'nvidia.com/gpu: "N"'. Exam tip: Node capacity shows GPU count only if device plugin successfully registered GPUs with scheduler |
| 9 | Slurm job pending with '(Resources)' and 'no gres.conf file' in logs | Missing /etc/slurm/gres.conf on controller. Create file with GPU definitions (Name=gpu Type=... File=...), restart slurmctld. Exam tip: Error in controller logs = controller-side config issue, not compute node |
| 10 | NVIDIA Container Toolkit vs nvidia-docker2 | Container Toolkit is current (used by GPU Operator). nvidia-docker2 is deprecated. Exam tip: GPU Operator automates Container Toolkit install — never manually install nvidia-docker2 |
| 11 | Slurm NodeName State parameter: initial value for new nodes | State=UNKNOWN (before slurmd registers). Exam tip: State=IDLE is wrong for initial config — nodes auto-transition to IDLE after slurmd starts and registers |
| 12 | kubeadm reset command purpose | Removes all Kubernetes components, resets iptables, cleans /etc/kubernetes, releases port 6443. Exam tip: Use before reinitializing control plane if 'port already in use' error occurs |
