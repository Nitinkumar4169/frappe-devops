# Frappe DevOps Automation

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Maintained by](https://img.shields.io/badge/Maintained%20by-Nitin%20Kumar-brightgreen)]()
[![Jenkins](https://img.shields.io/badge/Jenkins-Automation-blue)]()
[![Ansible](https://img.shields.io/badge/Ansible-2.9%2B-red)]()

Automated Frappe/ERPNext lifecycle management and deployment using **Jenkins** and **Ansible**. This project streamlines common Frappe administration tasks, reduces manual effort, and provides a repeatable, reliable deployment process.

## 📋 Table of Contents

- [Features](#features)
- [Architecture](#architecture)
- [Technology Stack](#technology-stack)
- [Prerequisites](#prerequisites)
- [Project Structure](#project-structure)
- [Installation & Setup](#installation--setup)
- [Usage Guide](#usage-guide)
- [Implemented Features](#implemented-features)
- [Jenkins Pipeline](#jenkins-pipeline)
- [Security Best Practices](#security-best-practices)
- [Troubleshooting](#troubleshooting)
- [Future Enhancements](#future-enhancements)
- [Contributing](#contributing)
- [License](#license)

## ✨ Features

- ✅ **Automated Site Management** - Create, delete, backup, and restore Frappe sites
- ✅ **Application Deployment** - Download, install, and update applications
- ✅ **Database Migrations** - Automated schema migrations and updates
- ✅ **Health Monitoring** - Real-time server and application health checks
- ✅ **Jenkins Integration** - CI/CD pipeline for automated deployments
- ✅ **GitHub Integration** - Webhook support for automated triggers
- ✅ **Ansible Playbooks** - Modular, reusable automation scripts
- ✅ **SSH-based Execution** - Secure remote command execution
- ✅ **Credential Management** - Secure handling of sensitive information

## 🏗️ Architecture

The deployment flow follows a clean, automated pipeline:

```
Developer
   ↓
Git Push (to GitHub)
   ↓
GitHub Webhook Trigger
   ↓
Jenkins Pipeline
   ↓
Ansible Playbooks
   ↓
Frappe Server(s)
   ↓
Live Environment
```

**Key Components:**

1. **Developer** - Commits code changes to Git repository
2. **GitHub** - Central source control repository with webhook integration
3. **Jenkins** - Orchestrates the CI/CD pipeline and triggers Ansible
4. **Ansible** - Executes automation playbooks on target servers
5. **Frappe Server** - Target deployment environment

## 🛠️ Technology Stack

| Component | Version | Purpose |
|-----------|---------|---------|
| **Jenkins** | 2.0+ | CI/CD Orchestration & Pipeline Management |
| **Ansible** | 2.9+ | Infrastructure Automation & Configuration Management |
| **GitHub** | Latest | Source Control & Webhook Integration |
| **Python** | 3.6+ | Ansible Runtime & Scripting |
| **SSH** | Latest | Secure Remote Communication |
| **Ubuntu Server** | 18.04 LTS+ | Target Operating System |
| **Frappe Framework** | 13+ | Application Framework |
| **MariaDB/MySQL** | 5.7+ | Database Backend |

## 📋 Prerequisites

Before setting up this project, ensure you have:

### On Jenkins Server:
- Jenkins 2.0+ installed and running
- Python 3.6+ installed
- Ansible 2.9+ installed
- Git installed
- SSH client configured

### On Frappe Servers:
- Ubuntu 18.04 LTS or later
- SSH server enabled and accessible from Jenkins
- Frappe Framework installed
- Bench CLI configured
- MariaDB/MySQL running
- Python 3.6+ installed

### Access & Credentials:
- SSH key-based authentication configured
- GitHub repository access (for webhooks)
- Frappe server SSH access
- Jenkins admin credentials

## 📁 Project Structure

```
frappe-devops/
├── README.md                          # This file
├── ansible/
│   ├── ansible.cfg                    # Ansible configuration
│   ├── inventory.ini                  # Host inventory file
│   ├── group_vars/
│   │   └── frappe.yml.example         # Example group variables
│   ├── playbooks/                     # Main automation playbooks
│   │   ├── backup.yml                 # Backup site data
│   │   ├── create_site.yml            # Create new Frappe site
│   │   ├── deploy_app.yml             # Deploy application updates
│   │   ├── drop_site.yml              # Delete a Frappe site
│   │   ├── get_app.yml                # Download applications to bench
│   │   ├── health_check.yml           # Check server and app health
│   │   ├── install_app.yml            # Install app on a site
│   │   ├── migrate_site.yml           # Run database migrations
│   │   └── restore_site.yml           # Restore site from backup
│   └── roles/                         # Reusable Ansible roles
│       ├── app_deployment/            # Application deployment role
│       ├── backup/                    # Backup operations role
│       ├── create_site/               # Site creation role
│       ├── drop_site/                 # Site deletion role
│       ├── get_app/                   # App download role
│       ├── health_check/              # Health monitoring role
│       ├── install_app/               # App installation role
│       ├── migration/                 # Database migration role
│       └── restore_site/              # Restore operations role
├── jenkins/
│   └── Jenkinsfile                    # Jenkins pipeline definition
└── docs/
    ├── SETUP.md                       # Detailed setup instructions
    ├── TROUBLESHOOTING.md             # Common issues & solutions
    └── CONTRIBUTING.md                # Contribution guidelines
```

## ⚙️ Installation & Setup

### Step 1: Clone the Repository

```bash
git clone https://github.com/Nitinkumar4169/frappe-devops.git
cd frappe-devops
```

### Step 2: Configure Ansible Inventory

```bash
# Copy the example inventory
cp ansible/group_vars/frappe.yml.example ansible/group_vars/frappe.yml

# Edit with your server details
nano ansible/group_vars/frappe.yml
```

**Example `frappe.yml` Configuration:**

```yaml
---
# Frappe Server Configuration
bench_path: /home/frappe-user/frappe-bench
frappe_user: frappe-user
frappe_port: 8000

# Database Configuration
mariadb_root_password: your_secure_password_here
mariadb_host: localhost
mariadb_port: 3306

# Backup Configuration
backup_retention_days: 30
backup_path: /backups/frappe

# Application Settings
enable_ssl: true
ssl_cert_path: /etc/ssl/certs
```

### Step 3: Configure SSH Access

```bash
# Generate SSH key pair (if not already done)
ssh-keygen -t rsa -b 4096 -f ~/.ssh/frappe_deploy

# Copy public key to Frappe servers
ssh-copy-id -i ~/.ssh/frappe_deploy.pub frappe-user@your-server-ip

# Add key to Jenkins credentials
# Go to Jenkins → Manage Credentials → Global Credentials → Add Credentials
# Select: SSH Username with private key
# Upload your private key
```

### Step 4: Configure Jenkins Pipeline

1. **Create New Jenkins Job:**
   - Go to Jenkins Dashboard → New Item
   - Select "Pipeline" job type
   - Name it (e.g., "Frappe-DevOps-Pipeline")

2. **Configure Pipeline:**
   - Under "Pipeline" section, select "Pipeline script from SCM"
   - Set SCM to "Git"
   - Enter repository URL: `https://github.com/Nitinkumar4169/frappe-devops.git`
   - Set branch to `*/main`
   - Script path: `jenkins/Jenkinsfile`

3. **Set up Webhook:**
   - Go to GitHub repository → Settings → Webhooks
   - Add webhook with Jenkins URL: `http://your-jenkins-ip/github-webhook/`
   - Select "Push events" and "Pull requests"

### Step 5: Install Ansible Requirements

```bash
# On Jenkins server
pip install ansible paramiko pyyaml

# Or using requirements.txt
pip install -r requirements.txt
```

## 🚀 Usage Guide

### Running Individual Playbooks

#### 1. Health Check
Monitor server and application availability:

```bash
ansible-playbook ansible/playbooks/health_check.yml
```

**Output Example:**
```
TASK [Check Frappe Server Status]
ok: [frappe-prod]

TASK [Check Application Port]
ok: [frappe-prod]
```

#### 2. Create New Site

Create a fresh Frappe site with database:

```bash
ansible-playbook ansible/playbooks/create_site.yml \
  -e "site_name=erp.example.com \
      admin_password=secure_password"
```

**Variables:**
- `site_name` (required) - Domain name for the site
- `admin_password` (required) - Administrator password

#### 3. Get Application

Download an application into the bench:

```bash
ansible-playbook ansible/playbooks/get_app.yml \
  -e "app_name=crm \
      repo_url=https://github.com/frappe/crm.git \
      branch=version-15"
```

**Variables:**
- `app_name` (required) - Application name
- `repo_url` (required) - Git repository URL
- `branch` (required) - Git branch to checkout

#### 4. Install Application

Install an application on a specific site:

```bash
ansible-playbook ansible/playbooks/install_app.yml \
  -e "site_name=erp.example.com app_name=crm"
```

#### 5. Backup Site

Create a backup of site data and database:

```bash
ansible-playbook ansible/playbooks/backup.yml \
  -e "site_name=erp.example.com"
```

Backup files are stored in: `/backups/frappe/site_name/`

#### 6. Migrate Site

Run database migrations (typically after updates):

```bash
ansible-playbook ansible/playbooks/migrate_site.yml \
  -e "site_name=erp.example.com"
```

#### 7. Restore Site

Restore a site from a previous backup:

```bash
ansible-playbook ansible/playbooks/restore_site.yml \
  -e "site_name=erp.example.com \
      backup_file=backup_timestamp.tar"
```

#### 8. Deploy Application

Deploy latest code changes and run migrations:

```bash
ansible-playbook ansible/playbooks/deploy_app.yml \
  -e "site_name=erp.example.com"
```

**Pipeline:**
1. Git Pull (latest code)
2. Bench Migrate (database updates)
3. Clear Cache (refresh application)

#### 9. Drop Site

Permanently delete a site (after verification):

```bash
ansible-playbook ansible/playbooks/drop_site.yml \
  -e "site_name=erp.example.com \
      confirm=yes"
```

## 📊 Jenkins Pipeline

### Pipeline Stages

The Jenkins pipeline executes in the following order:

```
Stage 1: Checkout
├─ Pull latest code from GitHub
└─ Verify repository integrity

Stage 2: Validation
├─ Validate Ansible syntax
├─ Check inventory connectivity
└─ Verify target server accessibility

Stage 3: Deploy
├─ Git Pull (latest code on server)
├─ Bench Migrate (run migrations)
├─ Clear Cache (refresh application)
└─ Health Check (verify deployment)

Stage 4: Post-Deployment
├─ Run smoke tests
├─ Verify application logs
└─ Notify team (Slack/Email)
```

### Success Workflow Example

```
TASK [Pull latest code]
changed: [frappe-prod] - Code updated from origin/main

TASK [Run migration]
changed: [frappe-prod] - 5 migrations applied successfully

TASK [Clear cache]
changed: [frappe-prod] - Cache cleared

PLAY RECAP
frappe-prod: ok=3 changed=3 failed=0 unreachable=0 skipped=0
```

### Jenkins Parameter Support

The pipeline supports parameters for flexibility:

```groovy
parameters {
  choice(name: 'ACTION', choices: [
    'create_site', 'get_app', 'install_app', 
    'backup', 'restore', 'migrate', 'drop_site', 
    'health_check', 'deploy'
  ], description: 'Select deployment action')
  
  string(name: 'SITE_NAME', description: 'Frappe site name (e.g., erp.example.com)')
  string(name: 'APP_NAME', description: 'Application name (e.g., crm)')
  string(name: 'REPO_URL', description: 'Git repository URL')
  string(name: 'BRANCH', description: 'Git branch to deploy')
  string(name: 'BACKUP_FILE', description: 'Backup file for restore operation')
}
```

## 🔒 Security Best Practices

### 1. Credential Management

**Sensitive information should NOT be committed to Git:**

```bash
# ❌ DO NOT commit
ansible/group_vars/frappe.yml          # Contains passwords
jenkins/secrets.txt                     # Jenkins credentials

# ✅ DO THIS INSTEAD
ansible/group_vars/frappe.yml.example   # Template only
```

**Use environment variables:**

```bash
export ANSIBLE_VAULT_PASSWORD_FILE=~/.vault_password
ansible-playbook playbooks/deploy_app.yml --vault-id @prompt
```

### 2. SSH Key Security

```bash
# Generate strong SSH key
ssh-keygen -t ed25519 -C "frappe-deploy" -f ~/.ssh/frappe_deploy

# Set restrictive permissions
chmod 600 ~/.ssh/frappe_deploy
chmod 644 ~/.ssh/frappe_deploy.pub

# Restrict key usage in authorized_keys
# command="only_this_command",no-X11-forwarding ssh-ed25519 AAAA...
```

### 3. Ansible Vault for Secrets

Encrypt sensitive variables:

```bash
# Create encrypted variable file
ansible-vault create ansible/group_vars/vault.yml

# Edit encrypted file
ansible-vault edit ansible/group_vars/vault.yml

# Run playbook with vault
ansible-playbook playbooks/deploy_app.yml --vault-password-file ~/.vault_password
```

### 4. Jenkins Security

- ✅ Enable authentication and authorization
- ✅ Use Jenkins credentials plugin for secrets
- ✅ Restrict job execution to trusted users
- ✅ Enable CSRF protection
- ✅ Use HTTPS for Jenkins UI
- ✅ Regularly update Jenkins and plugins

### 5. Network Security

- ✅ Whitelist Jenkins IP on Frappe servers
- ✅ Use VPN/SSH tunnel for remote connections
- ✅ Enable firewall rules on application servers
- ✅ Restrict SSH access to specific IPs
- ✅ Use security groups in cloud environments

## 🐛 Troubleshooting

### Issue 1: SSH Connection Refused

**Error:**
```
fatal: [frappe-prod]: UNREACHABLE! => {
  "changed": false,
  "msg": "ssh: connect to host 192.168.1.10 port 22: Connection refused"
}
```

**Solution:**
```bash
# 1. Verify SSH service is running
ssh -v frappe-user@192.168.1.10

# 2. Check firewall rules
sudo ufw status
sudo ufw allow 22/tcp

# 3. Verify SSH key permissions
chmod 600 ~/.ssh/frappe_deploy
chmod 700 ~/.ssh

# 4. Test SSH connectivity
ssh -i ~/.ssh/frappe_deploy frappe-user@192.168.1.10 "echo OK"
```

### Issue 2: Ansible Playbook Syntax Error

**Error:**
```
ERROR! Unexpected failure during task execution.
```

**Solution:**
```bash
# Validate playbook syntax
ansible-playbook ansible/playbooks/deploy_app.yml --syntax-check

# Run with verbose output
ansible-playbook ansible/playbooks/deploy_app.yml -vvv

# Check YAML formatting
yamllint ansible/playbooks/*.yml
```

### Issue 3: Database Migration Failure

**Error:**
```
TASK [Run bench migrate] FAILED!
```

**Solution:**
```bash
# 1. Check Frappe logs
tail -f /path/to/frappe-bench/logs/bench.log

# 2. Verify database connectivity
mysql -u frappe -p -e "SELECT 1;"

# 3. Check for locked tables
mysql -u frappe -p frappe_site -e "SHOW OPEN TABLES WHERE In_use > 0;"

# 4. Run migration manually on server
cd /home/frappe-user/frappe-bench
bench migrate
```

### Issue 4: Backup File Not Found

**Error:**
```
FAILED! File not found: /backups/frappe/site_name/backup_2024*.tar
```

**Solution:**
```bash
# 1. Verify backup path exists
ls -la /backups/frappe/

# 2. Check backup retention settings
grep backup_retention_days ansible/group_vars/frappe.yml

# 3. Create manual backup
cd /home/frappe-user/frappe-bench
bench backup site_name

# 4. List available backups
ls -lah /home/frappe-user/frappe-bench/sites/site_name/private/backups/
```

### Issue 5: Jenkins Pipeline Timeout

**Error:**
```
Pipeline job exceeded timeout: 3600 seconds
```

**Solution:**
```groovy
// In Jenkinsfile, increase timeout
timeout(time: 2, unit: 'HOURS') {
  stage('Deploy') {
    steps {
      // deployment steps
    }
  }
}

// Or run playbook with timeout
ansible-playbook playbooks/deploy_app.yml --timeout=900
```

## 🚧 Future Enhancements

### Planned Features

- [ ] **Multi-Environment Support**
  - DEV, UAT, and PROD environment templates
  - Environment-specific variables and configurations

- [ ] **Enhanced Notifications**
  - Slack integration for deployment alerts
  - Email notifications for failures
  - Dashboard for deployment history

- [ ] **Rollback Automation**
  - One-click rollback to previous versions
  - Automated backup before deployments
  - Version tracking and management

- [ ] **Multi-Server Deployment**
  - Load balancer integration
  - Parallel deployment across multiple servers
  - Server health monitoring

- [ ] **Monitoring & Observability**
  - Prometheus metrics export
  - Grafana dashboards
  - CloudWatch/ELK integration
  - Real-time alerting

- [ ] **Scheduled Operations**
  - Automated backup scheduling
  - Maintenance windows
  - Automated health checks
  - Cache cleanup jobs

- [ ] **Vault Integration**
  - HashiCorp Vault integration
  - Automated secret rotation
  - Audit logging for secrets

- [ ] **Advanced Reporting**
  - Deployment history reports
  - Performance metrics
  - Compliance audit reports

## 📝 Contributing

Contributions are welcome! Please follow these guidelines:

1. **Fork the repository**
   ```bash
   git clone https://github.com/YOUR-USERNAME/frappe-devops.git
   ```

2. **Create a feature branch**
   ```bash
   git checkout -b feature/your-feature-name
   ```

3. **Make your changes**
   - Follow existing code style
   - Update documentation
   - Test thoroughly

4. **Commit with clear messages**
   ```bash
   git commit -m "feat: add new deployment feature"
   ```

5. **Push and create a Pull Request**
   ```bash
   git push origin feature/your-feature-name
   ```

## 📄 License

This project is licensed under the **MIT License** - see the [LICENSE](LICENSE) file for details.

## 👤 Author

**Nitin Kumar**
- GitHub: [@Nitinkumar4169](https://github.com/Nitinkumar4169)
- DevOps Automation Specialist

## 🤝 Support

For issues, questions, or suggestions:

1. **Open an Issue** - [GitHub Issues](https://github.com/Nitinkumar4169/frappe-devops/issues)
2. **Check FAQ** - See [TROUBLESHOOTING.md](docs/TROUBLESHOOTING.md)
3. **Review Documentation** - See [docs/](docs/) folder

## 📚 Additional Resources

- [Frappe Framework Documentation](https://frappeframework.com)
- [Ansible Documentation](https://docs.ansible.com)
- [Jenkins Documentation](https://jenkins.io/doc)
- [Ubuntu Server Guide](https://ubuntu.com/server/docs)

---

**Made with ❤️ by Nitin Kumar**

Last Updated: 2024 | Version: 1.0.0
