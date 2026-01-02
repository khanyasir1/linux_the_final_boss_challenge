

# Hands-on Linux & Networking Lab on AWS EC2

*(Ubuntu 24.04 LTS & Amazon Linux 2023)*

## 📌 Overview

This hands-on lab focuses on **Linux fundamentals, AWS EC2 operations, networking, snapshots (AMI), boot analysis, and multi-instance communication** using real cloud infrastructure.

The lab was performed on **the same Ubuntu and Amazon Linux EC2 instances used in earlier exercises**, extending them step-by-step to simulate **real-world DevOps and cloud workflows**.

---

## 🧰 Environment Used

### Operating Systems

* **Ubuntu 24.04 LTS (AWS EC2)**
* **Amazon Linux 2023 (AWS EC2)**

### Cloud Platform

* **Amazon Web Services (AWS EC2)**
* Same VPC & Subnet for networking labs
* Access via SSH

### Users

* Ubuntu → `ubuntu`
* Amazon Linux → `ec2-user`

---

## 🧪 Lab Objectives

By completing this lab, you will understand:

* Linux kernel vs distribution
* AWS-patched kernels
* Package management (`apt`, `dnf`)
* Snapshot & rollback using AMIs
* Hostname management
* Boot-time analysis using `systemd`
* AWS networking (VPC-based communication)
* Multi-instance (LAN-like) communication

---

## Part 1: Instance Access & First Boot Verification

### What Was Done

* Reused **existing Ubuntu & Amazon Linux EC2 instances**
* Connected via SSH
* Verified login and system access

---

### Install & Run `neofetch`

`neofetch` is used to visually confirm:

* OS name
* Kernel version
* Architecture
* Uptime

#### Ubuntu

```bash
sudo apt update
sudo apt install neofetch -y
neofetch
```

#### Amazon Linux 2023

```bash
sudo dnf install neofetch -y
neofetch
```

### Why install `neofetch`?

* Quickly validates OS & kernel
* Useful for documentation/screenshots
* Safe, lightweight, and non-intrusive
* Commonly used in learning & debugging environments

> `neofetch` does NOT change system behavior — it is informational only.

---

### Kernel Version Check

```bash
uname -r
```

### Observed Output

| OS                | Kernel Version                  |
| ----------------- | ------------------------------- |
| Amazon Linux 2023 | 6.1.158-180.294.amzn2023.x86_64 |
| Ubuntu 24.04      | 6.14.0-1015-aws                 |

### Key Learning

* Both systems use **AWS-patched kernels**
* Kernel version is controlled by **OS + cloud provider**
* User commands do NOT change kernel versions

---

## Part 2: Network Check & DNS Validation (AWS)

### Ping Tests

```bash
ping -c 4 8.8.8.8
ping -c 4 google.com
```

### Interpretation

| Result            | Meaning             |
| ----------------- | ------------------- |
| IP ping works     | Internet routing OK |
| Domain ping works | DNS resolution OK   |

---

### DNS Fix (If Required)

```bash
sudo nano /etc/resolv.conf
```

Add:

```text
nameserver 8.8.8.8
```

Test again:

```bash
ping google.com
```

> In AWS, DNS is usually auto-managed, but knowing this fix is essential.

---

### How AWS Networking Differs from Host Machine

| Local Machine | AWS EC2                     |
| ------------- | --------------------------- |
| Physical NIC  | Virtual NIC (ENI)           |
| ISP routing   | VPC routing tables          |
| Manual setup  | Software-defined networking |
| Fixed         | Scalable & dynamic          |

**AWS uses SDN (Software Defined Networking)**
Instances communicate via **VPC, subnets, route tables, and security groups**.

---

## Part 3: Snapshot & Rollback (AWS AMI Workflow)

### Snapshot Concept in AWS

In AWS:

* **VM Snapshot = AMI**
* **Disk Snapshot = EBS Snapshot**

### AMI Lifecycle Used

```
Base AMI
  ↓
Baseline AMI
  ↓
Configured AMI (apps, hostname)
  ↓
Launch new instances
```

---

### Step 1: Create Baseline AMI

* EC2 → Instances → Actions
* Image & Templates → **Create Image**
* Name: `Baseline`

---

### Step 2: Install Tool (`cowsay`)

#### Ubuntu

```bash
sudo apt install cowsay -y
cowsay "Hello VM"
```

#### Amazon Linux

```bash
sudo dnf install cowsay -y
cowsay "Hello VM"
```

---

### Step 3: Create Updated AMI

* Create new AMI
* Name: `With-Cow`

---

### Step 4: Restore Baseline

* Launch instance from `Baseline` AMI
* Test:

```bash
cowsay "test"
```

❌ Command not found → confirms rollback worked

---

### Why Snapshots Matter

* Instant rollback
* Disaster recovery
* Safe experimentation
* Blue/Green deployments
* Immutable infrastructure

---

## Part 4: Hostname Configuration

### Change Hostname

```bash
sudo hostnamectl set-hostname my-lab-server
sudo reboot
```

Verify:

```bash
hostname
```

Output:

```text
my-lab-server
```

### Create New AMI

Name:

```
Renamed-Host
```

---

### Why Hostnames Matter

* Identify nodes easily
* Useful in logs & monitoring
* Mandatory for clusters & Kubernetes
* Improves operational clarity

Example:

```
api-prod-01
db-prod-01
worker-dev-02
```

---

## Part 5: Boot Analysis & systemd

### Check Boot Errors

```bash
journalctl -b -p err
```

Observed:

* Minor hypervisor-related warnings
* No critical failures
* Common on AWS Xen/Nitro platforms

---

### Boot Time Analysis

```bash
systemd-analyze blame
```

Key Observation:

```text
snapd.service  (slowest)
```

### Explanation

* Snap initializes packages at boot
* Resource-heavy on small EC2 instances
* Common Ubuntu cloud behavior

---

## Part 6: Multi-Instance Networking (AWS LAN Equivalent)

### AWS Equivalent of Bridge Mode

In AWS:

* Same **VPC**
* Same **Subnet**
* Same **Security Group**

Security Group rule:

```
All traffic → Source: same security group
```

---

### Get Private IPs

```bash
ip addr show
```

Example:

```
10.0.1.12
10.0.1.15
```

---

### Ping Between Instances

```bash
ping 10.0.1.15
```

✔ Successful reply

---

### Netcat Test (Direct Communication)

#### Install

```bash
sudo apt install netcat -y
sudo dnf install nc -y
```

#### Listener (VM1)

```bash
nc -l 1234
```

#### Sender (VM2)

```bash
nc 10.0.1.12 1234
Hello from second instance!
```

Message received instantly 🎉

---

### Why This Matters

| NAT           | Same Subnet        |
| ------------- | ------------------ |
| Outbound only | Full bidirectional |
| Limited       | Production-like    |
| Hidden        | Discoverable       |

Used in:

* Microservices
* Databases
* Kubernetes
* HA systems

---


## Performed By

**Mohammed Yasir Khan**
