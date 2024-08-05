https://docs.ansible.com/ansible/latest/getting_started/index.html
https://spacelift.io/blog/ansible-tutorial

# Verify your inventory.
ansible-inventory -i inventory.yml --list

# Tips for building inventories
- Ensure that group names are meaningful and unique. Group names are also case sensitive.
- Avoid spaces, hyphens, and preceding numbers (use floor_19, not 19th_floor) in group names.
- Group hosts in your inventory logically according to their What, Where, and When.

**What**
Group hosts according to the topology, for example: db, web, leaf, spine.

**Where**
Group hosts by geographic location, for example: datacenter, region, floor, building.

**When**
Group hosts by stage, for example: development, test, staging, production.

# Ping the `scw_instances` in your inventory
ansible -i inventory.yml scw_instances -m ping

# Run your playbook
ansible-playbook -i inventory.yml setup_users.yml 