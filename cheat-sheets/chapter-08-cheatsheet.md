# Chapter 8: Troubleshooting NVIDIA AI Infrastructure — Quick Reference

## Docker & Container Toolkit Troubleshooting

| Error | Cause | Fix |
|-------|-------|-----|
| `nvidia-container-cli: initialization error` | Toolkit not installed or Docker not restarted | `nvidia-ctk --version` → `sudo systemctl restart docker` |
| `could not select device driver "nvidia"` | Missing runtime in daemon.json | Add `{"runtimes":{"nvidia":{"path":"nvidia-container-runtime"}}}` to `/etc/docker/daemon.json` + restart |
| `failed to create shim task` | Docker daemon didn't reload config | `sudo systemctl restart docker` after toolkit install |
| GPU not visible in container | Wrong runtime or --gpus flag missing | Use `docker run --gpus all` or set `"default-runtime":"nvidia"` |

**Key Config:** `/etc/docker/daemon.json`
```json
{
  "default-runtime": "nvidia",
  "runtimes": {
    "nvidia": {
      "path": "nvidia-container-runtime",
      "runtimeArgs": []
    }
  }
}
```

## Fabric Manager (NVSwitch Systems)

| System | NVSwitch | Fabric Manager Required? |
|--------|----------|-------------------------|
| DGX A100 | Yes | **YES** (nvidia-fabricmanager service) |
| DGX H100 | Yes | **YES** |
| HGX A100 | Yes | **YES** |
| PCIe consumer cards (RTX 4090, A6000) | No | No |

**Diagnosis Commands:**
```bash
# Check NVLink status
nvidia-smi nvlink --status
# Link 0: active = good, inactive = check fabric manager

# Check fabric manager service
systemctl status nvidia-fabricmanager
sudo systemctl start nvidia-fabricmanager
sudo systemctl enable nvidia-fabricmanager

# View topology
nvidia-smi topo -m
# NV1-NV12 = NVLink connections, SYS = across sockets
```

## GPUDirect Storage (GDS)

**Requirements:**
1. nvidia-fs kernel module loaded
2. `/etc/cufile.json` configured with mountpoints
3. Supported filesystem (XFS, ext4, not NFS)

**Troubleshooting Workflow:**
```bash
# 1. Verify driver loaded
lsmod | grep nvidia_fs
# If missing: modprobe nvidia_fs

# 2. Check cufile.json exists and has mountpoints
cat /etc/cufile.json
# Must have: "mountpoint": ["/mnt/nvme", ...]

# 3. Test with gdsio
gdsio -f /dev/nvme0n1 -s 1G -w 4 -d 0
# Should show ~10+ GB/s on NVMe. Low BW = config issue

# 4. Check app can use GDS
# Apps must use cuFile API; verify cufile.log for errors
```

**Common Issues:**
- GDS not working but nvidia-fs loaded → check `/etc/cufile.json` mountpoint entries
- Low bandwidth → filesystem not GDS-optimized (use XFS) or wrong mount options
- "Operation not supported" → storage device doesn't support GDS (check vendor compatibility)

## GPUDirect RDMA (NCCL over InfiniBand)

**Requirements:**
1. `nv_peer_mem` kernel module loaded
2. InfiniBand active (ibstatus)
3. NCCL configured to use IB: `NCCL_IB_DISABLE=0` (default)

**Diagnosis Commands:**
```bash
# 1. Check nv_peer_mem module
lsmod | grep nv_peer_mem
# If missing: modprobe nv_peer_mem

# 2. Verify IB port active
ibstatus
# State: Active, Physical state: LinkUp, base lid: 0x1

# 3. Check NCCL will use IB (not disabled)
echo $NCCL_IB_DISABLE
# Empty or 0 = IB enabled. 1 = disabled (forces TCP/IP)

# 4. Run NCCL test with debug
NCCL_DEBUG=INFO ./nccl-tests/build/all_reduce_perf -b 8 -e 128M -f 2 -g 8
# Look for: "Selected NCCL_IB_DISABLE=0" and "Using transport InfiniBand"
```

## NCCL Communication Troubleshooting

| Symptom | Check | Fix |
|---------|-------|-----|
| Hang during all_reduce | NCCL_DEBUG=INFO → look for timeout | Check IB fabric (ibstatus), subnet manager, firewall |
| Falls back to TCP/IP | NCCL logs say "NCCL_IB_DISABLE=1" | Verify nv_peer_mem loaded, IB active |
| Slow multi-node training | Bandwidth test: nccl-tests | Check `nvidia-smi topo -m` for GPU-NIC affinity (NODE vs SYS) |
| "waiting for connections" | Bootstrap can't reach ranks | Firewall blocking ports, wrong network interface |

**Key Environment Variables:**
- `NCCL_DEBUG=INFO` — shows all NCCL decisions (transport, rings, errors)
- `NCCL_IB_DISABLE=0` — enable InfiniBand (default)
- `NCCL_IB_DISABLE=1` — force TCP/IP (for debugging IB issues)
- `NCCL_SOCKET_IFNAME=eth0` — specify network interface if multiple NICs
- `NCCL_IB_HCA=mlx5_0` — specify InfiniBand adapter

**NCCL Test Commands:**
```bash
# Basic all_reduce test (single node, 8 GPUs)
./all_reduce_perf -b 8 -e 128M -f 2 -g 8

# Multi-node (2 nodes, 8 GPUs each)
# Node 0:
NCCL_DEBUG=INFO ./all_reduce_perf -b 8 -e 128M -f 2 -g 8 -n 2 -i 0
# Node 1:
NCCL_DEBUG=INFO ./all_reduce_perf -b 8 -e 128M -f 2 -g 8 -n 2 -i 1
```

## InfiniBand Troubleshooting

**Quick Checks:**
```bash
# Port state
ibstatus
# Must show: State: Active, Physical state: LinkUp

# List IB devices
ibv_devinfo
# Shows: mlx5_0, mlx5_1, etc.

# Check subnet manager
sminfo
# Must have active SM for fabric to work

# Verify RDMA connectivity between nodes
ib_write_bw -d mlx5_0  # server side
ib_write_bw -d mlx5_0 <server_ip>  # client side
# Should show 100+ Gbps on HDR InfiniBand
```

**Common IB Issues:**
- `State: Down` → cable disconnected, switch port down
- No LID assigned → subnet manager not running (start opensm)
- NCCL slow but ibstatus active → check GPU-NIC affinity with `nvidia-smi topo -m` (use closest NIC)

## Exam Focus Areas

1. **Docker errors**: Know that nvidia-container-cli errors = toolkit + Docker restart
2. **Fabric manager**: Required for DGX/HGX NVSwitch, check service status first
3. **GDS**: nvidia-fs + cufile.json with mountpoints; test with gdsio
4. **GPUDirect RDMA**: nv_peer_mem + NCCL_IB_DISABLE=0 + active IB
5. **NCCL debug**: NCCL_DEBUG=INFO is the primary diagnostic tool; shows transport selection and failures
6. **nccl-tests**: all_reduce_perf is standard test; run with NCCL_DEBUG to see what's happening
