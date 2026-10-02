# Base Command Manager: Installation & Cluster Initialization Cheat Sheet

## BCM Architecture Components

| Component | Purpose | Key Details |
|-----------|---------|-------------|
| Head Node | BCM services, database, scheduler | Single point of failure unless HA configured; runs PostgreSQL, Slurm/PBS, Base View web service |
| Management Network | Control plane communication | Isolated VLAN; handles cmsh commands, monitoring data, image sync; NOT for job data |
| Compute Nodes | GPU/CPU workload execution | Report to head node via heartbeat; run DCGM for GPU telemetry; receive images via sync |

## Installation Prerequisites

- NVIDIA-certified server for head node (required for support)
- PostgreSQL 12+ installed and running before BCM init
- Management network configured with static IPs
- NTP synchronized across all nodes
- DNS or /etc/hosts with all node hostnames

## Core cmsh Commands

| Task | Command | Notes |
|------|---------|-------|
| Add node | `device add <name> ip=<ip>` | Must specify IP; MAC optional |
| List devices | `device list -s` | Shows status, heartbeat, connection state |
| Create category | `category add <name>` | Logical grouping for scheduling |
| Assign to category | `category use <name>; device assign <range>` | Use bracket notation: gpu[001-032] |
| Sync software | `device sync <node>` | Pushes assigned image to node |
| Update firmware | `device update <node>` | Applies firmware from image; may require reboot |
| Show node details | `device show <name>` | Full config including image, category, status |

## Node Status Values

| Status | Meaning | Common Cause |
|--------|---------|-------------|
| ready | Accepting jobs | Normal operational state |
| unknown | No heartbeat | Network issue, power loss, node crash |
| not ready | Responsive but not accepting jobs | Maintenance mode, health check failed, initializing |
| provisioning | Installing image | Sync or update in progress |

## User Roles & Permissions

| Role | Can Do | Cannot Do |
|------|--------|----------|
| admin | Provision nodes, create users, modify cluster config, view all jobs | (full access) |
| operator | View/manage all jobs, monitor nodes, limited config changes | Add/remove nodes, create users, change network settings |
| user | Submit own jobs, view own job status, SSH to compute nodes for own jobs | View others' jobs, modify cluster, provision nodes |

## Base View Dashboard Metrics

**GPU Telemetry (requires DCGM running on compute nodes):**
- GPU utilization (%)
- Power consumption (watts)
- Temperature (°C)
- Memory utilization (%)
- Update frequency: 30-60 seconds

**Node Health:**
- Node status (ready/unknown/not ready)
- CPU utilization
- Memory usage
- Network throughput

**Job Metrics:**
- Running jobs per node
- Queue depth
- Job start/end times

## Software Image Workflow

1. **Create image:** Define OS, drivers, apps, firmware version
2. **Assign to nodes:** `device use <node>; image assign <image_name>`
3. **Sync to apply:** `device sync <node>` (software) or `device update <node>` (firmware)
4. **Verify:** `device show <node>` confirms active image

## Node Categories for Workload Targeting

**Purpose:** Organize nodes by GPU type, purpose, or generation for scheduler partitioning

**Example:**
```bash
category add a100-training
category use a100-training
device assign gpu[001-048]

category add h100-inference  
category use h100-inference
device assign gpu[049-096]
```

**Scheduler integration:** Configure Slurm partitions or PBS queues to map to BCM categories

## Common Installation Issues

| Error | Cause | Fix |
|-------|-------|-----|
| Database connection timeout | PostgreSQL not running | Start postgresql service before BCM init |
| Node shows 'unknown' | No network heartbeat | Check management network, node power, cmsh device list -s |
| GPU metrics show N/A | DCGM not running on node | Restart dcgm service: `systemctl restart nvidia-dcgm` |
| Image sync fails | Network MTU mismatch or firewall | Verify management network connectivity, check iptables |

## Exam-Critical Facts

- **BCM database:** PostgreSQL (not MySQL/MariaDB)
- **Default user role permissions:** User role CANNOT see other users' jobs or modify cluster
- **Node categories:** Enable scheduler partitioning; purely logical (not network-based)
- **device update vs sync:** update = firmware/BIOS; sync = software image (OS/apps)
- **Base View GPU metrics:** Require NVIDIA DCGM running on each compute node
- **Head node HA:** Not automatic; requires separate HA configuration
- **Certified hardware:** Required for head node support; compute nodes more flexible
