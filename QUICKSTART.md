# Quick Start Guide

## 1. Install Dependencies

### Ubuntu/Debian
```bash
sudo apt-get update
sudo apt-get install -y \
    build-essential \
    gcc \
    make \
    libpcap-dev \
    libbpf-dev \
    pkg-config \
    dpdk-dev \
    libdpdk-dev
```

### Check Dependencies
```bash
make check-deps
```

## 2. Build Project

### Build All Modules
```bash
make all
```

## 3. Setup Huge Pages (DPDK mode)

```bash
# Check current huge pages
cat /proc/meminfo | grep Huge

# Set huge pages (example: 1024 pages of 2MB each)
echo 1024 > /sys/kernel/mm/hugepages/hugepages-2048kB/nr_hugepages

# Mount huge pages
sudo mkdir -p /mnt/huge
sudo mount -t hugetlbfs nodev /mnt/huge
```

## 4. Run Tests

### Socket Mode Test
```bash
# View available network interfaces
ip link show

# Run receiver (requires root privileges)
sudo ./bin/packet_receiver --mode socket --interface eth0 --duration 10
```

### AF_XDP Mode Test
```bash
# Check kernel version (requires 4.18+)
uname -r

# Run receiver
sudo ./bin/packet_receiver --mode af_xdp --interface eth0 --duration 10
```

### DPDK Mode Test
```bash
# Bind Network Interface to DPDK Driver

# List available network interfaces
dpdk-devbind.py --status

# Bind interface to DPDK driver
sudo dpdk-devbind.py --bind=uio_pci_generic <PCI_ADDRESS>

# Or use vfio-pci (requires IOMMU support)
sudo modprobe vfio-pci
sudo dpdk-devbind.py --bind=vfio-pci <PCI_ADDRESS>

# Run receiver
sudo ./bin/packet_receiver --mode dpdk -a 0000:01:00.0
```

### View Help
```bash
./bin/packet_receiver --help
```

## 5. Use Pktgen Testing

### Prepare Pktgen Environment

#### Method 1: Use Kernel Pktgen
```bash
# Load module
sudo modprobe pktgen

# Configure (via /proc/net/pktgen/)
# See pktgen documentation for details
```

#### Method 2: Use DPDK Pktgen
```bash
# Download and compile DPDK Pktgen
# Configure and run
```

### Run Automated Test Script
```bash
# Send test packets from another terminal
sudo ./scripts/run_pktgen_test.sh
```

## 6. View Results

After the program finishes, it will automatically display statistics:
- Total packets received
- Packet rate (PPS)
- Bit rate (Mbps)