# Linux Security Monitoring Home Lab

## 📌 Overview

This project demonstrates a lightweight security monitoring lab built using Ubuntu in a virtualized environment. The goal was to simulate common suspicious activities on a Linux system and analyze how they appear in system and audit logs.

Instead of relying on heavy SIEM tools, this lab focuses on understanding **native Linux logging and process monitoring**, which is a core skill for entry-level security analysts.

---

## 🎯 Objectives

* Set up a Linux-based monitoring environment
* Generate realistic suspicious activity
* Analyze authentication and system logs
* Configure `auditd` to track specific behaviors
* Document findings in an analyst-style format

---

## 🧱 Lab Environment

* **Platform:** VirtualBox
* **OS:** Ubuntu 24.04 LTS
* **Tools Used:**

  * `auditd` (process monitoring)
  * `OpenSSH` (authentication logging)
  * `nmap` (network scanning)
  * Native Linux logs (`/var/log/auth.log`, `journalctl`)

---

## ⚙️ System Setup

The lab environment was configured with:

* SSH enabled for authentication logging
* System packages updated
* `auditd` installed and configured for command monitoring

Key command:

```bash
sudo apt install -y openssh-server auditd nmap
```

---

## 🔍 Monitoring Configuration (auditd)

Custom audit rules were created to track specific categories of activity:

```bash
-w /usr/bin/whoami -p x -k recon_commands
-w /usr/bin/id -p x -k recon_commands
-w /bin/id -p x -k recon_commands
-w /usr/bin/hostname -p x -k recon_commands
-w /bin/hostname -p x -k recon_commands
-w /usr/bin/cat -p x -k recon_commands
-w /bin/cat -p x -k recon_commands
-w /usr/bin/ss -p x -k recon_commands

-w /usr/sbin/useradd -p x -k user_management
-w /usr/bin/crontab -p x -k persistence
```

These rules allow tracking of:

* System enumeration commands
* User account creation
* Scheduled task persistence

---

## 🧪 Simulated Scenarios

### 1. Failed Login Attempts

**Action:**
Triggered failed logins using incorrect credentials.

**Detection:**
Logs in `/var/log/auth.log` showed:

* `Failed password`
* Targeted usernames
* Timestamps

**Insight:**
Repeated failures may indicate brute-force attempts or unauthorized access.

---

### 2. Privilege Escalation (sudo)

**Action:**
Executed administrative commands using `sudo`.

**Detection:**
Authentication logs recorded:

* User performing the action
* Timestamp of privilege usage

**Insight:**
Monitoring `sudo` helps identify sensitive system changes.

---

### 3. Reconnaissance Commands

**Action:**
Executed enumeration commands:

```bash
whoami
id
hostname
cat /etc/passwd
ss -tulnp
```

**Detection:**
Captured using:

```bash
sudo ausearch -k recon_commands -i
```

**Insight:**
These commands are commonly used post-compromise to understand the system.

---

### 4. User Account Creation

**Action:**
Created a new account:

```bash
sudo useradd backupadmin
```

**Detection:**

```bash
sudo ausearch -k user_management -i
```

**Insight:**
Unexpected account creation may indicate persistence or unauthorized admin activity.

---

### 5. Cron-Based Persistence

**Action:**
Added scheduled task:

```bash
crontab -e
```

Example:

```bash
*/5 * * * * echo "SOC LAB TEST" >> /tmp/lab.log
```

**Detection:**

```bash
sudo ausearch -k persistence -i
```

**Verification:**

```bash
cat /tmp/lab.log
```

**Insight:**
Cron jobs are commonly used to maintain persistence.

---

### 6. Network Exposure (Nmap)

**Action:**
Scanned the system:

```bash
nmap localhost
```

**Insight:**
Identifies open ports and potential attack surface (e.g., SSH).

---

## 📊 Log Analysis Techniques

Instead of reading raw logs directly, filtered approaches were used:

```bash
sudo grep "Failed password" /var/log/auth.log
sudo grep "sudo" /var/log/auth.log
sudo ausearch -k recon_commands -i
sudo aureport -x
```

This improves readability and focuses on relevant events.

---

## 🧠 Key Takeaways

* Linux systems provide valuable security data through native logs
* `auditd` allows targeted monitoring of high-risk actions
* Suspicious behavior often appears as:

  * Failed authentication attempts
  * Privileged command execution
  * System enumeration
  * Persistence mechanisms
* Effective analysis is more about **filtering and interpretation** than collecting massive amounts of data

---

## 📁 Project Structure

```
Linux-Home-SOC/
├── README.md
├── screenshots/
├── notes/
│   ├── failed-logins.md
│   ├── sudo-activity.md
│   ├── recon-commands.md
│   ├── user-management.md
│   └── persistence.md
└── incident-summary.md
```

---

## 🧾 Conclusion

This lab demonstrates how basic system monitoring can be used to detect and analyze suspicious activity on a Linux endpoint.

By combining system logs with targeted audit rules, it is possible to identify behaviors commonly associated with unauthorized access, privilege escalation, and persistence — all without requiring complex enterprise tools.

---

## 🚀 Next Steps

* Integrate a SIEM (e.g., Wazuh or Splunk)
* Add multiple machines for network-level monitoring
* Expand audit rules for broader coverage
* Simulate more advanced attack scenarios

---

## 💡 Notes

This project focuses on **fundamentals over complexity**. The goal is to understand how systems generate and record activity before relying on advanced security platforms.
