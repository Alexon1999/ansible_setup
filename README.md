# Ansible
We can use Ansible for configuration management of slave servers. meaning install/deploy applications and configure provisioned infrastructure.

## Documentations
- [Official Documentation](https://docs.ansible.com/ansible/latest/getting_started/index.html)
- [SpaceLift Ansible Tutorial](https://spacelift.io/blog/ansible-tutorial)

## Workflow

- Make at least one machine a control node (control machine, master machine) which will connect to your slave servers (the servers you wish to manage).
- Inventory file is created from terraform results.
- set scaleway inventory vars ansible user : ansible
- create a new playbook to create ansible user with sudo permissions by overriding Var ansible user to root then disable ssh login root and password authentication in general.
- then other playbooks to manage servers, create users, install packages, etc.

### Verify your inventory.
```
$ ansible-inventory -i inventory.yml --list
```

### Tips for building inventories
- Ensure that group names are meaningful and unique. Group names are also case sensitive.
- Avoid spaces, hyphens, and preceding numbers (use floor_19, not 19th_floor) in group names.
- Group hosts in your inventory logically according to their What, Where, and When.

**What**
Group hosts according to the topology, for example: db, web, leaf, spine.

**Where**
Group hosts by geographic location, for example: datacenter, region, floor, building.

**When**
Group hosts by stage, for example: development, test, staging, production.

### Ansible Vault
Ansible Vault allows you to encrypt sensitive data, such as passwords, within your playbooks and variable files. Here's how you can use Ansible Vault to pass a password:

create/edit vault file
```bash
$ ansible-vault create vault.yml
$ ansible-vault edit vault.yml

# Add the following content
vault_control_node_user_password: "your_control_node_user_password"
```

### Initialize ansible environment
- Initialize ansible environment
```bash
$ ansible-playbook -i inventory.yml initialize.yml
```

### Ping the `scw_instances` in your inventory
```bash
$ ansible -i inventory.yml scw_instances -m ping
```

### Run your playbook
- Run a playbook
```bash
$ ansible-playbook -i inventory.yml setup_users.yml
```

### Run your playbook with vault
```bash
# it will ask for the vault password
$ ansible-playbook -i inventory.yml setup_users.yml --ask-vault-pass
```

### Master playbook
You can individually run playbooks or create a master playbook that includes all the playbooks you want to run at once.

- Create a master playbook that includes all the playbooks you want to run at once.
```bash
$ ansible-playbook -i inventory.yml initialize.yml
$ ansible-playbook -i inventory.yml master_playbook.yml --ask-vault-pass
```

# [Troubleshoting]
- Test the connection to the host
  ```bash
  $ ssh -i /home/alexon/.ssh/ansible-demo-user ansible@51.158.102.133 -v
  ```

- Remove the host from the known_hosts file : because the host key has changed
  **Host Key**
  A host key is a cryptographic key used by an SSH server to identify itself to clients. When you connect to an SSH server, the server presents its host key to the client, which the client can use to verify the server's identity. This helps ensure that you are connecting to the intended server and not to a malicious server pretending to be the intended one (a man-in-the-middle attack).

  Host keys are specific to a server and typically remain consistent unless the server is reinstalled, or the SSH server software is reconfigured. They are part of the SSH protocol's security measures.

  **known_hosts** File
  The known_hosts file is a file on the client's machine that stores the host keys of SSH servers the client has previously connected to. It acts as a record of trusted servers.

  ```bash
  $ ssh-keygen -f "/home/alexon/.ssh/known_hosts" -R "51.158.102.133"
  ```
