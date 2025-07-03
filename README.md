# aap-platform

Repository containing config as code for AAP 2.5. This looks at AAP from a platform perspective. For a team-specific example, have a look at https://github.com/alawong/aap-network, which does not require setting up authentication, settings, or PAH (i.e. any platform specific stuff).

This includes seeding for:

* Organisations (alternatively can be done when setting up Authentication Methods e.g. to Azure AD. This is the recommended approach)
* Teams (alternatively can be done when setting up Authentication Methods e.g. to Azure AD. This is the recommended approach)
* Users (alternatively can be done when setting up Authentication Methods e.g. to Azure AD. This is the recommended approach)
* Credentials
* Credential input sources
* Inventories
* Projects
* Settings
* Job templates
* Workflow templates
* Private Automation Hub Collection Repositories
* Private Automation Hub EE registries

The playbook will also setup a workflow template for the aap_configure.yml playbook. You setup a GitLab/GitHub webhook in your CaC repo to the workflow, so that when you make changes to the CaC and push them to your main branch, the workflow will run to update AAP with any new config that was added.

## Getting Started

### Prerequisites

* Ansible Automation Platform 2.5 installed
* Bastion node with SSH access to Automation Controller hosts that has:
  * `ansible.controller` and `infra.controller_configuration` collections installed. These can be installed from Ansible Automation Hub.

### Initial Controller Configuration and Configuration as Code Setup

1. SSH into your bastion host, from which you'll run the AAP seeding.

2. Clone this repository.

    `git clone https://github.com/alawong/aap-platform.git`

3. Ensure that `ansible-core` is installed, as well as the collections `infra.aap_configuration`, `ansible.controller`, `ansible.platform`, `ansible.eda`, and `ansible.hub`. 

   The collections needed are in `collections/requirements.yml`. You can use the below `ansible.cfg` file, obtaining the token from https://console.redhat.com/ansible/automation-hub/token, and then run `ansible-galaxy collection install -r collections/requirements.yml` from inside this repo. This will install the collections on the bastion for you to use.

```
[galaxy]
server_list = certified,validated

[galaxy_server.certified]
url=https://console.redhat.com/api/automation-hub/content/published/
auth_url=https://sso.redhat.com/auth/realms/redhat-external/protocol/openid-connect/token
token=<yourtoken>

[galaxy_server.validated]
url=https://console.redhat.com/api/automation-hub/content/validated/
auth_url=https://sso.redhat.com/auth/realms/redhat-external/protocol/openid-connect/token
token=<yourtoken>
```

If you have a proxy, you'll need to whitelist the following URLs:

For Ansible certified and validated collections:
  * galaxy.ansible.com
  * cloud.redhat.com
  * console.redhat.com
  * sso.redhat.com
  * automation-hub-prd.s3.us-east-2.amazonaws.com
  * ansible-galaxy.s3.amazonaws.com

For Red Hat Execution Environment images:
  * registry.redhat.io

4. Edit `vars/config-as-code/common.yml` within `aap-platform` to point to your AAP host, and add your username and password.

5. Fill in the blanks within `vars/config-as-code/`. Of note are the settings, projects, and credentials. The others shouldn't _need_ to be changed, and should work. Organisation names may need to change to match your requirements however in job_templates.yml and workflow_templates.yml to what comes from your Authentication.

6. Run `aap_configure.yml`. This will also require the vault password you used to decrypt Ansible Vault encrypted secrets.

    `ansible-playbook --ask-vault-pass -vv aap_configure.yml`

7. Troubleshoot any issues. If you get an error saying something like "Expected 1 result, found 0", then it means that you're referencing an AAP object that doesn't exist e.g. an organisation or a credential.

8. C