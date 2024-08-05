# DevOps Ansible Playbook

This repository contains Ansible playbooks and roles for setting up and configuring a DevOps environment. The playbooks have been tested on a server with the following specifications:
- **OS:** Debian 12.6
- **CPU:** 4 cores
- **Memory:** 5 GB
- **Ansible:** 2.9.22

The repository is designed to work with the following domain names, for which SSL certificates will be issued:
- devops.example.com
- kibana.example.com
- grafana.example.com
- prometheus.example.com

## Prerequisites

1. **DNS Setup:**
   Add the following A-records in your DNS for your domain:
   - devops
   - kibana
   - grafana
   - prometheus
   Or add a wildcard record `*.example.com`.

2. **Configuration:**
   - Update the default settings in the `hosts` file and `./group_vars/all/inventory.yml` to replace `example.com` with your domain.
   - Add your SSH public key to the `files/authorized_keys/deploy.pub` file.
   - Install necessary Ansible dependencies:
     ```sh
     ansible-galaxy collection install community.grafana
     ```

## Usage

This repository includes two primary playbooks:

1. **playbook/user.yml:**
   Creates the main user `deploy` for further configurations.

2. **playbook/devops.yml:**
   The main playbook that installs and configures the following:
   - Docker
   - Nginx Proxy
   - Certbot
   - Nginx Web
   - ELK Stack
   - Prometheus
   - Grafana

### Steps to Execute

1. **Run the User Creation Playbook:**
   ```sh
   ansible-playbook -i hosts playbook/user.yml --user $USER --ask-pass --ask-become-pass

Replace $USER with your username that has su privileges.

2. **Run the Main Playbook:**
  ```sh
  ansible-playbook -i hosts playbook/devops.yml -e "certbot_domains=devops.example.com,kibana.example.com,grafana.example.com,prometheus.example.com"
  ```
Replace example.com with your domain.
Using Tags
In the main playbook playbook/devops.yml, you can run specific roles using tags.

Note:
If you want to run roles individually using tags, ensure that you run the certbot role beforehand to issue the SSL certificates for the respective domain. For example:
```sh
ansible-playbook -i hosts playbook/devops.yml -e "certbot_domains=grafana.example.com" --tags certbot
```
Or combine tags:
```sh
ansible-playbook -i hosts playbook/devops.yml -e "certbot_domains=grafana.example.com" --tags common,docker,nginx-proxy,certbot,prometheus,prometheus-web
```
Additional Information
* Ensure that the certbot role is run before running the nginx-web, elk-kibana-web, prometheus-web, and grafana-web roles to issue SSL certificates for the specified domains.
* The provided configurations and playbooks are designed to be flexible and can be customized as per your requirements.
