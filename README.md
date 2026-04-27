# ITI-CCNA-PROJECT
# Network Configuration README

## 📌 Overview

This project involves building and configuring a multi-switch, multi-router network topology with VLAN segmentation, DHCP services, routing using EIGRP, SSH security, ACL policies, and AAA authentication using a RADIUS server.

---

## 🧩 Part 1: Basic Device Configuration

### Switches & Routers Naming

* **Switches:**

  * S1 → First Name
  * S2 → Second Name
  * S3 → Third Name
* **Routers:**

  * R1 → First Name
  * R2 → Second Name
  * R3 → Third Name

### Global Configuration

* Domain Name: `ITI.com`
* Line Password: First 3 letters of each name (First + Second + Third)
* Enable Password Encryption:

  ```
  service password-encryption
  ```

### User Configuration

```
username <ThirdName> password 123
```

### SSH Configuration

```
ip domain-name ITI.com
crypto key generate rsa
(choose default bits)

line vty 0 4
transport input ssh
login local
```

---

## 🌐 Part 2: VLAN Configuration

### SW1 VLANs

| VLAN    | Ports            |
| ------- | ---------------- |
| VLAN 10 | f0/1, f0/2, f0/3 |
| VLAN 20 | f0/4, f0/5, f0/6 |
| VLAN 30 | g0/1             |

### SW2 VLANs

| VLAN    | Ports            |
| ------- | ---------------- |
| VLAN 40 | f0/1, f0/2, f0/3 |
| VLAN 50 | f0/4, f0/5, f0/6 |
| VLAN 60 | g0/1             |

### VLAN Creation Example

```
vlan 10
name VLAN10
interface range f0/1-3
switchport mode access
switchport access vlan 10
```

### Enable Rapid STP

```
spanning-tree mode rapid-pvst
```

---

## 📡 Part 3: DHCP Configuration

### General Rules

* Subnet Mask: `255.255.255.0 (/24)`
* DNS Server: `192.168.7.10`

### DHCP Pools

| VLAN    | Network         | Default Gateway |
| ------- | --------------- | --------------- |
| VLAN 10 | 192.168.X.0     | 192.168.X.1     |
| VLAN 20 | 192.168.(X+1).0 | 192.168.(X+1).1 |
| VLAN 30 | 192.168.(X+2).0 | 192.168.(X+2).1 |
| VLAN 40 | 192.168.(X+3).0 | 192.168.(X+3).1 |
| VLAN 50 | 192.168.(X+4).0 | 192.168.(X+4).1 |
| VLAN 60 | 192.168.(X+5).0 | 192.168.(X+5).1 |
| SW4     | 10.10.50.0      | 10.10.50.1      |

### DHCP Example

```
ip dhcp pool VLAN10
network 192.168.X.0 255.255.255.0
default-router 192.168.X.1
dns-server 192.168.7.10
```

---

## 🔁 Part 4: Routing Configuration

### Dynamic Routing (EIGRP)

```
router eigrp 100
network 192.168.0.0
no auto-summary
```

### Default Route (Serial Connection)

```
ip route 0.0.0.0 0.0.0.0 <next-hop-ip>
```

---

## 🔐 RADIUS Server Configuration

### Enable AAA

```
aaa new-model
```

### Configure RADIUS

```
radius-server host <radius-ip> key cisco
aaa authentication login default group radius local
```

---

## 🚫 ACL Configuration

### 1. Deny Host from DNS

Block host `192.168.X.2` from DNS (port 53):

```
access-list 101 deny udp host 192.168.X.2 any eq 53
access-list 101 permit ip any any
```

---

### 2. Block VLAN 30 from Web Server

```
access-list 102 deny tcp 192.168.(X+2).0 0.0.0.255 any eq 80
access-list 102 permit ip any any
```

---

### 3. Prevent VLAN 10 from Mail Services

(Mail ports: 25, 110, 143)

```
access-list 103 deny tcp 192.168.X.0 0.0.0.255 any eq 25
access-list 103 deny tcp 192.168.X.0 0.0.0.255 any eq 110
access-list 103 deny tcp 192.168.X.0 0.0.0.255 any eq 143
access-list 103 permit ip any any
```

---

## ✅ Notes

* Replace **X** with your assigned base network number.
* Ensure trunk links between switches are configured properly.
* Apply ACLs to the correct interfaces (inbound/outbound).
* Verify connectivity using `ping` and `show ip route`.

---

## 🎯 Outcome

After completing all steps:

* VLAN segmentation is functional
* Devices receive IPs via DHCP
* Routing is operational using EIGRP
* SSH access is secured
* ACL policies are enforced
* Central authentication via RADIUS is active

---
