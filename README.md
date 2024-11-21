# Ansible Playbook to deploy Celestia Bridge Node

This Ansible playbook automates the deployment and configuration of Celestia Bridge Node. It ensures that the necessary dependencies, configuration files, and services are properly set up and running.

## Table of Contents

- [Requirements](#requirements)
- [Setup](#setup)
- [Variables](#variables)
- [Usage](#usage)

## Requirements

Before using this playbook, ensure the following requirements are met:

1. **Ansible version**: Make sure you have Ansible 2.15+ installed.
2. **SSH Access**: Passwordless SSH access to all target servers.
3. **Python**: Python 3.x installed on the control node and all target hosts.
4. **Privileges**: The user running the playbook must have sudo privileges on the target machines.

**Note**: The following ansible playbook dynamically fetches private bridge and node keys from hashicorp vault. 

## Setup

### 1. Install Ansible

If Ansible is not installed, visit the official documentation for detailed instructions on how to install Ansible on various Linux distributions:

[Ansible Installation Guide](https://docs.ansible.com/ansible/latest/installation_guide/installation_distros.html)


### 2. Clone the repository

Clone this repository to your Ansible control node:

```bash
git clone https://github.com/encapsulate-xyz/celestia-bridge-ansible.git
cd celestia-bridge-ansible
```

### 3. Inventory

Define your target servers' IP address or DNS in the inventory folder, and select either `mainnet.yml` or `testnet.yml` to update.

Example for testnet.yml

```yaml
---
all:
  vars:
    env: testnet
  children:
    celestia:
      hosts:
        bridge.celestia.testnet.encapsulate.xyz:
          type: bridge
```

## Variables

This playbook allows customization through several variables. You can define these variables in the following locations:

- **`group_vars/all.yml`**: Contains all the source urls and configurations.
- **`group_vars/mainnet.yml`** or **`group_vars/testnet.yml`**: Contains version-specific variables.
- **`group_vars/vault.yml`**: Store secret variables, such as api keys, passwords in this file.

### Usage

1. First, install the dependencies:

  ```bash
   ansible-galaxy install -r requirements.yml
  ```

2. Create a `ansible_vault_password` file containing ansible-vault password

3. Then run the playbook:

  ```bash
  ansible-playbook setup_bridge.yml -l bridge.celestia.testnet.encapsulate.xyz
  ```
4. After completing the playbook, to recover your keys:

```
cel-key add wallet --recover --keyring-backend test --node.type $TYPE --p2p.network $CHAIN_ID
cel-key list --node.type $TYPE --keyring-backend test --p2p.network $CHAIN_ID
```
After you run the playbook, it will ask for confirmation, displaying all the variables and the IP address or DNS of the server you are going to deploy.

Example output:

```bash
TASK [Display environment being deployed to] ***************************************************************************************************
ok: [bridge.celestia.testnet.encapsulate.xyz] => {
    "msg": [
        "Deploying to Host: bridge.celestia.testnet.encapsulate.xyz",
        "Groups: ['celestia']",
        "Project: celestia",
        "Environment: testnet",
        "Type: bridge",
        "Version: v0.18.3-mocha",
        "Username: celestia-bridge",
        "Service Name: celestia-bridge",
        "Operating System: linux",
        "System Architecture: amd64"
    ]
}

TASK [Set all port variables dynamically for bridge node] ***************************************************************************************************************
ok: [142.93.221.79]

TASK [Display all bridge port variables] ********************************************************************************************************************************
ok: [142.93.221.79] => {
    "msg": [
        "P2P Port: 10421",
        "RPC Port: 10458",
        "Gateway: 10459"
    ]
}

TASK [Confirm deployment details] ********************************************************************************************************************
Pausing for 40 seconds
(ctrl+C then 'C' = continue early, ctrl+C then 'A' = abort)
ok: [bridge.celestia.testnet.encapsulate.xyz]

TASK [Please confirm again] ********************************************************************************************************************
ok: [bridge.celestia.testnet.encapsulate.xyz] => {
    "msg": [
        "Deploying to Host: bridge.celestia.testnet.encapsulate.xyz",
        "Project: celestia",
        "Environment: testnet",
        "Type: bridge"
    ]
}

TASK [Confirm deployment details] **************************************************************************************************************
Pausing for 20 seconds
(ctrl+C then 'C' = continue early, ctrl+C then 'A' = abort)
ok: [bridge.celestia.testnet.encapsulate.xyz]
```
