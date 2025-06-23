# aap-platform

Repository containing config as code for AAP 2.4.

This includes seeding for:

* Organisations
* Teams
* Credentials
* Credential input sources coming from Hashicorp Vault
* Inventories
* Projects
* Settings
* Job templates
* Workflow templates

## Getting Started

### Prerequisites

* Ansible Automation Platform 2 installed
* Bastion node with SSH access to Automation Controller hosts that has:
  * `ansible.controller` and `infra.controller_configuration` collections installed.

### Initial Controller Configuration and Configuration as Code Setup

1. SSH into the bastion node.

2. Clone this repository. This contains the configuration playbook `aap_controller_configure.yml` and `aap_pah_configure.yml`, as well as all of the default configuration for Automation Controller.

    `git clone https://github.com/alawong/aap-platform.git`

3. Edit `vars/config-as-code/common.yml within `aap-platform` to point to your controller host and PAH host.

4. Fill in the blanks within `vars/config-as-code/`. This will be relevant to their required setup.

5. Run `aap_controller_configure.yml`. This will also require the vault password you used to decrypt Ansible Vault encrypted secrets.

    `ansible-playbook --vault-password-file ../password.txt aap_controller_configure.yml`

