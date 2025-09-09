# ANSIBLE:
## [Official Documentation](https://docs.ansible.com)

# PLAYBOOK

## Ansible Inventory 
```yaml
[host-group-name]
server-name-1        ansible_host=192.168.1.2    ansible_port=22
server-name-2        ansible_host=192.168.1.3    ansible_port=22

```

## Add history timestamp
```yaml
---
- name: Configure shell history with timestamps
  hosts: hostname-in-inventory
  become: yes
  tasks:
    - name: Set history timestamp for all users
      lineinfile:
        path: "/etc/bash.bashrc"
        line: "export HISTTIMEFORMAT='%F %T '"
        state: present

    - name: Reload shell for all users
      shell: "source /etc/bash.bashrc"
      args:
        executable: /bin/bash
```        

## Remove user and sudoers file 
```yml
---
- name: Remove user directory and sudoers file
  hosts: hostname-in-inventory
  gather_facts: false
  become: true

  tasks:
    - name: Remove user directory
      file:
        path: "/home/name=of-the-user"
        state: absent
      ignore_errors: yes

    - name: Remove sudoers file
      file:
        path: "/etc/sudoers.d/name=of-the-user"
        state: absent
      ignore_errors: yes
```

## Add user into the server ubuntu
```yaml
#admin= ALL= NOPASSWD:ALL
#sudoers ALL= NOPASSWD:ALL,!/bin/su,!/bin/bash,!/usr/sbin/visudo,!/bin/chattr,!/usr/bin/firewall-cmd,!/usr/share/bash-completion/completions/firewall-cmd
#checkbox= ALL= NOPASSWD:/usr/bin/ls,/usr/bin/yum list,/usr/bin/yum search,/usr/bin/cat,/usr/bin/find,/usr/bin/grep,/usr/bin/diff,/usr/bin/head,/usr/bin/tail,/usr/bin/ping,/usr/bin/curl,/usr/bin/git status,/usr/bin/journalctl
---
- name: Create sudoers user with passwordless sudo
  hosts: hostname-in0inventory

  tasks:
    - name: Add sudoers username
      user:
        name: user-name
        shell: /bin/bash

    - name: Create sudo file
      file:
        path: /etc/sudoers.d/user-name
        state: touch

    - name: Give sudoers user passwordless sudo
      lineinfile:
        path: /etc/sudoers.d/user-name
        state: present
        line: 'user-name ALL= NOPASSWD:ALL,!/bin/su,!/bin/bash,!/usr/sbin/visudo,!/bin/chattr,!/usr/bin/firewall-cmd,!/usr/share/bash-completion/completions/firewall-cmd'
        validate: "/usr/sbin/visudo -cf %s"

    - name: Set authorized key taken from file
      authorized_key:
        user: user-name
        state: present
        key: "{{ lookup('file', '/ssh/key/location/user-name/user-name.pub') }}"
```

## First Provisioning ubuntu server
```yaml
---
- name: Configure shell history with timestamps
  hosts: hostname-in0inventory
  become: true
  tasks:

  ## Update dan Upgrade OS
    - name: Update dan upgrade OS
      shell: "sudo apt update -y && sudo apt upgrade -y"
  ## Instsall net-tools
    - name: install net-tools
      shell: "sudo apt install net-tools -y"
  ## TimeStampt Ubuntu#
    - name: Set history timestamp for all users
      lineinfile:
        path: "/etc/bash.bashrc"
        line: "export HISTTIMEFORMAT='%F %T '"
        state: present
    - name: Reload shell for all users
      shell: "source /etc/bash.bashrc"
      args:
        executable: /bin/bash
  ## Set TimeZone Asia / Jakarta
    - name: Set timezone to Asia/Jakarta
      timezone:
       name: Asia/Jakarta

### Add user name-of-the-user
    - name: Add sudoers admin name-of-the-user
      user:
        name: name-of-the-user
        shell: /bin/bash

    - name: Create sudo file
      file:
        path: /etc/sudoers.d/name-of-the-user
        state: touch

    - name: Give sudoers name-of-the-user passwordless sudo
      lineinfile:
        path: /etc/sudoers.d/name-of-the-user
        state: present
        line: 'name-of-the-user ALL= NOPASSWD:ALL'
        validate: "/usr/sbin/visudo -cf %s"

    - name: Set authorized key taken from file
      authorized_key:
        user: name-of-the-user
        state: present
        key: "{{ lookup('file', '/srv/key/name-of-the-user/name-of-the-user.pub') }}"
```