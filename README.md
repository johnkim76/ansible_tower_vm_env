# Ansible Tower VM Environment

This repo allows for a simple VM environment to be created on a mobile laptop (e.g. MBP)

This [Example Ansible Project](https://github.com/johnkim76/ansible-poc-demo/tree/centos) will be used with is VM environment.

## Prerequisite

* [vagrant](https://code2bits.com/how-to-install-vagrant-on-macos-using-homebrew/) installed
* [vagrant-hosts plugin](https://github.com/oscar-stack/vagrant-hosts) installed (***vagrant plugin install vagrant-hosts***)
* virtual box installed (provider)

## Clone the repo

```bash
$ git clone https://github.com/johnkim76/ansible_tower_vm_env.git
$ cd ansible_tower_vm_env
```

## Bring up the Vagrant Environment

```bash
$ vagrant up
```

One Tower, and 3 Additional ***ansible-hosts*** will be created

```bash
$ vagrant status
Current machine states:

tower                     running (virtualbox)
machine1                  running (virtualbox)
machine2                  running (virtualbox)
machine3                  running (virtualbox)
```

The following maps the VM name to the hostname & IP address

| VM Name | Hostname | IP Address |
| --- | --- | --- |
| tower | ansible-tower.example.com | 192.168.37.20 |
| machine1 | ansible-node1.example.com | 192.168.37.21 |
| machine2 | ansible-node2.example.com | 192.168.37.22 |
| machine3 | ansible-node3.example.com | 192.168.37.23 |

**NOTE:** All VM's internally mounts this repo (parent) folder to ***/vagrant***.  Therefore, you can easily bring files back and forth from any of the VMs by simply copying files to and from ***/vagrant*** and this parent repo folder on the laptop.

## Logon to the Tower VM to obtain the login credentials

You'll need to logon to the Tower VM to view the MOTD message containing the initial login credentials.  If you restore from a backup, the password will be restored to what it was in the backup files.

```bash
$ vagrant ssh tower

Last login: Mon Apr 10 22:11:00 2020 from gateway

  Welcome to Ansible Tower!

  Log into the web interface here:

    https://10.44.0.45/

    Username: admin
    Password: <password>

  The documentation for Ansible Tower is available here:

    http://www.ansible.com/tower/
```

## Restoring a VM

If something happened to a VM, I found it easier to simply destroy that VM and recreate it

```bash
$ vagrant destroy <vm name>
$ vagrant up <vm name>
```

## Backup & Restore Ansible Tower Configuration

Once you've setup your Ansible Tower, you may want to back it up and create a tarball on the ***/vagrant*** folder for you to restore from.  The below are simple notes to backup and restore your tower settings.

### logon to the Tower VM and become the root user

```bash
$ vagrant ssh tower
$ sudo su -
```

### Download the Ansible Tower Tarball File

```bash
$ curl https://releases.ansible.com/ansible-tower/setup/ansible-tower-setup-3.6.3-1.tar.gz > ansible-tower-setup-3.6.3-1.tar.gz
```

### Untar the Tarball File

```bash
$ tar xfvz ansible-tower-setup-3.6.3-1.tar.gz
```

### Edit the Inventory file

Navigate to the Ansible Tower folder, and edit the inventory file.  Fill in blank password values

```bash
$ cd ansible-tower-setup-3.6.3-1
$ vi inventory

...
admin_password='admin'
...
pg_password='awx'
...
rabbitmq_password='tower'
```

### Backup with the following command

```bash
$ ./setup.sh -e 'backup_dest=/path/to/tower/backup/folder/' -b
```

### Restore with the follwoing command

```bash
$ ./setup.sh -e 'restore_backup_file=/path/to/tower/backup/folder/tower-backup-latest.tar.gz' -r
```
