# Secure SOHO/Branch Office Network Deployment

## 🌐 Project Overview
This repository contains the full architectural design and implementation files for a secure, simulated enterprise branch office network engineered in Cisco Packet Tracer. The network architecture effectively isolates wireless user segments from the corporate core utilizing localized DHCP infrastructure, device hardening baselines, and stateful NAT translation boundaries.

![Network Topology](./topology-map.png)

## 📊 Logical IP Addressing Matrix
| Device / Zone | Interface | Assigned IP | Subnet Mask | Default Gateway | Purpose |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **COLOMBO-EDGE-RTR** | Gig0/0/0 | 192.168.10.1 | 255.255.255.0 | N/A | Edge Gateway (Cisco ISR 4331) |
| **COLOMBO-CORE-SW** | VLAN 1 | 192.168.10.2 | 255.255.255.0 | 192.168.10.1 | Management Interface (Catalyst 2960) |
| **Core Infrastructure** | Server0 | 192.168.10.10 | 255.255.255.0 | 192.168.10.1 | Static DHCP/DNS Host |
| **Wired Endpoints** | PC0 / PC1 | Dynamic (Pool) | 255.255.255.0 | 192.168.10.1 | Corporate Seats (Scope: .20 to .254) |
| **Wireless Zone** | Laptops | Dynamic (NAT) | 255.255.255.0 | 192.168.0.1 | Isolated Wi-Fi Space (Scope: .100+) |

## 🔒 Implemented Security & Hardening Baselines
The following enterprise staging protocols were deployed via the Cisco IOS CLI to secure the internal management plane of the infrastructure assets:
* **Privileged Mode Security:** Configured secure hashing algorithms via `enable secret` to protect global configuration access modes from unauthorized compromise.
* **Console Line Abstraction:** Disabled automated domain name resolutions using `no ip domain-lookup` to prevent terminal hangs and latency during administrative typos.
* **Credential Protection:** Activated global `service password-encryption` to scramble plain-text configuration strings stored within device running memory.
* **Access Compliance:** Deployed an official Message of the Day (`banner motd`) boundary notification to comply with standard legal and organizational information security practices.

## Verification Logs & Operational Evidence

### 1. Gateway Status Matrix (`show ip interface brief`)
```text
COLOMBO-EDGE-RTR#show ip interface brief
Interface              IP-Address      OK? Method Status                Protocol 
GigabitEthernet0/0/0   192.168.10.1    YES manual up                    up 
GigabitEthernet0/0/1   unassigned      YES unset  administratively down down 
GigabitEthernet0/0/2   unassigned      YES unset  administratively down down 
Vlan1                  unassigned      YES unset  administratively down down
```
**2. Active Routing Table Core (show ip route)**

```
COLOMBO-EDGE-RTR#show ip route
Codes: L - local, C - connected, S - static, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2, E - EGP
       i - IS-IS, L1 - IS-IS level-1, L2 - IS-IS level-2, ia - IS-IS inter area
       * - candidate default, U - per-user static route, o - ODR
       P - periodic downloaded static route

Gateway of last resort is not set

     192.168.10.0/24 is variably subnetted, 2 subnets, 2 masks
C       192.168.10.0/24 is directly connected, GigabitEthernet0/0/0
L       192.168.10.1/32 is directly connected, GigabitEthernet0/0/0
```
**3. Cross-Subnet End-to-End Connectivity (Wireless Endpoint to Core Server)**

```
C:\>ping 192.168.10.10

Pinging 192.168.10.10 with 32 bytes of data:

Reply from 192.168.10.10: bytes=32 time=36ms TTL=127
Reply from 192.168.10.10: bytes=32 time=31ms TTL=127
Reply from 192.168.10.10: bytes=32 time=37ms TTL=127
Reply from 192.168.10.10: bytes=32 time=26ms TTL=127

Ping statistics for 192.168.10.10:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 26ms, Maximum = 37ms, Average = 32ms
```
