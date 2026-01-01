# Linux on AWS – Hands-on Lab (Amazon Linux 2023 vs Ubuntu 24.04)

## Overview
This lab focuses on practical Linux fundamentals using real cloud infrastructure.  
Two different Linux distributions were launched on AWS EC2 and explored using SSH, basic commands, package managers, scripting, and kernel inspection.

The goal is to understand:
- Differences between Linux distributions
- Package managers
- Kernel vs Distribution
- GNU Project role
- Basic shell scripting
- Why cloud providers use specific kernel versions

---

## Environment Details

### AWS EC2 Instances Used

| Distribution        | Instance Type |
|---------------------|---------------|
| Amazon Linux 2023   | t2.nano      | 
| Ubuntu 24.04 LTS    | t2.nano      |

### Key Pair
- **Key Name:** `linux-30-days.pem`
- **Permission Command:**
```bash
chmod 400 linux-30-days.pem
````

---

## Part 1: Launching Two Linux Instances

### Amazon Linux 2023 (RHEL-based)

* AMI: Amazon Linux 2023
* Default user: `ec2-user`
* Package manager: `yum`

### Ubuntu 24.04 LTS (Debian-based)

* AMI: Ubuntu Server 24.04 LTS
* Default user: `ubuntu`
* Package manager: `apt`

Both instances were launched successfully and reached the **Running** state.

---

## Part 2: Connecting via SSH

### Amazon Linux

```bash
ssh -i "linux-30-days.pem" ec2-user@your-ec2-public-ip
```

### Ubuntu

```bash
ssh -i "linux-30-days.pem" ubuntu@your-ec2-public-ip
```

---

## Part 3: Command-Line Basics

### Commands Executed

```bash
pwd
ls -ltr
ls -ltra
man ls
```

### Answers

#### What does `pwd` tell you?

* `pwd` (Print Working Directory) shows the **absolute path** of the current directory.
* Example:

  * Amazon Linux: `/home/ec2-user`
  * Ubuntu: `/home/ubuntu`

This helps you understand **where you are in the filesystem**, which is critical in scripting and automation.

#### Why is `man` useful?

* `man` displays the **manual page** of a command.
* It provides:

  * Description
  * Options
  * Examples
* This is the **primary documentation source** on Linux systems.

---

## Part 4: Identifying the Distribution

### Command Used

```bash
cat /etc/os-release
```

### Amazon Linux Output (Key Fields)

```text
PRETTY_NAME="Amazon Linux 2023.9.20251208"
ID_LIKE="fedora"
SUPPORT_END="2029-06-30"
```

### Ubuntu Output (Key Fields)

```text
PRETTY_NAME="Ubuntu 24.04.3 LTS"
ID_LIKE=debian
VERSION_CODENAME=noble
```

### Key Differences

| Feature        | Amazon Linux    | Ubuntu             |
| -------------- | --------------- | ------------------ |
| Base           | Fedora/RHEL     | Debian             |
| Vendor         | AWS             | Canonical          |
| Support Model  | Cloud-optimized | General-purpose    |
| Default Kernel | AWS-patched     | AWS-patched Ubuntu |

---

## Part 5: Package Managers

### Commands Executed

#### Amazon Linux

```bash
sudo yum update
```

#### Ubuntu

```bash
sudo apt update
```

### Explanation (One Sentence)

Amazon Linux uses **`yum` (Fedora/RHEL ecosystem)** while Ubuntu uses **`apt` (Debian ecosystem)**, both serving the same purpose of managing packages but with different repositories and tooling.

---

## Part 6: Linux Kernel vs Linux Distribution

### Linux Kernel

* Core of the OS
* Handles:

  * CPU scheduling
  * Memory management
  * Hardware interaction
  * Process management

### Linux Distribution

* Kernel **+**

  * GNU tools
  * Package manager
  * Init system
  * Configuration
  * Vendor support

### Simple Analogy

* **Kernel:** Engine of a car
* **Distribution:** Full car (engine + body + dashboard + support)

---

## Part 7: Role of the GNU Project

### What is GNU?

* Started by **Richard Stallman**
* Goal: Create a **free Unix-like OS**

### Role in Linux Ecosystem

* Provides essential tools:

  * `bash`
  * `ls`, `cp`, `mv`
  * `grep`, `awk`, `sed`
  * `gcc`
* Linux without GNU tools is **not usable** for users.

> Correct term: **GNU/Linux**, because Linux is only the kernel.

---

## Part 8: Shell Script – `hello.sh`

### Script Content

```bash
#!/bin/bash
echo "Hello, Linux World!"
echo "My current directory is: $(pwd)"
echo "My distribution is:"
cat /etc/os-release | grep PRETTY_NAME
```

### What Each Line Does

| Line                  | Explanation                                   |
| --------------------- | --------------------------------------------- |
| `#!/bin/bash`         | Specifies Bash as the interpreter             |
| `echo`                | Prints output to terminal                     |
| `$(pwd)`              | Command substitution to get current directory |
| `cat /etc/os-release` | Reads OS info                                 |
| `grep PRETTY_NAME`    | Filters readable OS name                      |

### Output Comparison

#### Amazon Linux

```text
PRETTY_NAME="Amazon Linux 2023.9.20251208"
```

#### Ubuntu

```text
PRETTY_NAME="Ubuntu 24.04.3 LTS"
```

---

## Part 9: Kernel Version Challenge

### Command Used

```bash
uname -r
```

### Kernel Versions Observed

| Distribution | Kernel Version           |
| ------------ | ------------------------ |
| Amazon Linux | 6.1.158-180.294.amzn2023 |
| Ubuntu       | 6.14.0-1015-aws          |

### Why AWS Uses Older / Custom Kernels

Cloud providers prioritize:

1. **Stability** – No breaking changes
2. **Security backports** – Patches without upgrades
3. **Compatibility** – Works with EC2, ENA, Nitro
4. **Enterprise support** – Predictable behavior

> Newer kernel ≠ better for production

---

## Final Conclusion

This lab demonstrated:

* Practical Linux usage on real cloud infrastructure
* Differences between RHEL-based and Debian-based systems
* Importance of package managers
* Kernel vs distribution clarity
* GNU Project significance
* Basic shell scripting
* Cloud kernel version strategy

**Performed By:** Mohammed Yasir Khan

