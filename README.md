___
# TP3 : Ansible

## 3-1 Document your inventory and base commands

- `ansible all -i ansilbe/inventories/setup.yml -m ping`

- `ansible all -i ansible/inventories/setup.yml -m setup -a "filter=ansible_distribution*"`

- `ansible all -i ansible/inventories/setup.yml -m yum -a "name=httpd state=absent" --become`

## 3-2 Document your playbook

## Document your docker_container tasks configuration.