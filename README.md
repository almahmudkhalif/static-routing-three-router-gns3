# Static Routing with GNS-3 — Three-Router Network

A hands-on implementation of static routing across a three-router topology using GNS-3. Three PCs on separate subnets (`192.168.1.0/24`, `192.168.2.0/24`, `192.168.4.0/24`) achieve full end-to-end connectivity through manually configured static routes on R1, R2, and R3.

📖 **Full article on Dev.to:** [How I Built a Multi-Router Static Routing Network from Scratch Using GNS-3]([https://dev.to](https://dev.to/almahmudkhalif/lab-task-2-how-i-built-a-multi-router-static-routing-network-from-scratch-using-gns-3-4i21))

---

## Network Topology

![Network Topology](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/az1ic2kgqz5kddhyt8cy.png)

---

## IP Address Table

| Device | Interface | IP Address | Network |
|--------|-----------|------------|---------|
| R1 | Fa3/0 (→ PC1) | 192.168.1.1 | 192.168.1.0/24 |
| R1 | Fa2/0 (→ R2) | 192.168.0.1 | 192.168.0.0/24 |
| R1 | Fa0/0 (→ R3) | 192.168.3.1 | 192.168.3.0/24 |
| R2 | Fa2/0 (→ PC2) | 192.168.2.1 | 192.168.2.0/24 |
| R2 | Fa0/0 (→ R1) | 192.168.0.2 | 192.168.0.0/24 |
| R3 | Fa2/0 (→ PC3) | 192.168.4.1 | 192.168.4.0/24 |
| R3 | Fa0/0 (→ R1) | 192.168.3.2 | 192.168.3.0/24 |
| PC1 | eth0 | 192.168.1.2 | GW: 192.168.1.1 |
| PC2 | eth0 | 192.168.2.2 | GW: 192.168.2.1 |
| PC3 | eth0 | 192.168.4.2 | GW: 192.168.4.1 |

---

## Configuration Steps

### Step 1 — R3 Interface (Fa2/0 → PC3)

![R3 Interface Config](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/8n6modjkjfi8cozhuq3w.png)

```
R3#enable
R3#configure terminal
R3(config)#interface fastEthernet2/0
R3(config-if)#ip address 192.168.4.1 255.255.255.0
R3(config-if)#no shutdown
R3(config-if)#exit
R3(config)#end
R3#write memory
```

---

### Step 2 — PC3 IP Configuration

![PC3 Config](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/z1kra0dytr6vlzu2xow1.png)

```
PC3> ip 192.168.4.2 255.255.255.0 192.168.4.1
PC3> save
```

---

### Step 3 — Verify Local Connectivity (PC3 ↔ R3)

![PC3 Ping R3](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/zsl0jny6nys7t0f98of9.png)

```
PC3> ping 192.168.4.1
```

---

### Step 4 — R1 Interface Configuration

![R1 Interface Config](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/wenocp3d2n3dk0whxqbp.png)

```
R1#enable
R1#configure terminal

R1(config)#interface fastEthernet3/0
R1(config-if)#ip address 192.168.1.1 255.255.255.0
R1(config-if)#no shutdown
R1(config-if)#exit

R1(config)#interface fastEthernet2/0
R1(config-if)#ip address 192.168.0.1 255.255.255.0
R1(config-if)#no shutdown
R1(config-if)#exit

R1(config)#interface fastEthernet0/0
R1(config-if)#ip address 192.168.3.1 255.255.255.0
R1(config-if)#no shutdown
R1(config-if)#exit

R1(config)#end
R1#write memory
```

---

### Step 5 — PC1 IP Configuration & Ping to R1

![PC1 Config and Ping](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/rx4vllc2rvufzt5j586t.png)

```
PC1> ip 192.168.1.2 255.255.255.0 192.168.1.1
PC1> save
PC1> ping 192.168.1.1
```

---

### Step 6 — R2 Interface Configuration

![R2 Interface Config](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/jvhheacf6q49132ffpxy.png)

```
R2#enable
R2#configure terminal

R2(config)#interface fastEthernet2/0
R2(config-if)#ip address 192.168.2.1 255.255.255.0
R2(config-if)#no shutdown
R2(config-if)#exit

R2(config)#interface fastEthernet0/0
R2(config-if)#ip address 192.168.0.2 255.255.255.0
R2(config-if)#no shutdown
R2(config-if)#exit

R2(config)#end
R2#write memory
```

---

### Step 7 — PC2 IP Configuration & Ping to R2

![PC2 Config and Ping](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/3fz202aceivomxio08m1.png)

```
PC2> ip 192.168.2.2 255.255.255.0 192.168.2.1
PC2> save
PC2> ping 192.168.2.1
```

---

### Step 8 — Static Routes on R1

![R1 Static Routes](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/dsyn7wo13gvn9ycg1wqs.png)

```
R1#enable
R1#configure terminal
R1(config)#ip route 192.168.2.0 255.255.255.0 192.168.0.2
R1(config)#ip route 192.168.4.0 255.255.255.0 192.168.3.2
R1(config)#end
R1#write memory
```

---

### Step 9 — Static Routes on R2

![R2 Static Routes](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/l0n946crcdn2jyg56g4i.png)

```
R2#enable
R2#configure terminal
R2(config)#ip route 192.168.1.0 255.255.255.0 192.168.0.1
R2(config)#ip route 192.168.4.0 255.255.255.0 192.168.0.1
R2(config)#end
R2#write memory
```

---

### Step 10 — Static Routes on R3

![R3 Static Routes](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/hvthhy6t759tp0a0w4p0.png)

```
R3#enable
R3#configure terminal
R3(config)#ip route 192.168.1.0 255.255.255.0 192.168.3.1
R3(config)#ip route 192.168.2.0 255.255.255.0 192.168.3.1
R3(config)#end
R3#write memory
```

---

### Step 11 — End-to-End Connectivity Verification

**PC3 → PC1 and PC3 → PC2**

![PC3 Ping PC1 and PC2](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/9daiytrrccxmvew1iehs.png)

```
PC3> ping 192.168.1.2
PC3> ping 192.168.2.2
```

**PC2 → PC3**

![PC2 Ping PC3](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/a9qxuxztnr0k9v9yswur.png)

```
PC2> ping 192.168.4.2
```

---

## Useful Verification Commands

```
# Check routing table
R1#show ip route

# Check interface status
R1#show ip interface brief
```

---

## Common Mistakes

| Mistake | What happens | Fix |
|--------|--------------|-----|
| Forgetting `no shutdown` | Interface stays administratively down | Re-enter interface config and run `no shutdown` |
| Wrong next-hop IP | Route exists but traffic goes nowhere | Verify next-hop with `show ip route` |
| Missing return route | Ping sends but never replies | Add static routes on the destination router pointing back |
| VPCS `.0` address | "Invalid host address" error | Use `.1`, `.2`, etc. — not the network address |
| Not saving config | Config lost on restart | Always run `write memory` after configuring |

---

## Tools Used

- [GNS-3](https://www.gns3.com/) — Network emulator
- Cisco IOS Router Images
- VPCS — Virtual PC Simulator (built into GNS-3)

## 🌐 Connect With Me

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-blue?logo=linkedin)](https://www.linkedin.com/in/almahmudkhalif/)
[![Dev.to](https://img.shields.io/badge/Dev.to-Articles-black?logo=devdotto)](https://dev.to/almahmudkhalif/)
