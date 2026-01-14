# Test Result

**Control Variable:**
1. Same NIC
2. Run with single core / queue.
3. Each program runs for 10 seconds.

**Independent Variable:**

1. Packet size

## Socket
### 64 Bytes
![socket_64](tests/packet_size_64/socket_64.png)
### 128 Bytes
![socket_128](tests/packet_size_128/socket_128.png)
### 512 Bytes
![socket_512](tests/packet_size_512/socket_512.png)


## AF_XDP (zero copy)
### 64 Bytes
![xdp_64](tests/packet_size_64/af_xdp_64.png)
### 128 Bytes
![xdp_128](tests/packet_size_128/af_xdp_128.png)
### 512 Bytes
![xdp_512](tests/packet_size_512/af_xdp_512.png)


## DPDK
### 64 Bytes
![dpdk_64](tests/packet_size_64/dpdk_64.png)
### 128 Bytes
![dpdk_128](tests/packet_size_128/dpdk_128.png)
### 512 Bytes
![dpdk_512](tests/packet_size_512/dpdk_512.png)


## SUMMARY

### Mode

**PPS**

DPDK > AF_XDP >> SOCKET

**CPU usage**

DPDK > SOCKET >> AF_XDP (polling + blocking)

### Packet size

**Throughput**

512bytes > 128 bytes > 64 bytes

**PPS**

64 bytes > 128 bytes > 512bytes