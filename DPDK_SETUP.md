# DPDK Setup Guide

## Prerequisites

Before using DPDK mode, you need to:

1. **Install DPDK**
   ```bash
   # Download DPDK from https://www.dpdk.org/
   # Or use package manager (if available)
   ```

2. **Build DPDK**
   ```bash
   cd dpdk-<version>
   meson build
   ninja -C build
   sudo ninja -C build install
   ```

3. **Setup Huge Pages**
   ```bash
   # Check current huge pages
   cat /proc/meminfo | grep Huge
   
   # Set huge pages (example: 1024 pages of 2MB each)
   echo 1024 > /sys/kernel/mm/hugepages/hugepages-2048kB/nr_hugepages
   
   # Mount huge pages
   sudo mkdir -p /mnt/huge
   sudo mount -t hugetlbfs nodev /mnt/huge
   ```

4. **Bind Network Interface to DPDK Driver**
   ```bash
   # List available network interfaces
   dpdk-devbind.py --status
   
   # Bind interface to DPDK driver (example: use igb_uio or vfio-pci)
   sudo dpdk-devbind.py --bind=igb_uio <PCI_ADDRESS>
   
   # Or use vfio-pci (requires IOMMU support)
   sudo modprobe vfio-pci
   sudo dpdk-devbind.py --bind=vfio-pci <PCI_ADDRESS>
   ```

## Configuration

### Set DPDK Directory

If DPDK is installed in a non-standard location, you can set the following variables in the Makefile:

```makefile
# DPDK header files directory (default: /usr/include/dpdk)
DPDK_INCLUDE_DIR ?= /usr/include/dpdk

# DPDK config files directory (default: /usr/include/x86_64-linux-gnu/dpdk)
DPDK_CONFIG_DIR ?= /usr/include/x86_64-linux-gnu/dpdk

# DPDK library files directory (default: /usr/lib/x86_64-linux-gnu)
DPDK_LIB_DIR ?= /usr/lib/x86_64-linux-gnu
```

Or set environment variables:

```bash
export DPDK_INCLUDE_DIR=/path/to/dpdk/include
export DPDK_CONFIG_DIR=/path/to/dpdk/config
export DPDK_LIB_DIR=/path/to/dpdk/lib
make all
```

### Common DPDK Installation Paths

**Package Manager Installation (Ubuntu/Debian):**
- Headers: `/usr/include/dpdk`
- Config: `/usr/include/x86_64-linux-gnu/dpdk`
- Libraries: `/usr/lib/x86_64-linux-gnu`

**Manual Installation:**
- `/usr/local/dpdk` (typical default)
- Custom build directory

## Usage

### Run DPDK Mode

```bash
# Note: DPDK requires root privileges
sudo ./bin/packet_receiver --mode dpdk --interface <interface_name>
```

### Important Notes

1. **Interface Name**: The interface name should match the DPDK port name or PCI address
   - DPDK ports are typically named like `0000:01:00.0`
   - You can find port names using: `dpdk-devbind.py --status`

2. **Core Affinity**: DPDK EAL is configured to use core 0 by default
   - Modify `dpdk_init()` function to change core assignment
   - Use `-l` option in EAL arguments

3. **Memory**: DPDK requires huge pages
   - Ensure huge pages are configured before running
   - Default: 8191 mbufs, can be adjusted in code

## Troubleshooting

### Error: "No DPDK ports available"

- Check if network interface is bound to DPDK driver
- Verify with: `dpdk-devbind.py --status`
- Rebind interface if needed

### Error: "DPDK EAL initialization failed"

- Check huge pages: `cat /proc/meminfo | grep Huge`
- Ensure huge pages are mounted
- Check DPDK installation path

### Error: "Failed to create mbuf pool"

- Increase huge pages
- Check available memory
- Verify DPDK installation

### Performance Issues

- Use dedicated CPU cores (isolate cores)
- Disable CPU frequency scaling
- Use NUMA-aware memory allocation
- Adjust burst size and ring sizes

## Example Setup Script

```bash
#!/bin/bash

# Setup huge pages
echo 1024 > /sys/kernel/mm/hugepages/hugepages-2048kB/nr_hugepages
mkdir -p /mnt/huge
mount -t hugetlbfs nodev /mnt/huge

# Bind network interface (replace with your PCI address)
sudo dpdk-devbind.py --bind=igb_uio 0000:01:00.0

# Run packet receiver
sudo ./bin/packet_receiver --mode dpdk --interface 0000:01:00.0
```

## References

- [DPDK Documentation](https://doc.dpdk.org/)
- [DPDK Getting Started Guide](https://doc.dpdk.org/guides/linux_gsg/)
- [DPDK Application User Guide](https://doc.dpdk.org/guides/prog_guide/)

