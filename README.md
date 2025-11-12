# Ansible + Auditd Configuration Lab

## Overview
This project automates the installation and configuration of Auditd (the Linux Auditing Daemon) on an Ubuntu 24.x virtual machine using Ansible and GitHub.  
Auditd monitors and logs critical system events such as file access, configuration changes, and privilege use.  
By using ansible-pull, the system can self-configure by pulling this playbook directly from GitHub, ensuring consistent and repeatable deployments.

---

## System Requirements
- Operating System: Ubuntu 24.04 LTS (or compatible)
- Ansible Version: 2.15 or newer  
  Install using:
  ```bash
  sudo apt update && sudo apt install ansible -y
  ```
- Network Requirements:  
  Internet access to reach GitHub
- Privileges:  
  Must be run with sudo or as a user with elevated privileges

---

## Repository Structure
```
ansible-auditd-clean/
├── playbook.yml      # Main Ansible playbook that installs and configures auditd
└── README.md         # Documentation and usage guide (this file)
```

---

## How to Use
1. Install Ansible:
   ```bash
   sudo apt update && sudo apt install ansible -y
   ```

2. Run the playbook using ansible-pull:
   ```bash
   sudo ansible-pull -U https://github.com/<your-username>/ansible-auditd-clean.git -C main playbook.yml
   ```

3. Verify successful setup:
   ```bash
   sudo systemctl status auditd
   sudo auditctl -l
   ```

---

## Audit Rules Implemented
The playbook deploys the following audit rules via /etc/audit/rules.d/audit.rules:

| Rule | Description |
|------|--------------|
| -w /etc/passwd -p wa -k passwd_changes | Watches /etc/passwd for writes or attribute changes (user account modifications). |
| -w /etc/sudoers -p wa -k sudo_changes | Monitors any edits to the sudoers file (privilege configuration). |
| -w /var/log/ -p wa -k log_access | Tracks any write or attribute changes to system log files. |

These rules help detect unauthorized system modifications and privilege escalation attempts.

---

## Architecture Diagram

```text
           ┌────────────────────────────┐
           │         GitHub Repo         │
           │  (hosts playbook.yml)       │
           └──────────────┬──────────────┘
                          │ ansible-pull
                          ▼
           ┌────────────────────────────┐
           │     Ubuntu 24.x VM          │
           │  Ansible client executes    │
           │  playbook to install Auditd │
           └──────────────┬──────────────┘
                          │
                          ▼
           ┌────────────────────────────┐
           │        Auditd Service       │
           │   Logs monitored events     │
           │   (/var/log/audit/audit.log)│
           └────────────────────────────┘
```

---

## Verification Steps
After running the playbook, verify Auditd is active and logging:

```bash
sudo systemctl status auditd              # Should show 'active (running)'
sudo auditctl -l                          # Lists loaded audit rules
sudo ausearch -k log_access               # Search for triggered audit events
sudo tail -n 20 /var/log/audit/audit.log  # View recent audit logs
```

Example output from auditctl -l:
```
-w /etc/passwd -p wa -k passwd_changes
-w /etc/sudoers -p wa -k sudo_changes
-w /var/log/ -p wa -k log_access
```

---

## Troubleshooting Tips

| Issue | Possible Cause | Solution |
|-------|----------------|-----------|
| auditctl -l shows no rules | Audit rules file not applied | Run `sudo augenrules --load` |
| ansible-pull fails to connect | Repo URL or branch incorrect | Verify repo URL and branch name (main) |
| auditd not running | Service not enabled | Run `sudo systemctl enable --now auditd` |
| Permission denied errors | Not using sudo | Always run commands with sudo |

---

## License / Notes
This lab was created for educational purposes to demonstrate configuration management with Ansible and system auditing using Auditd.  
It is safe for local VM use and follows standard Ubuntu audit configuration practices.

---

## Repository Link
GitHub Repository: https://github.com/<your-username>/ansible-auditd-clean
