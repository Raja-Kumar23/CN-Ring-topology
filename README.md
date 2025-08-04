# 🔁 Ring Topology using `iptables` in Ubuntu

This project demonstrates how to implement a **Ring Topology** using `iptables` in **Ubuntu**. It uses 3 systems connected in a logical ring. Each system can only forward packets in one direction, forming a closed loop.

---

## 🕸️ What is Ring Topology?

A **Ring Topology** is a network configuration where each node is connected to exactly two other nodes, forming a closed loop.  
Data travels **in one direction** (clockwise or anti-clockwise), and each system forwards packets to the next.

### 🔄 Example:
For nodes: **A → B → C → A**  
- A can send to B ✅  
- B can send to C ✅  
- C can send to A ✅  
- A ❌ C directly  
- C ❌ B directly

---

## 🖥️ Sample Setup

Let's assume the following IP addresses:

| Node | IP Address       |
|------|------------------|
| A    | 192.168.1.10     |
| B    | 192.168.1.20     |
| C    | 192.168.1.30     |

We will enforce these rules using `iptables`.

---

## 📜 Ring Topology Rules

| From → To     | Allowed? | Explanation                              |
|---------------|----------|------------------------------------------|
| A → B         | ✅ Yes   | Clockwise flow from A to B               |
| A → C         | ❌ No    | C is not the next clockwise node         |
| B → C         | ✅ Yes   | Clockwise flow from B to C               |
| B → A         | ❌ No    | A is behind B in the ring                |
| C → A         | ✅ Yes   | Clockwise flow from C to A               |
| C → B         | ❌ No    | B is not the next clockwise node         |

---

## 🧑‍💻 Commands for Each Node (Using `iptables`)

### 📍 On Node A (192.168.1.10)
```bash
sudo iptables -A FORWARD -d 192.168.1.20 -j ACCEPT
sudo iptables -A FORWARD -d 192.168.1.30 -j DROP


### 📍 On Node B (192.168.1.20)
```bash
sudo iptables -A FORWARD -d 192.168.1.30 -j ACCEPT
sudo iptables -A FORWARD -d 192.168.1.10 -j DROP


### 📍 On Node C (192.168.1.30)
```bash

sudo iptables -A FORWARD -d 192.168.1.10 -j ACCEPT
sudo iptables -A FORWARD -d 192.168.1.20 -j DROP


✅ How to Check if It Works

# From A:
ping 192.168.1.20   # Success ✅
ping 192.168.1.30   # Blocked ❌

# From B:
ping 192.168.1.30   # Success ✅
ping 192.168.1.10   # Blocked ❌

# From C:
ping 192.168.1.10   # Success ✅
ping 192.168.1.20   # Blocked ❌


📚 Final Explanation
This configuration simulates a unidirectional ring, allowing traffic to flow in a single direction.
It restricts direct access to nodes not immediately next in the ring using iptables.

This setup is helpful for understanding network topologies and enforcing strict traffic rules at the OS level using firewalls.


FOR SAURAV
sudo iptables -F
sudo iptables -A INPUT -s 10.6.189.198 -j ACCEPT    # Allow Shomya
sudo iptables -A INPUT -s 10.7.225.247 -j DROP      # Block Raja
sudo iptables -A OUTPUT -d 10.6.189.198 -j ACCEPT
sudo iptables -A OUTPUT -d 10.7.225.247 -j DROP


FOR SHOMYA 
sudo iptables -F
sudo iptables -A INPUT -s 10.7.225.247 -j ACCEPT    # Allow Raja
sudo iptables -A INPUT -s 10.7.239.244 -j DROP      # Block Saurav
sudo iptables -A OUTPUT -d 10.7.225.247 -j ACCEPT
sudo iptables -A OUTPUT -d 10.7.239.244 -j DROP

FOR RAJA 
sudo iptables -F
sudo iptables -A INPUT -s 10.6.189.198 -j ACCEPT    # Allow from Shomya
sudo iptables -A INPUT -s 10.7.239.244 -j DROP      # Block Saurav
sudo iptables -A OUTPUT -d 10.6.189.198 -j ACCEPT
sudo iptables -A OUTPUT -d 10.7.239.244 -j DROP

RAJA ------ SHOMYA -------------SAURAV ------------RAJA------------SHOMYA

