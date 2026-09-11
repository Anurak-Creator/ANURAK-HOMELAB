# ANURAK-HOMELAB
Infrastructure &amp; Environment Documentation for ANURAK-HOMELAB
# 🚀 ANURAK-HOMELAB Infrastructure & Environment

เอกสารสรุปสถาปัตยกรรมระบบเครือข่าย เครื่องเซิร์ฟเวอร์ และบริการต่างๆ (Services) ภายในแล็บ **ANURAK-HOMELAB**
* **Last Updated:** 2026-09-11
* **Maintainer:** Anurak P.
* **Environment:** Home/Lab Production Infrastructure

---

## 📐 1. Network Topology & Architecture

![Network Diagram] (./docs/network-diagram.png)
(https://drive.google.com/file/d/1Ra522HhGhfWKg_2d1QyhTViKlkLGA5c4/view?usp=sharing;https://github.com/Anurak-Creator/ANURAK-HOMELAB/blob/main/Anurak-Network_Diagram.drawio)
### 🌐 WAN & Internet Connectivity (Multi-WAN / Failover)
* **WAN 1 (Primary):** AP WiFi Apartment (`192.168.1.1`) via `wifi2` (Station Mode)
* **WAN 2 (Backup):** Router 4G (`192.168.0.1`) via `eth1`
* **Routing Strategy:** Main Outbound via Apartment WiFi, Failover/Backup via 4G Router
* **Netwatch Script (UP):** `/ip route enable [find comment="WAN1"]`
* **Netwatch Script (Down):** `/ip route disable [find comment="WAN1"]`

---

## 🔌 2. IP Addressing & Network Segmentation

| Subnet / Zone | Subnet Mask | Gateway (hAP) | Interface | Description / Target Devices |
| :--- | :--- | :--- | :--- | :--- |
| **Management** | `192.168.99.0/24` | `192.168.99.1` | Router hAP | Core Router Management & Infrastructure |
| **Server Subnet**| `192.168.10.0/24` | `192.168.10.1` | `eth3` | Virtualization Host & Server VMs |
| **Wireless Subnet**| `192.168.20.0/24`| `192.168.20.1` | `wifi1` | Mobile Devices & Wireless Clients |
| **Client Subnet**  | `192.168.30.0/24` | `192.168.30.1` | `eth2` | Wired Workstation PC |

---

## 🖥️ 3. Infrastructure & Virtualization Workloads

### 📦 Host Machine
* **Host Gateway/IP Zone:** `192.168.10.0/24` Zone (`eth3`)

### 🤖 Virtual Machines (VMs)
| VM Name | IP Address | OS / Spec | Services & Software | Access / Description |
| :--- | :--- | :--- | :--- | :--- |
| **VM-Ubuntu** | `192.168.10.251` | Ubuntu Server | WebServer, Tailscale Subnet Router | Overlay Gateway / Internal Web |
| **VM-EVE-NG** | `192.168.10.252` | Linux | Network Emulation / Testing | Network Lab Simulation |
| **VM-FreePBX**| `192.168.10.249` | FreePBX 17 | IP-PBX, Asterisk Voice Gateway | SIP/RTP Voice System |

---

## 🔑 4. Remote Access & Overlay Network (Tailscale)
* **Tailscale Node:** VM Ubuntu (`192.168.10.251`)
* **Routing Function:** Subnet Routing ข้ามไปหา IP วง internal (`192.168.10.0/24`, `192.168.0.1/24`)
* **SIP Remote Client:** เชื่อมต่อ FreePBX 17 (`192.168.10.249`) ผ่าน IP ของ Tailscale (`100.X.X.X`) เพื่อข้าม NAT Issue

---

## 🔒 5. Security & Isolation Policies
* **Inter-VLAN Rules:**
  * Block Traffic จาก Wireless Clients (`192.168.20.0/24`) ไม่ให้ข้ามไป Server Subnet (`192.168.10.0/24`) และ Client PC (`192.168.30.0/24`)
* **Outbound NAT:**
  * Masquerade ทั้งวง ออกไปทาง `wifi2` และ `eth1`

---

## 📜 6. Operational Changelog

* **2026-09-11:** 
  * ปรับโครงสร้าง Subnet แยก Port บน Router hAP (`eth2`: PC `192.168.30.0/24`, `eth3`: Server `192.168.10.0/24`) เพื่อแก้ปัญหา IP Conflict
  * กำหนด IP ประจำตัวให้กับ VM Server (`.251`, `.252`, `.249`) และตั้งค่า Netwatch Failover บน Router hAP
  * กำหนดชื่ออย่างเป็นทางการของแล็บเป็น **ANURAK-HOMELAB**
