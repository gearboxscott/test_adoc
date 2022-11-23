Role-Hardened-Mounts Name
=========

This role will remount linux partition on the OS so they are hardened via Qualys and CIS benchmark standards.

Requirements
------------

Before running this role, set the `ansible_remote_tmp` to `$HOME/.ansible/tmp` in the inventory per host, by group, or in the `extra_vars` for either the Job Template or Job Template Workflow.

> WARNING: After this role has been setup, `ansible_remote_tmp` will need to setup to allow anything Ansible to run on a host.

Alternatively, from the command-line to run the playbooks:

```bash
ansible-playbook test.yaml --extra-vars "ansible_remote_tmp=$HOME/.ansible/tmp"
```


Role Variables
--------------

From the inventory file or inventory in Ansible Controller:

```yaml
# Partition Tables
partition_tables:
  hardened:
    - name: /tmp
      src: /dev/mapper/rootvg-lv_tmp
      fstype: xfs
      options: 'defaults,nodev,noexec,nosuid'
      boot: true
      passno: 0
      state: present
    - name: /var/tmp
      src: /dev/mapper/rootvg-lv_var_tmp
      fstype: xfs
      options: 'defaults,nodev,noexec,nosuid'
      boot: true
      passno: 0
      state: present
    - name: /var
      src: /dev/mapper/rootvg-lv_var
      fstype: xfs
      options: 'defaults,nodev'
      boot: true
      passno: 0
      state: present
    - name: /home
      src: /dev/mapper/rootvg-lv_home
      fstype: xfs
      options: 'defaults,nodev'
      boot: true
      passno: 0
      state: present
```

Sample Partition Table in Variables:

```yaml
partition_tables:
  rhel7:
    - part pv.253 --fstype="lvmpv" --ondisk=sda --size=67583
    - part /boot  --fstype="xfs"   --ondisk=sda --size=2048
    - volgroup rootvg --pesize=4096 pv.253
    - logvol /var/tmp --fstype="xfs"  --size=4096  --name=var_tmp --vgname=rootvg --name=lv_var_tmp
    - logvol /var     --fstype="xfs"  --size=16384 --name=var     --vgname=rootvg --name=lv_var
    - logvol swap     --fstype="swap" --size=8192  --name=swap    --vgname=rootvg --name=lv_swap
    - logvol /        --fstype="xfs"  --size=22517 --name=root    --vgname=rootvg --name=lv_root
    - logvol /home    --fstype="xfs"  --size=8192  --name=home    --vgname=rootvg --name=lv_home
    - logvol /tmp     --fstype="xfs"  --size=8192  --name=tmp     --vgname=rootvg --name=lv_tmp
```

The item to note in the table above is the `--name` is consistent and `--vgname=rootvg` is the same for all OSes so this role will work correctly.

Example Playbook
----------------

Setup the variable in inventory mentioned above and use the role in a playbook:

```yaml
- name: Project Playbook
  hosts: all
  gather_facts: true
  vars:
    ansible_remote_tmp: $HOME/.ansible/tmp

  roles:
    - Role-Hardened-Mounts

```

License
-------

GPL

Author Information
------------------

Red Hat, Inc.