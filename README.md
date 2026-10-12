# Enterprise E-Sports Multi-Layer Network Architecture

A multi-layer enterprise network designed and simulated in Cisco Packet Tracer for an e-sports organization[cite: 4]. The network features departmental segmentation, role-based Access Control Lists (ACLs), NAT Overload (PAT) for internet access, a dedicated DMZ for public-facing servers, and a secure GRE over IPsec VPN tunnel for remote site connectivity[cite: 4].

## 📐 Subnetting & IP Addressing Scheme

### 1. Private Subnets
| Department / Segment | Network Address | Usable Host Range | Broadcast Address | Subnet Mask | Default Gateway |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **DATAMode (Players)** | `10.10.0.0` | `10.10.0.1 – 10.10.0.30` | `10.10.0.31` | `255.255.255.224 (/27)` | `10.10.0.1`[cite: 6, 7] |
| **Coaches** | `10.10.0.32` | `10.10.0.33 – 10.10.0.62` | `10.10.0.63` | `255.255.255.224 (/27)` | `10.10.0.33`[cite: 6, 7] |
| **Media** | `10.10.0.64` | `10.10.0.65 – 10.10.0.94` | `10.10.0.95` | `255.255.255.224 (/27)` | `10.10.0.65`[cite: 6, 7] |
| **DMZ (Web Server)** | `10.10.0.96` | `10.10.0.97 – 10.10.0.102` | `10.10.0.103` | `255.255.255.248 (/29)` | `10.10.0.97`[cite: 6, 7] |
| **Main-to-Core Link** | `10.10.0.104` | `10.10.0.105 – 10.10.0.106` | `10.10.0.107` | `255.255.255.252 (/30)` | N/A[cite: 6, 7] |
| **HQ LAN** | `10.20.0.0` | `10.20.0.1 – 10.20.0.254` | `10.20.0.255` | `255.255.255.0 (/24)` | `10.20.0.1`[cite: 6, 7] |
| **GRE Tunnel Link** | `10.30.0.0` | `10.30.0.1 – 10.30.0.2` | `10.30.0.3` | `255.255.255.252 (/30)` | N/A[cite: 7, 8, 25] |

### 2. Public Subnets
| Connection | Network Address | Usable Range | Subnet Mask | Interface Assignments |
| :--- | :--- | :--- | :--- | :--- |
| **Main Router ↔ ISP** | `115.55.100.0` | `115.55.100.1 – 115.55.100.2` | `255.255.255.252 (/30)` | Main: `115.55.100.1` / ISP: `115.55.100.2`[cite: 6, 7] |
| **ISP ↔ VPN Router** | `115.55.100.4` | `115.55.100.5 – 115.55.100.6` | `255.255.255.252 (/30)` | ISP: `115.55.100.5` / VPN: `115.55.100.6`[cite: 6, 7] |
| **Public Switch Subnet**| `115.55.100.8` | `115.55.100.9 – 115.55.100.14` | `255.255.255.248 (/29)` | ISP G0/0: `115.55.100.9`[cite: 6, 7] |

---

## 🔒 Key Security & Network Features

### 1. Role-Based Access Control Lists (ACLs)
Configured on the **Core Router** to control traffic between `DATAMode`, `Coaches`, and `Media` subnets, as well as access to the DMZ Web Server and Internet[cite: 12, 30]:
* **DATAMode ACL:** Permits direct communication between specific player and coach pairs, enables UDP traffic, allows HTTPS (Port 443) and ICMP to the DMZ web server (`10.10.0.98`), and allows outbound ICMP to public/VPN nodes[cite: 12, 30].
* **Coaches ACL:** Grants communication to dedicated player endpoints, permits HTTPS (Port 443) and SSH (Port 22) access to the DMZ server, and denies unauthorized cross-departmental traffic[cite: 12, 30].
* **Media ACL:** Restricts HTTP access (`eq www`) to the web server while permitting HTTPS, FTP, UDP, and specific peer-to-peer ICMP links[cite: 12, 30].

### 2. Network Address Translation (NAT Overload & Static NAT)
Implemented on the **Main Router**[cite: 10, 11, 20]:
* **NAT Overload (PAT):** Translates internal private addresses (`10.10.0.0/24`) to public interface IP `115.55.100.1` for outbound internet traffic[cite: 11, 20, 21].
* **Static NAT:** Maps the internal Team Web Server (`10.10.0.98`) to a public IP (`115.55.100.14`) to allow external HTTP/HTTPS access[cite: 10, 20].

### 3. GRE over IPsec Site-to-Site VPN
Encrypted tunnel between **Main Router** (`115.55.100.1`) and **VPN Router** (`115.55.100.6`)[cite: 13, 22, 26]:
* **GRE Tunneling:** Encapsulates internal routing protocols (`Tunnel0` on `10.30.0.0/30`)[cite: 14, 23, 27].
* **IPsec Encryption:** Secures GRE packets using AES encryption (`esp-aes`) and SHA-HMAC authentication (`esp-sha-hmac`) with ISAKMP Phase 1 (`QM_IDLE / ACTIVE`)[cite: 13, 22, 26].

---

## 🧪 Verification & Testing Results

1. **Inter-Departmental Ping Checks:** Authorized pairings (e.g., Team Captain to Head Coach) passed successfully, while unauthorized cross-departmental ICMP requests were blocked by Core Router ACLs[cite: 14, 15, 16].
2. **NAT Verification:** Output from `show ip nat translations` confirmed active translation hits for outbound host traffic and static inbound requests to the DMZ server[cite: 10, 11, 20].
3. **VPN Tunnel Security:** Verified via `show crypto isakmp sa` and `show crypto ipsec sa`, confirming active encrypted ESP packet encapsulation/decapsulation between sites[cite: 13, 15, 22, 26].

---

## 📝 Conclusion

The network setup effectively demonstrates a secure enterprise deployment suitable for an e-sports organization[cite: 4, 16]. By pairing fine-grained ACL filtering at the core with NAT and encrypted VPN tunnels at the edge, the system maintains strict privacy across player, coaching, and management tiers while ensuring seamless connectivity to public web services and remote headquarters[cite: 4, 16].
