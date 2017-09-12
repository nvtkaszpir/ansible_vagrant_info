# Vagrant info via Ansible

Generates html file with vagrant environment

# Requirements
Your host used to run vagrant commands needs to have ansible installed.

# Installation
We assume that your pproject is in `~/src/proj` directory.
Create `~/src/proj/ansible` directory.
Create `~/src/proj/ansible/roles` directory, put there `vagrant_info` ansible role.
Create ansible playbook in `~/src/proj/ansible/vagrant_info.yml`:

```yaml
---

- hosts: all
  gather_facts: yes

  roles:
    - roles/vagrant_info

```

Modify Vagrantfile, add at the very end of the `Vagrant.configure` config section below code:

```ruby
config.vm.provision "info", type: 'ansible' do |ansible|
ansible.limit = "all"
ansible.verbose = true
ansible.playbook = "ansible/vagrant_info.yml"
end
```

Add `~/src/proj/ansible.cfg` file:

```ini
[defaults]
gathering = smart
log_path = ansible.log
nocows = yes
retry_files_enabled = False
jinja2_extensions = jinja2.ext.do
ansible_managed = "This file is MANAGED BY ANSIBLE, all changes will be LOST upon an update."
host_key_checking = false
inventory = .vagrant/provisioners/ansible/inventory/vagrant_ansible_inventory

[ssh_connection]
ssh_args = -o ControlMaster=auto -o ControlPersist=60s -o ControlPath=/tmp/ansible-ssh-%r-%h-%p
pipelining = True

```

# Runing

When you run ``vagrant provision`` then it will connect to hosts and generate `~/src/proj/ansible/vagrant_info.html` file.

Run `vagrant provision --provision-with=info` to generate only report and not to trigger other provisioners.
