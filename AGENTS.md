# AGENTS.md - AI Agent Documentation

This document provides AI agents with essential information about the ansible-role-kubya project.

## 1. 🎯 Project Overview

**Project Name**: ansible-role-kubya
**Type**: Ansible Role
**Purpose**: Automated developer workstation setup for Kubernetes and DevOps environments
**License**: GPL-2.0-or-later
**Maintainer**: CodeSuiteApp

### Core Functionality

This Ansible role automates the following tasks:
- Installation of modern CLI tools (bat, delta, eza, fd-find, fzf, ripgrep, zoxide)
- SSH key deployment and configuration
- Kubernetes environment setup (kubeconfig, K9s)
- Git repository cloning (kubya, kubya-playbooks, jenkins-docker)
- Dotfiles management via Chezmoi
- Shell environment customization (zsh/bash)
- Development workspace initialization

## 2. 🛠️ Tech Stack

### Core Technologies
- **Configuration Management**: Ansible 2.1+
- **Shell**: Bash/Zsh
- **Dotfiles Manager**: Chezmoi
- **Version Control**: Git
- **Container Orchestration**: Kubernetes (optional)

### CLI Tools Installed
- `bat` - cat replacement with syntax highlighting
- `delta` - Git diff tool with syntax highlighting
- `eza` - Modern ls replacement
- `fd-find` - Fast find alternative
- `fzf` - Fuzzy finder
- `ripgrep` - Fast grep alternative
- `zoxide` - Smart cd command

### Supported Platforms
- RedHat Enterprise Linux (all versions)
- Debian (all versions)
- Ubuntu (all versions)
- Alpine Linux (all versions)
- ArchLinux (all versions)

## 3. 🏗️ Architecture & Project Structure

```
ansible-role-kubya/
├── meta/
│   └── main.yml              # Role metadata, dependencies, platform support
├── defaults/
│   └── main.yml              # Default variables and configuration
├── tasks/
│   └── main.yml              # Main task definitions (325 lines)
├── handlers/
│   └── main.yml              # Event handlers (currently empty)
├── tests/
│   ├── test.yml              # Test playbook
│   └── inventory             # Test inventory (localhost)
├── README.md                 # User documentation (Korean)
└── AGENTS.md                 # AI agent documentation (this file)
```

### Key Files

**meta/main.yml** (741 bytes)
- Role metadata and author information
- Supported platforms and versions
- Minimum Ansible version requirement

**defaults/main.yml** (741 bytes)
- Path variables (user_home, ssh_key, kubeconfig, etc.)
- Feature toggles (use_kube, git_clone, k9s_tail)
- Package list (kubya_packages)
- Workspace directories

**tasks/main.yml** (8345 bytes, 325 lines)
- Package installation
- SSH configuration
- Kubernetes setup
- Git repository cloning
- Dotfiles management
- K9s configuration
- Symbolic link creation (Debian-specific)

## 4. 🔑 Core Domain Concepts

### Variables

#### Path Variables
- `user_home`: User home directory (default: `~`)
- `kubeconfig`: Kubernetes config file path (default: `~/.kube/config`)
- `ssh_key`: SSH private key path (default: `~/.ssh/id_rsa`)
- `ssh_pub`: SSH public key path (default: `~/.ssh/id_rsa.pub`)
- `local_ws`: Workspace directory (default: `~/.local/workspace`)

#### Feature Toggles
- `use_kube` (bool): Enable Kubernetes setup (default: `true`)
- `git_clone` (bool): Enable Git repository cloning (default: `true`)
- `k9s_tail` (int): K9s log tail line count (default: `10000`)

#### Encrypted Variables (Required)
These must be provided via Ansible Vault:
- `enc_id_rsa_key`: SSH private key content
- `enc_id_rsa_pub`: SSH public key content
- `enc_gh_user`: GitHub username
- `enc_gh_token`: GitHub personal access token
- `enc_aval_pw`: Ansible vault password

### Tags

- `pkg`: Package update and installation
- `ssh`: SSH configuration tasks
- `kube`: Kubernetes setup
- `git_clone`: Git repository cloning
- `sync`: Synchronization tasks
- `k9s`: K9s configuration

### Conditional Logic

The role uses several conditions to control task execution:
- `ansible_pkg_mgr != "pacman"`: Skip package install on ArchLinux
- `use_kube == true`: Execute Kubernetes setup
- `git_clone == true`: Execute Git cloning
- `ansible_user_shell == "/usr/bin/zsh"`: Shell-specific tasks
- `ansible_facts['os_family'] == "Debian"`: Debian-specific symlinks

## 5. 📜 Coding Guidelines & Rules

### Must-Do

1. **Security**
   - ALWAYS use Ansible Vault for sensitive variables
   - ALWAYS set SSH key permissions to 0600
   - NEVER commit vault files to version control
   - ALWAYS use minimal privilege GitHub tokens

2. **Variables**
   - USE default variables from `defaults/main.yml`
   - OVERRIDE in playbook vars or group_vars as needed
   - PROVIDE all encrypted variables via Ansible Vault
   - VALIDATE paths before file operations

3. **Tasks**
   - USE tags for all tasks to enable selective execution
   - USE `become: true` for tasks requiring sudo
   - CHECK file existence before modification
   - USE conditional execution (`when:`) appropriately

4. **Platform Compatibility**
   - HANDLE platform-specific differences (e.g., package names)
   - USE `ansible_facts['os_family']` for OS detection
   - USE `ansible_pkg_mgr` for package manager detection
   - CREATE symlinks for Debian-specific package names

5. **Idempotence**
   - ENSURE all tasks are idempotent
   - USE `creates:` parameter for file creation tasks
   - CHECK state before making changes
   - USE `changed_when:` to control change reporting

### Don'ts

1. **Security**
   - DON'T hardcode sensitive information in tasks
   - DON'T use plain text for SSH keys or tokens
   - DON'T skip SSL verification
   - DON'T use weak file permissions

2. **Code Quality**
   - DON'T duplicate task definitions
   - DON'T ignore error handling
   - DON'T use shell commands when Ansible modules exist
   - DON'T skip task documentation

3. **Compatibility**
   - DON'T assume specific package manager
   - DON'T hardcode paths without variables
   - DON'T ignore platform differences
   - DON'T use OS-specific commands without conditionals

4. **Variables**
   - DON'T modify `defaults/main.yml` for user-specific configs
   - DON'T use undefined variables without defaults
   - DON'T mix encrypted and plain variables
   - DON'T override system facts

## 6. ⚙️ Configuration & Environment

### Prerequisites

- Ansible 2.1 or higher
- sudo/root access on target machine
- Internet connection for package installation
- curl (for chezmoi installation on non-ArchLinux)

### Environment Setup

1. **Install Role**
   ```bash
   ansible-galaxy install codesuiteapp.ansible-role-kubya
   ```

2. **Create Vault File**
   ```bash
   ansible-vault create vault.yml
   ```

3. **Define Encrypted Variables**
   ```yaml
   vault_enc_id_rsa_key: |
     -----BEGIN RSA PRIVATE KEY-----
     [SSH private key content]
     -----END RSA PRIVATE KEY-----
   vault_enc_id_rsa_pub: "ssh-rsa AAAAB3..."
   vault_enc_gh_user: "username"
   vault_enc_gh_token: "ghp_token"
   vault_enc_aval_pw: "password"
   ```

4. **Create Playbook**
   ```yaml
   ---
   - hosts: localhost
     become: true
     vars_files:
       - vault.yml
     roles:
       - ansible-role-kubya
   ```

5. **Execute Playbook**
   ```bash
   ansible-playbook playbook.yml --ask-vault-pass
   ```

### Variable Override Examples

**Disable Kubernetes setup:**
```yaml
vars:
  use_kube: false
```

**Disable Git cloning:**
```yaml
vars:
  git_clone: false
```

**Customize K9s tail length:**
```yaml
vars:
  k9s_tail: 5000
```

**Custom workspace location:**
```yaml
vars:
  local_ws: /custom/workspace/path
```

### Tag-based Execution

Execute specific task groups:
```bash
# Only install packages
ansible-playbook playbook.yml --tags pkg

# Only configure SSH
ansible-playbook playbook.yml --tags ssh

# Only Kubernetes setup
ansible-playbook playbook.yml --tags kube

# Only Git cloning
ansible-playbook playbook.yml --tags git_clone

# Multiple tags
ansible-playbook playbook.yml --tags "pkg,ssh,kube"
```

### Debugging

Enable verbose output:
```bash
# Basic verbosity
ansible-playbook playbook.yml -v

# More detailed
ansible-playbook playbook.yml -vvv

# Debug specific task
ansible-playbook playbook.yml --start-at-task="Create SSH directory"
```

### File Permissions

The role automatically sets these permissions:
- SSH config: 0600
- SSH private key: 0600
- SSH directory: 0700 (implicitly via task)

### Repository Cloning Details

When `git_clone: true`, the role clones these repositories:
- **kubya**: Main application repository
- **kubya-playbooks**: Ansible playbooks repository
- **jenkins-docker**: Jenkins Docker configuration

All repositories are:
- Cloned to `~/.local/workspace/`
- Checked out to `dev` branch
- Authenticated using GitHub token

### Chezmoi Integration

**ArchLinux:**
```bash
chezmoi init --apply {{ github_username }}
```

**Other Linux distributions:**
```bash
sh -c "$(curl -fsLS get.chezmoi.io)" -- init --apply {{ github_username }}
```

This automatically:
- Downloads and installs chezmoi
- Initializes dotfiles from GitHub
- Applies dotfiles to home directory

---

**Last Updated**: 2025-11-01
**Document Version**: 1.0
