# Borg backup with pull mode - ansible role
This role installs Borg backup with pull mode on your borgbackup server and clients. 

Ansible 2.4 or higher is required to run this role.

## Prerequisites

Setup ssh public key authentication from backup server to all clients for the root user

Add to /etc/ssh/sshd_config on the backup server
```
AllowUsers borgbackup@127.0.0.1

Match User borgbackup
    PasswordAuthentication no
```	

## Example playbook

```
---
- name: Setup Borg backup - pull mode
  hosts: all
  become: True

  vars:

    borgbackup_cleanup: false
    borgbackup_mode: serial

    borgbackup_serial_cron_day: "*"
    borgbackup_serial_cron_hour: "23"
    borgbackup_serial_cron_minute: "00"

    borgbackup_server:
      - fqdn: localhost
        port: 2222
        type: normal
        home: /mnt/borgbackup/
        pool: repos
        options: ""

    borgbackup_retention:
      hourly: 12
      daily: 7
      weekly: 4
      monthly: 6
      yearly: 1

    borgbackup_include:
      - "/"

    borgbackup_exclude:
      - "/dev/"
      - "/proc/"
      - "/sys/"
      - "/var/run/"
      - "/run/"
      - "/mnt/"
      - "/media/"
      - "/tmp/"
      - "/lost+found"
      - "/var/lib/lxc*"
      - "/var/lib/mysql*"
      - "/root/.cache"
      - "/var/cache/"
      - "/var/lib/docker/devicemapper/"
      - "sh:/home/*/.cache"

  roles:
    - role: borgbackup-pull
      tags: ['backup']
```

*WARNING: the trailing / in item.home is required.*

## Usage

Configure Borg on the server and on clients:
```
ansible-playbook setup-borg-pull.yml
```

## Further reading
* [Borg documentation](https://borgbackup.readthedocs.io/en/stable/)
* [Append only mode information](http://borgbackup.readthedocs.io/en/stable/usage/notes.html#append-only-mode)
