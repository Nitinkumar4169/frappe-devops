# Frappe DevOps Automation

Automated Frappe/ERPNext lifecycle management using Jenkins and Ansible.

---

## Overview

This project automates common Frappe administration tasks and deployment operations.

The automation is designed to reduce manual effort and provide a repeatable deployment process.

---

## Technology Stack

- Jenkins
- Ansible
- GitHub
- SSH
- Ubuntu Server
- Frappe Framework


---

## Architecture

Developer
   ↓
Git Push
   ↓
GitHub Repository
   ↓
Jenkins Pipeline
   ↓
Ansible Playbooks
   ↓
Frappe Server

---

## Project Structure


ansible/
├── ansible.cfg
├── inventory.ini
├── group_vars/
│   └── frappe.yml.example
├── playbooks/
│   ├── backup.yml
│   ├── create_site.yml
│   ├── deploy_app.yml
│   ├── drop_site.yml
│   ├── get_app.yml
│   ├── health_check.yml
│   ├── install_app.yml
│   ├── migrate_site.yml
│   └── restore_site.yml
└── roles/
    ├── app_deployment
    ├── backup
    ├── create_site
    ├── drop_site
    ├── get_app
    ├── health_check
    ├── install_app
    ├── migration
    └── restore_site

Implemented Features
Health Check

Checks server and application availability.

ansible-playbook playbooks/health_check.yml
Create Site

Creates a new Frappe site.

ansible-playbook playbooks/create_site.yml \
-e "site_name=test.local"
Get App

3Downloads an application into the bench.

ansible-playbook playbooks/get_app.yml \
-e "app_name=crm \
repo_url=https://github.com/frappe/crm.git \
branch=version-15"
Install App

#Installs an application on a site.

ansible-playbook playbooks/install_app.yml \
-e "site_name=test.local app_name=crm"
Backup Site

#Creates a site backup.

ansible-playbook playbooks/backup.yml \
-e "site_name=test.local"
Restore Site

Restores a site from backup.

ansible-playbook playbooks/restore_site.yml
Migrate Site

#Runs database migrations.

ansible-playbook playbooks/migrate_site.yml \
-e "site_name=test.local"
Drop Site

#Deletes a site after verification.

ansible-playbook playbooks/drop_site.yml \
-e "site_name=test.local"
Jenkins CI/CD Pipeline

Current Pipeline Workflow:

Pull latest code from GitHub.
Connect to target Frappe server.
Execute Ansible playbooks.
Deploy latest changes.
Run migration.
Clear cache.

Pipeline Stages:

Checkout
   ↓
Deploy
   ├── Git Pull
   ├── Bench Migrate
   └── Clear Cache
Jenkins Success Example
TASK [Pull latest code]
changed

TASK [Run migration]
changed

TASK [Clear cache]
changed

PLAY RECAP
ok=3 changed=3 failed=0
Security

Sensitive information is excluded from Git.

Example:

# frappe.yml.example

bench_path: /home/frappe-user/frappe-bench
mariadb_root_password: CHANGE_ME

Actual credentials remain local and are not committed.

Achievements

#Implemented:

Site Creation Automation
Site Backup Automation
Site Restore Automation
Site Migration Automation
Site Deletion Automation
App Download Automation
App Installation Automation
Health Check Automation
Jenkins Deployment Pipeline
GitHub Integration
SSH-based Remote Execution
Future Enhancements
Jenkins Parameterized Pipeline
ACTION
SITE_NAME
APP_NAME
REPO_URL
BRANCH
BACKUP_FILE

Supported actions:

create_site
get_app
install_app
backup
restore
migrate
drop_site
health_check
Multi-Environment Support
DEV
UAT
PROD
Additional Enhancements
Slack Notifications
Email Alerts
Rollback Automation
Multi-Server Deployment
Monitoring Dashboard
Scheduled Backups
Ansible Vault Integration
Author

Nitin Kumar

DevOps Automation Project using Jenkins, Ansible and Frappe Framework.


This README reflects what you've actually built so far and looks strong enough for GitHub, interviews, and internal company documentation.
