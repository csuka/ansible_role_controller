# Ansible Role - Controller

This role installs and configures Ansible to be used as a controller node for EL 8 and 9 systems.

An Ansible user is created with sudo privileges on the node. Ansilbe itself is installed using pip.
To do so, a specific Python version is installed as well, Ansible is installed with that chosen python version. To ensure all required packages can be installed, the epel repo is installed as well.

```yaml
# since there are several reserved variables, e.g. ansible version,
# we use other conventional naming
ansible_v: 6.0.0
python_v: 3.9
```
The ansible-core version is automatically installed along with the chosen base version.

See the file `defaults/main.yml` for the remaining defaults, they should speak for itself.

## remote_tmp

Ansible works by transferring modules to your remote machines, running them, and then cleaning up after itself. Ansible stores some files on the controller machine as well machine. The path is configured with:

```yaml
remote_tmp: /home/ansible/.ansible/tmp
```

## pip packages

Several pip packages are installed by default:

  - pip==22.1.2
  - simplejson
  - ansible-lint[yamllint]
  - netaddr
  - pexpect
  - jmespath
  - docker
  - requests
  - xmltodict
  - wheel
  - yamllint
  - setuptools_rust

Define additional pip packages to install with:

```yaml
pip_extra_pkgs:
  - my_package
```

## configuration

A configuration file is placed in `/etc/ansible/ansible.cfg`, with the following default variables:

```yaml
config:
  defaults:
    ansibe_managed: 'Ansible managed'
    remote_user: ansible
    interpreter_python: auto
    callbacks_enabled: ansible.posix.profile_tasks
    host_key_checking: 'False'
    retry_files_enabled: 'False'
    force_color: 'True'
    log_path: /var/log/ansible/ansible.log
    forks: 30
  ssh_connection:
    pipelining: 'True'
    retries: 3
    ssh_args: '-C -o ControlMaster=auto -o ControlPersist=1800s'
```

## Example playbook

Fact gathering is required.

```yaml
---
- hosts: my_hosts
  gather_facts: true
  become: true
  roles:
    - ansible_role_controller
```

### Mitogen

I've decided to not include mitogen, as I've experienced several weird use cases where Ansible became buggy using Mitogen.
