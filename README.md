# Ansible Auditd (Single-File Version)

This playbook installs and configures the `auditd` service on Ubuntu using `ansible-pull`.

## Usage

1. On your Ubuntu VM, install Ansible:
   ```bash
   sudo apt update && sudo apt install ansible -y
   ```

2. Run the playbook with ansible-pull (replace your GitHub username):
   ```bash
   sudo ansible-pull -U https://github.com/<your-username>/ansible-auditd-clean.git -C main playbook.yml
   ```

3. Verify:
   ```bash
   sudo systemctl status auditd
   sudo auditctl -l
   sudo tail -n 50 /var/log/audit/audit.log
   ```

## Deliverables
- /etc/audit/auditd.conf
- /etc/audit/rules.d/audit.rules
- /var/log/audit/audit.log
- /etc/rsyslog.conf
- GitHub repository link
