# Ansible Role - Controller

This role installs and configures Ansible to be used as a controller node.
A specific Python version is installed as well, Ansible is installed with that chosen python version.

```yaml
# since there are several reserved variables, e.g. ansible version,
# we use other conventional naming
ansible_v: 5.0.1
python_v: 3.9
```
The ansible-core version is automatically installed along with the chosen base version.

See the file `defaults/main.yml` for the remaining defaults, they should speak for itself.

## remote_tmp

Ansible works by transferring modules to your remote machines, running them, and then cleaning up after itself. In some cases, you may not wish to use the default location and would like to change the path. You can do so by altering this setting:

```yaml
remote_tmp: /home/ansible/.ansible/tmp
```

## pip packages

Several pip packages are installed by default:

  - pip==21.3.1
  - simplejson
  - ansible-lint[yamllint]
  - netaddr
  - pexpect
  - jmespath
  - docker
  - requests
  - xmltodict
  - yamllint
  - setuptools_rust

Define additional packages with:

```yaml
pip_extra_pkgs:
  - my_package
```

## configuration

A configuration file is placed in `/etc/ansible/ansible.cfg`, with the following variables:

```yaml
config:
  defaults:
    ansibe_managed: 'Ansible managed'
    remote_user: ansible
    interpreter_python: auto
    callback_enabled: profile_tasks
    host_key_checking: 'False'
    retry_files_enabled: 'False'
    force_color: 'True'
    log_path: /var/log/ansible/ansible.log
    forks: 50
  ssh_connection:
    pipelining: 'True'
    retries: 3
    ssh_args: '-C -o ControlMaster=auto -o ControlPersist=1800s'
```

## Example playbook

```yaml
---
- hosts: my_hosts
  gather_facts: false
  become: true
  roles:
    - controller
```

### Mitogen

This is not included.
Mitogen isn't working (yet) on ansible 2.10+.

I've also experienced several weird use cases where Ansible became buggy using Mitogen.