# BCM Advanced Operations Cheat Sheet

## Firmware Management

| Component | Update Command | Rollback Flag | Notes |
|-----------|---------------|---------------|-------|
| GPU | `cmsh device update firmware -c <cat>` | `--backup` | Exam tests rollback capability |
| BMC | `cmsh device update bmc --version <ver>` | `--verify-ipmi` | Check IPMI before update |
| NIC | `cmsh device update nic --sync --version <ver>` | N/A | Use --sync for version alignment |

**Key Points:**
- Category-based updates (`-c <category>`) are mandatory for exam
- `--backup` flag creates firmware rollback point
- `--skip-unreachable` prevents failures on offline nodes
- Default timeout: 30 seconds (configurable in bcm.conf)

## InfiniBand Configuration

| Task | Command/Config | Common Issue |
|------|----------------|---------------|
| Subnet Manager | Configured in BCM network policies | Multiple SMs → Polling state |
| Link Status | `ibstat` on node | Physical state: Polling = SM issue |
| Fabric Verify | `ibdiagnet` | Run from SM node only |

**Exam Gotchas:**
- "Physical state: Polling" = SM configuration problem (not cable)
- Only ONE active opensm per fabric
- BCM network policies define SM node and priority

## DPU Management (BlueField)

```bash
# Install DOCA Services workflow:
1. Create DPU software image in BCM repository
2. Assign image to node category with DPUs
3. cmsh device provision -c <dpu_category>
```

**Key Facts:**
- DPUs are provisionable devices (like nodes) in BCM
- DOCA Services installed on DPU Arm OS, not x86 host
- Used for DPU-accelerated storage (NVMe-oF) and networking

## Reporting & Monitoring

| Report Type | Command | Metrics |
|-------------|---------|----------|
| Usage | `cmsh report generate --type usage` | GPU util, power, jobs |
| Performance | `--metrics gpu_util,power,jobs` | Historical aggregation |
| Period | `--period monthly/weekly/daily` | Time range |

**vs. nvidia-smi:**
- `nvidia-smi`: Real-time, single node
- `cmsh report`: Historical, cluster-wide, job correlation

## Troubleshooting Commands

```bash
# Health checks
cmsh show health              # Summary
cmsh show health -v           # Verbose (daemon status)

# Provisioning failures
# Check these in order:
1. /var/log/bcm/provisioning.log   # BCM provisioning logs
2. /var/log/messages               # DHCP/PXE logs
3. cmsh show node <name>           # Node network config
4. Repository connectivity from failing nodes

# Agent recovery
cmsh device execute -c <cat> 'systemctl restart bcm-agent'

# Image sync issues
# Verify: image repo connectivity, node network config, DHCP/PXE logs
```

## Common Error Messages

| Error | Meaning | Fix |
|-------|---------|-----|
| `Image sync failed: connection timeout` | Node can't reach repository | Check node network (DHCP/PXE logs) |
| `bcm-agent: CRITICAL - last heartbeat X sec ago` | Agent daemon down | systemctl restart bcm-agent |
| `NIC firmware: NOT_SYNCED` | NIC firmware version mismatch | device update nic --sync |
| `provisioning failed: IPMI not accessible` | BMC unreachable | Verify BMC IP, IPMI credentials |

## Service Management

```bash
# BCM server (head node)
systemctl restart bcm-server
systemctl status bcm-server

# BCM agent (compute nodes)
systemctl restart bcm-agent   # On individual node
cmsh device execute -c all 'systemctl restart bcm-agent'  # Cluster-wide
```

## Exam-Tested Numbers

- **Default image repo timeout:** 30 seconds
- **Agent heartbeat interval:** ~60 seconds (CRITICAL if >180s)
- **Subnet Manager:** Exactly 1 active per InfiniBand fabric
- **Firmware rollback:** Enabled with `--backup` flag

## Troubleshooting Decision Tree

**Provisioning Failure:**
1. Repository reachable from head node? → Yes: Check node network
2. Node network OK? → Yes: Check image exists in repo
3. Image exists? → Yes: Check IPMI accessibility

**IB Link Down (Polling state):**
1. Check opensm running: `systemctl status opensm`
2. Verify only 1 active SM in fabric
3. Check BCM network policy SM configuration

**Health Degraded:**
1. Run `cmsh show health -v` for daemon status
2. If bcm-agent CRITICAL → restart agent on affected nodes
3. If persistent → check /var/log/bcm/agent.log
