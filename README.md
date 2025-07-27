# ansible-nexus


# Ansible Nexus Role

This repository provides an Ansible playbook and role to automate the installation and configuration of [Sonatype Nexus 3](https://www.sonatype.com/products/repository-oss) on a Linux system. It includes service setup, systemd integration, and optional Nginx reverse proxy configuration.

## Prerequisites

- **Java 17** must be installed. You can use the [rhattox/ansible-java](https://github.com/rhattox/ansible-java) role or collection for this purpose.
- Ansible 2.9+

## Quick Start

1. **Install the required collection:**
   ```bash
   ansible-galaxy collection install git+https://github.com/rhattox-ansible/collection-java.git
   ```

2. **Run the playbook:**
   ```bash
   ansible-playbook main.yaml
   ```

## Repository Structure

- `main.yaml` — Main playbook to install Java and Nexus.
- `roles/nexus/` — Main role for Nexus installation.
  - `defaults/main.yaml` — Default variables for the role.
  - `tasks/` — Task files:
    - `pre_install.yaml` — Creates service account and prepares system users/groups.
    - `core_install.yaml` — Downloads, extracts, and installs Nexus.
    - `post_install.yaml` — Configures systemd, Nexus properties, and JRE preferences.
    - `nginx_configuration.yaml` — (Optional) Installs and configures Nginx as a reverse proxy.
  - `templates/` — Jinja2 templates for configuration files and systemd service.

## Variables

The following variables can be customized (see `roles/nexus/defaults/main.yaml`):

- `app_name`: Nexus service name (default: `nexus`)
- `app_version`: Nexus version (default: `3.71.0-06`)
- `app_home`: Installation directory (default: `/var/lib/nexus`)
- `app_archive_url`: Download URL for Nexus tarball
- `corretto_version`: Java version (default: `17.0.13.11.1`)
- `corretto_home_path`: Java home path (default: `/usr/lib/jvm/corretto`)
- `nginx_config_path`: Nginx config path (default: `/etc/nginx/sites-available/nexus.conf`)

## What the Role Does

1. **Pre-Install:**
   - Creates a dedicated system group and user for Nexus.
   - Adds the current user to the Nexus group for management.
2. **Core Install:**
   - Downloads the Nexus tarball.
   - Extracts and copies files to the installation directory.
3. **Post-Install:**
   - Generates JRE preference and Nexus properties files from templates.
   - Sets up a systemd service for Nexus.
   - Reloads systemd.
4. **(Optional) Nginx Reverse Proxy:**
   - Installs Nginx and configures it as a reverse proxy for Nexus.

## Templates

- `nexus.conf.j2`: Nginx reverse proxy configuration
- `nexus.service.j2`: Systemd service unit for Nexus
- `nexus.properties.j2`: Nexus application properties
- `pref_jre.cfg.j2`: JRE preference for Nexus

## Example Playbook (`main.yaml`)

```yaml
- name: Nexus Installation
  hosts: localhost
  become: true
  gather_facts: true
  collections:
    - rhattox.java_installation
  vars:
    ansible_python_interpreter: /opt/ansible-venv/bin/python
  roles:
    - role: java
      vars:
        java_version: 17
    - nexus
```

## Notes

- The Nginx configuration is included but commented out by default. Uncomment in `roles/nexus/tasks/main.yaml` to enable.
- You may need to adjust variables in `roles/nexus/defaults/main.yaml` for your environment.

---
For more details, see the individual files in the `roles/nexus/` directory.