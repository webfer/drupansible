# DrupAnsible: Automated Drupal Deployments

<br>
<p align="center">
<img src="ansible/assets/images/cover.jpg" width="650">
</p>

## 🚀 **Overview**

DrupAnsible is a comprehensive Ansible-based deployment automation tool designed specifically for Drupal applications. It streamlines the deployment process to remote servers, providing a reliable and repeatable workflow for both staging and production environments.

### Key Features

- 🔄 **Automated Deployments**: Deploy to staging and production with simple commands
- 🔐 **Secure**: Vault-encrypted sensitive data and SSH key-based authentication
- 📦 **Asset Management**: Optional file synchronization between local and remote
- 🗄️ **Database Management**: Automated backups, imports, and updates
- 🔧 **Configuration Management**: Drupal configuration import and update handling
- 🌍 **Translation Support**: Automatic locale module management and translation updates
- ♻️ **Rollback Support**: Quick rollback to previous releases when needed
- 🧹 **Cleanup**: Automated management of old releases and database backups

<br>

## 📋 Prerequisites

Before getting started, ensure the following prerequisites are met:

- **Ansible** (version 2.9 or higher) installed on your local machine
  - Follow the instructions: [Ansible-drupal](https://intranet.tothomweb.com/node/342)
- **Ansistrano Deploy** role installed
  - Installation guide: [Ansistrano-deploy](https://github.com/ansistrano/deploy)
- **Python 3.11** on the remote server (configured in `ansible.cfg`)
- **PyMySQL** for database operations
- SSH access to remote servers with proper authentication
- Basic understanding of Drupal project structure and deployment processes
- **Drush** installed in your Drupal project

<br>

## 🏗️ Architecture

### Project Structure

```
drupansible/
├── ansible.cfg                    # Ansible configuration
├── vault_pass.txt                 # Vault password file (DO NOT commit)
├── LICENSE                        # MIT License
├── README.md                      # This file
└── ansible/
    ├── assets/
    │   └── images/                # Documentation images
    └── core/
        ├── live-deploy.yml        # Production deployment playbook
        ├── stage-deploy.yml       # Staging deployment playbook
        ├── rollback.yml           # Rollback playbook
        ├── inventories/
        │   ├── production/        # Production environment config
        │   │   ├── inventory.yml
        │   │   ├── group_vars/
        │   │   │   ├── deploy_vars.yml
        │   │   │   ├── server.yml
        │   │   │   └── vars.yml
        │   │   └── host_vars/
        │   │       └── live.yml
        │   └── stage/             # Staging environment config
        │       ├── inventory.yml
        │       ├── group_vars/
        │       │   ├── deploy_vars.yml
        │       │   ├── server.yml
        │       │   └── vars.yml
        │       └── host_vars/
        │           └── stage.yml
        └── tasks/
            ├── after-update-code-tasks.yml
            ├── ansistrano_after_symlink_tasks_file.yml
            ├── before-cleanup-tasks.yml
            ├── before-symlink-shared-tasks.yml
            └── before-symlink-tasks.yml
```

### Deployment Playbooks

#### **live-deploy.yml**

Production environment deployment with the following configuration:

- Uses rsync for efficient file transfer
- Excludes development files (.git, node_modules, etc.)
- Manages shared files and directories
- Integrates with production inventory and variables

#### **stage-deploy.yml**

Staging environment deployment with:

- Same deployment strategy as production
- Separate configuration for staging server
- Optional basic authentication support
- Uses staging-specific settings

#### **rollback.yml**

Quick rollback to previous release:

- Uses Ansistrano rollback role
- Restores previous working version
- Maintains database integrity

<br>

## 🔧 Dependencies

This project leverages the following technologies:

- **[Ansible](https://docs.ansible.com/ansible/latest/index.html)** - Automation engine
- **[Ansistrano Deploy](https://github.com/ansistrano/deploy)** - Capistrano-like deployment for Ansible
- **[webfer.drupal_settings](https://github.com/webfer/webfer.drupal_settings)** - Drupal settings generation role
- **Python 3.11** - Interpreter for remote execution
- **MySQL/MariaDB** - Database management
- **PyMySQL** - Python MySQL client library

<br>

## 📦 Installation

This project is designed to be used in combination with the **[MCP Ansible-Drupal](https://github.com/webfer/MCP-Ansible-Drupal)** Model Context Protocol server.

The MCP Ansible-Drupal server provides an automated way to install and manage DrupAnsible within your Drupal projects, offering seamless integration and simplified deployment workflows.

### Getting Started

To install and use DrupAnsible:

1. Set up the **[MCP Ansible-Drupal](https://github.com/webfer/MCP-Ansible-Drupal)** server
2. Follow the installation instructions in the MCP repository
3. Use the MCP tools to automatically install and configure DrupAnsible in your Drupal project

✅ The MCP server will handle the installation, configuration, and setup of the deployment structure for you.

<br>

## ⚙️ Configuration

### Directory Structure

After installation, your project will have the following structure:

```
your-drupal-project/
├── ansible/
│   └── core/
│       ├── inventories/
│       │   ├── production/
│       │   │   ├── inventory.yml
│       │   │   └── group_vars/
│       │   │       ├── deploy_vars.yml  # Deployment configuration
│       │   │       ├── server.yml       # Server settings (vaulted)
│       │   │       └── vars.yml         # Environment variables
│       │   └── stage/
│       │       ├── inventory.yml
│       │       └── group_vars/
│       │           ├── deploy_vars.yml
│       │           ├── server.yml
│       │           └── vars.yml
│       ├── live-deploy.yml
│       └── stage-deploy.yml
├── ansible.cfg                    # Ansible configuration
└── vault_pass.txt                 # Vault password (KEEP SECURE!)
```

### Configuration Files

#### **ansible.cfg**

Main Ansible configuration with:

- Python 3.11 interpreter path
- Vault password file location
- SSH connection settings
- Log file location (`ansible/tmp/logs/`)

#### **vault_pass.txt** 🔐

Contains the password to encrypt/decrypt sensitive variables. **Never commit this file to version control!**

#### **Inventory Files**

Define your server hosts:

- `inventories/production/inventory.yml` - Production servers
- `inventories/stage/inventory.yml` - Staging servers

#### **Variables (Vaulted)** 🔒

Encrypted sensitive data in `server.yml` files:

- `vault_database_name` - Database name
- `vault_database_user` - Database username
- `vault_database_password` - Database password
- `vault_database_port` - Database port
- `vault_server_root_path` - Server root directory
- `vault_server_client` - Client identifier
- `vault_server_domain` - Domain name
- `vault_server_group` - Server group ownership
- `vault_theme_name` - Drupal theme name
- `vault_keep_releases` - Number of releases to keep
- `vault_backup_keep_count` - Number of database backups to keep
- `vault_htpasswd_user` - Basic auth username (staging)
- `vault_htpasswd_pass` - Basic auth password (staging)
- `vault_project_root` - Project root path on server

### Editing Encrypted Variables

To edit vault-encrypted files:

```bash
ansible-vault edit ansible/core/inventories/production/group_vars/server.yml
```

<br>

## 🚀 Deployment

### Command Options

DrupAnsible supports the following deployment options:

| Option          | Description                                                        |
| --------------- | ------------------------------------------------------------------ |
| `--stage`       | Deploy to staging environment with optional basic authentication   |
| `--live`        | Deploy to production environment                                   |
| `--install`     | Initial deployment with complete database import                   |
| `--update`      | Update deployment with configuration import and database updates   |
| `--with-assets` | Synchronize assets folder (files) using rsync with `--delete` flag |

### Initial Deployment

#### Staging Environment

**Without assets:**

```bash
ansible-deploy --stage --install
```

**With assets (includes files sync):**

```bash
ansible-deploy --stage --install --with-assets
```

#### Production Environment

**Without assets:**

```bash
ansible-deploy --live --install
```

**With assets:**

```bash
ansible-deploy --live --install --with-assets
```

### Regular Updates

After the initial deployment, use the `--update` flag for subsequent deployments:

**Staging:**

```bash
ansible-deploy --stage --update
```

**Staging with assets:**

```bash
ansible-deploy --stage --update --with-assets
```

**Production:**

```bash
ansible-deploy --live --update
```

**Production with assets:**

```bash
ansible-deploy --live --update --with-assets
```

<br>

## 🔄 Deployment Process

### What Happens During Deployment

#### 1. **After Update Code** (`after-update-code-tasks.yml`)

- Generates Drupal `settings.php` file using webfer.drupal_settings role
- Creates public files directory (`web/sites/default/files`)
- Creates private files directory (`private`)
- Copies environment-specific settings files (stage/live)

#### 2. **Before Symlink Shared** (`before-symlink-shared-tasks.yml`)

- **Database Backup**: Creates timestamped MySQL dump
- **Backup Cleanup**: Keeps only the 5 most recent backups
- **Permissions**: Sets correct ownership and permissions for:
  - Release directory (755)
  - Files directory (755)
  - Vendor directory (755)
  - Web root directory (755)
  - Config sync directory (755)
- **Database Operations** (with `db_update` tag):
  - Extracts SQL file from gzip archive
  - Imports database using PyMySQL
- **Basic Authentication** (with `auth` tag for staging):
  - Creates `.htpasswd` file
  - Modifies `.htaccess` for authentication
- **Asset Deployment** (with `deploy_assets` tag):
  - Syncs files using rsync with `--delete` flag

#### 3. **Before Symlink** (`before-symlink-tasks.yml`)

- Clears Drupal cache
- **Configuration Import** (with `import_config` tag):
  - Imports Drupal configuration from sync directory
- **Database Updates** (with `updatedb` tag):
  - Runs pending database updates
- Clears Drupal cache again

#### 4. **After Symlink** (`ansistrano_after_symlink_tasks_file.yml`)

- **Translation Management** (with `translations` tag):
  - Checks if locale module is enabled
  - Enables locale module if needed
  - Checks for translation updates
  - Updates translations

#### 5. **Before Cleanup** (`before-cleanup-tasks.yml`)

- Identifies old releases (beyond retention limit)
- Changes ownership of old releases
- Sets permissions for safe deletion
- Ansistrano then removes old releases

### Excluded Files During Deployment

The following files and directories are excluded from deployment:

```
.ddev/
.DS_Store
.editorconfig
.git
.gitattributes
.gitignore
vault_pass.txt
installer.sh
LICENSE
.prettierrc
README.md
bitbucket-pipelines.yml
.vscode
web/sites/default/files
web/themes/custom/{theme_name}/node_modules
web/sites/default/settings.ddev.php
web/sites/default/settings.local.php
```

<br>

## 🔙 Rollback

If something goes wrong, you can quickly rollback to the previous release:

```bash
cd ansible/core
ansible-playbook rollback.yml -i inventories/production/inventory.yml
```

This will restore the previous symlinked release without touching the database.

<br>

## 📊 Logs and Monitoring

### Log Files

Ansible execution logs are stored in:

```
ansible/tmp/logs/ansible-latest.log
```

### Checking Deployment Status

Monitor the deployment process output for:

- ✅ Green: Successful tasks
- 🟡 Yellow: Changed tasks
- 🔴 Red: Failed tasks

<br>

## 🛠️ Advanced Usage

### Using Ansible Tags

Run specific parts of the deployment process using tags:

**Database import only:**

```bash
ansible-playbook ansible/core/stage-deploy.yml -i ansible/core/inventories/stage/inventory.yml --tags "db_update"
```

**Configuration import only:**

```bash
ansible-playbook ansible/core/stage-deploy.yml -i ansible/core/inventories/stage/inventory.yml --tags "import_config"
```

**Translation updates only:**

```bash
ansible-playbook ansible/core/stage-deploy.yml -i ansible/core/inventories/stage/inventory.yml --tags "translations"
```

**Deploy assets only:**

```bash
ansible-playbook ansible/core/stage-deploy.yml -i ansible/core/inventories/stage/inventory.yml --tags "deploy_assets"
```

**Add/Remove basic authentication (staging):**

```bash
# Add authentication
ansible-playbook ansible/core/stage-deploy.yml -i ansible/core/inventories/stage/inventory.yml --tags "auth"

# Remove authentication
ansible-playbook ansible/core/stage-deploy.yml -i ansible/core/inventories/stage/inventory.yml --tags "auth_cleanup"
```

### Ansible Configuration

Key settings in `ansible.cfg`:

- **Python Interpreter**: Uses Python 3.11 from `/opt/alt/python311/bin/python3`
- **Roles Path**: `ansible/core/.roles`
- **SSH Configuration**: Pipelining enabled, host key checking disabled
- **Connection Retries**: 3 attempts with 60s keep-alive
- **Vault**: Password file at `vault_pass.txt`

<br>

## 🔒 Security Best Practices

1. **Never commit** `vault_pass.txt` to version control
2. **Always encrypt** sensitive variables using Ansible Vault
3. **Use SSH keys** for authentication (configured in inventory)
4. **Limit access** to vault password files
5. **Review** `.gitignore` to ensure secrets are excluded
6. **Rotate credentials** regularly
7. **Use basic auth** on staging environments to prevent public access
8. **Keep backups** of database dumps in secure locations

<br>

## 🐛 Troubleshooting

### Common Issues

**Problem**: SSH connection fails

```bash
# Solution: Test SSH connection manually
ssh -p PORT user@hostname

# Check SSH config in inventory.yml
```

**Problem**: Vault decryption fails

```bash
# Solution: Verify vault_pass.txt exists and contains correct password
cat vault_pass.txt

# Try manual decryption
ansible-vault view ansible/core/inventories/stage/group_vars/server.yml
```

**Problem**: Database import fails

```bash
# Solution: Check database credentials
# Verify PyMySQL is installed on remote server
pip3 install PyMySQL
```

**Problem**: Permission denied errors

```bash
# Solution: Check ownership and permissions
ls -la /path/to/deployment/directory

# Ensure ansible_user has proper permissions
```

**Problem**: Configuration import conflicts

```bash
# Solution: Check Drupal configuration sync directory
drush config:status

# Export current config
drush config:export
```

<br>

## 📚 Additional Resources

- [Ansible Documentation](https://docs.ansible.com/ansible/latest/index.html)
- [Ansistrano Deploy Documentation](https://github.com/ansistrano/deploy)
- [Drupal Configuration Management](https://www.drupal.org/docs/configuration-management)
- [Drush Commands](https://www.drush.org/latest/commands/)
- [Internal Wiki: Ansible-Drupal](https://intranet.tothomweb.com/node/342)

<br>

## 🤝 Contributing

This Ansible application is specifically designed to streamline Drupal deployments to remote servers (Okitup hosting), providing a tailored solution for efficient and reliable deployment workflows.

We encourage you to explore and utilize this application for your deployment needs. If you find it beneficial and wish to contribute to its development, your participation would be greatly appreciated. By collaborating, we can enhance the functionality and robustness of this application, benefiting the broader community and fostering a spirit of shared innovation.

### How to Contribute

1. **Fork** the repository
2. **Create** a feature branch (`git checkout -b feature/amazing-feature`)
3. **Commit** your changes (`git commit -m 'Add some amazing feature'`)
4. **Push** to the branch (`git push origin feature/amazing-feature`)
5. **Open** a Pull Request

<br>

## 📝 License

This project is licensed under the [MIT License](https://mit-license.org/).

<br>

## 👥 Maintainers

- **Organization**: [webfer](https://github.com/webfer)
- **Repository**: [drupansible](https://github.com/webfer/drupansible)

<br>

---

<p align="center">
Made with ❤️ for streamlined Drupal deployments
</p>
