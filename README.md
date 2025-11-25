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

The MCP Ansible-Drupal server provides tools to manage vault-encrypted configuration files securely. You can edit encrypted variables for both staging and production environments through the MCP interface.

For manual editing, you can use Ansible Vault commands directly if needed.

<br>

## 🚀 Deployment

### Deployment with MCP Ansible-Drupal

Deployments are managed through the **[MCP Ansible-Drupal](https://github.com/webfer/MCP-Ansible-Drupal)** server, which provides an intuitive interface for executing deployments.

### Deployment Options

The MCP server supports the following deployment configurations:

| Option          | Description                                                      |
| --------------- | ---------------------------------------------------------------- |
| **Environment** | Choose between `staging` or `production`                         |
| **Mode**        | `install` (initial deployment) or `update` (subsequent updates)  |
| **Assets**      | Option to synchronize assets folder (files) with `--delete` flag |

### Initial Deployment

Use the MCP Ansible-Drupal tools to perform your initial deployment:

#### Staging Environment

- Deploy to staging with database import
- Optionally include asset synchronization
- Basic authentication can be configured for staging

#### Production Environment

- Deploy to production with database import
- Optionally include asset synchronization
- Uses production-specific configuration

### Regular Updates

For subsequent deployments after the initial setup:

- Use the MCP tools to deploy updates
- Configuration changes are automatically imported
- Database updates are applied
- Optional asset synchronization available

Refer to the **[MCP Ansible-Drupal documentation](https://github.com/webfer/MCP-Ansible-Drupal)** for detailed usage instructions.

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

If something goes wrong, you can quickly rollback to the previous release using the MCP Ansible-Drupal tools.

The rollback functionality will restore the previous symlinked release without touching the database, ensuring a safe recovery to the last known working version.

Refer to the **[MCP Ansible-Drupal documentation](https://github.com/webfer/MCP-Ansible-Drupal)** for rollback instructions.

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

### Using Ansible Tags with MCP

The MCP Ansible-Drupal server allows you to run specific parts of the deployment process using Ansible tags. This gives you fine-grained control over deployment operations.

**Available tag operations:**

- **Database import** (`db_update`) - Import database only
- **Configuration import** (`import_config`) - Import Drupal configuration only
- **Translation updates** (`translations`) - Update translations only
- **Deploy assets** (`deploy_assets`) - Synchronize files only
- **Basic authentication** (`auth` / `auth_cleanup`) - Add or remove staging authentication

Refer to the **[MCP Ansible-Drupal documentation](https://github.com/webfer/MCP-Ansible-Drupal)** for instructions on executing tag-specific deployments.

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

- **Solution**: Verify SSH configuration in your inventory files
- Check that SSH keys are properly configured
- Ensure the remote server is accessible
- The MCP Ansible-Drupal tools will help diagnose connection issues

**Problem**: Vault decryption fails

- **Solution**: Verify `vault_pass.txt` exists and contains the correct password
- Check file permissions on the vault password file
- Use the MCP tools to validate vault configuration

**Problem**: Database import fails

- **Solution**: Check database credentials in vault-encrypted files
- Verify PyMySQL is installed on the remote server
- Ensure database permissions are correct
- Review the Ansible logs for detailed error messages

**Problem**: Permission denied errors

- **Solution**: Verify ownership and permissions on deployment directories
- Ensure the Ansible user has proper permissions
- Check that the server group is correctly configured

**Problem**: Configuration import conflicts

- **Solution**: Check Drupal configuration sync directory status
- Review configuration differences using Drush
- Resolve conflicts before deployment
- Export current configuration if needed

<br>

## 📚 Additional Resources

- [Ansible Documentation](https://docs.ansible.com/ansible/latest/index.html)
- [Ansistrano Deploy Documentation](https://github.com/ansistrano/deploy)
- [Drupal Configuration Management](https://www.drupal.org/docs/configuration-management)
- [Drush Commands](https://www.drush.org/latest/commands/)
- [Internal Wiki: Ansible-Drupal](https://intranet.tothomweb.com/node/342)

<br>

## 🤝 Contributing

This Ansible application is specifically designed to streamline Drupal deployments to remote servers with the most common server configurations, providing a tailored solution for efficient and reliable deployment workflows.

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
