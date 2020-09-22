## TL;DR
```
# yum -y install python3-virtualenv git gcc
$ python3 -m venv .tox-venv
$ . .tox-venv/bin/activate
(.tox-venv) $ pip install tox
(.tox-venv) $ git clone git@github.com:wmcdonald404/bootstrap_ansible.git
(.tox-venv) $ ./bootstrap_ansible/build.sh
(.tox-venv) $ deactivate

# subscription-manager register
# subscription-manager list --available --matches 'Red Hat Ansible Engine' --matches 'Employee SKU'
# subscription-manager attach --pool=<pool-id>
# subscription-manager repos --enable ansible-2.9-for-rhel-8-x86_64-rpms
# yum install ansible

$ git clone git@github.com:wmcdonald404/quick-ec2-instance.git

$ echo <<EOF > ~/.vault-pass
<vault-password>
EOF 
# nb: NEVER COMMIT TO SCM!!!
$ mkdir -p ~/quick-ec2-instance/inventories/group_vars/all/vault
$ ansible-vault <<EOF > ~/quick-ec2-instance/inventories/group_vars/all/vault/all.yml
vaulted_aws_access_key: <access_key>
vaulted_aws_secret_key: <secret_key>
vaulted_ec2_keypair: <key_pair_name>
EOF
$ ansible-vault encrypt ~/quick-ec2-instance/inventories/group_vars/all/vault/all.yml
# nb: PREVIOUS STEP CONSIDERED QUICK AND DIRTY. vault create <filename> && vault edit <filename> is more correct.

$ . ./local/python/tox/py3-ansible29/bin/activate
$ cd quick-ec2-instance/
$ ansible-playbook ./playbooks/create-x86-ec2-instances.yml -e '{ "ec2_instance_tag": { "name": "rhel8" } }'
```

## What the What?
I have some old, crappy Ansible code that I occasionally use to quickly throw up an EC2 instance to test stuff. It's lightly used, rarely maintained and frequently breaks if I run it from anywhere other than where I know it already works. Typically due to Boto or AWS CLI or some other thing that's understably changed between 2018 and 2020 or RHEL 7, Fedora 32 or RHEL 8.

This is an effort to try and decouple some of the Ansible code (which will still be basic AF) from its external dependencies without horribly polluting a nicely packaged Python install. 

## Overview
Testing on EL8 illustrates that there's no easily accesible boto2. Boto2 is required for the AWS modules. So we need to resort to pip. :( Installing unpackaged things on a packaged distribution makes my inner-sysadmin sad. <dawson boohoo face> If it's not in an RPM (or a deb, IPS or SVR4 package or _something_), it's not *proper* as far as I'm concerned.

The following steps illustrate one method to get Boto2 and its dependencies prepped in a relatively clean, isolated fashion. (That may allow me to sleep at night.)

My initial plan was to try and use the EPEL python3-tox and keep things packaged as much as possible. The EPEL python3-tox appears to be _quite old_ and doesn't have --devenv to create neatly portable venvs. Let the yak shaving commence.

As an alternative, we can create a manual venv, pip-install tox into this, then bootstrap the rest of the venv(s).

## Notes / Follow-up
RHEL8: python 3.6.8 & ansible 2.9

TODO:

- Assess venv + tox vs pipx
- Lee's OOTB envs include:

  py27-ansible27
  py27-ansible28
  py27-ansible29
  py3-ansible27
  py3-ansible28
  py3-ansible29 # rhel8 base

  ... is there value in having env lists / requirements which match:
  rhel7 base ansible + python2
  rhel7 base ansible + python3
  rhel8 base ansible + python2
  rhel8 base ansible + python3

  ... this is probably a sub/superset of the versions Lee maps but would explicit: "This is RHEL7, this is RHEL8, this is F31, 32, 33" be useful to build more targetted venvs?

Possibly refactor as env blocks:

[testenv:rhel6]

[testenv:rhel7]

[testenv:rhel8]
deps =
  py3
  ansible29

## Notes / Foll

Playbooks failing with `"target uses selinux but python bindings (libselinux-python) aren't installed!"` which appears to be down to: https://github.com/ansible-community/molecule/issues/1724

This can possibly be mitigated via tox --sitepackages (update Lee's build.sh and re-run.)

```
You are using pip version 9.0.3, however version 20.2.3 is available.
You should consider upgrading via the 'pip install --upgrade pip' command.
```

