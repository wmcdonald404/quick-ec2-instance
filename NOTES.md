## TL;DR
```
# yum -y install python3-virtualenv git gcc
$ python3 -m venv .tox-venv
$ . .tox-venv/bin/activate
(.tox-venv) $ pip install tox
(.tox-venv) $ git clone https://github.com/lshake/bootstrap_ansible
(.tox-venv) $ ./bootstrap_ansible/build.sh
(.tox-venv) $ deactivate

# subscription-manager register
# subscription-manager list --available --matches 'Red Hat Ansible Engine' --matches 'Employee SKU'
# subscription-manager attach --pool=<pool-id>
# subscription-manager repos --enable ansible-2.9-for-rhel-8-x86_64-rpms
# yum install ansible ansible-test ansible-doc
```

## What the What?
I have some old, crappy Ansible code that I occasionally use to quickly throw up an EC2 instance to test stuff. It's lightly used, rarely maintained and frequently breaks if I run it from anywhere other than where I know it already works. Typically due to Boto or AWS CLI or some other thing that's understably changed between 2018 and 2020 or RHEL 7, Fedora 32 or RHEL 8.

This is an effort to try and decouple some of the Ansible code (which will still be basic AF) from its external dependencies without horribly polluting a nicely packaged Python install. 

## Overview
Testing on EL8 illustrates that there's no easily accesible boto2. Boto2 is required for the AWS modules. So we need to resort to pip. :( Installing unpackaged things on a packaged distribution makes my inner-sysadmin sad. <dawson boohoo face> If it's not in an RPM (or a deb, IPS or SVR4 package or _something_), it's not *proper* as far as I'm concerned.

The following steps illustrate one method to get Boto2 and its dependencies prepped in a relatively clean, isolated fashion. (That may allow me to sleep at night.)

My initial plan was to try and use the EPEL python3-tox and keep things packaged as much as possible. The EPEL python3-tox appears to be _quite old_ and doesn't have --devenv to create neatly portable venvs. Let the yak shaving commence.

As an alternative, we can create a manual venv, pip-install tox into this, then bootstrap the rest of the venv(s).

## High-level Steps
### RHEL8
1. Create a clean rhel8 ec2 instance.
2. Install a (non-platform) Python 3 and pre-requisite dev tools, Python 3 devel & virtualenv, Git and GCC.
3. Register the machine with Red Hat Subscription Manager
7. Enable the repository and install Ansible Engine
5. Create a venv.
6. Pip install tox.
7. Bootstrap the tox environment.
99. Ansible all the things.

## Detailed-level Steps
1. Create a clean rhel8 ec2 instance.
2. Install a (non-platform) Python 3 and pre-requisite dev tools, Python 3 devel & virtualenv, Git and GCC.
```
# yum -y install python3 python3-virtualenv python3-devel git gcc
```
6. Register the machine with Red Hat Subscription Manager and enable the Ansible Engine repository.
```
# subscription-manager register
Registering to: subscription.rhsm.redhat.com:443/subscription
Username: <username>
Password: <password>
# subscription-manager list --available --matches 'Red Hat Ansible Engine' --matches 'Employee SKU'
# subscription-manager attach --pool=<pool-id>
```
7. Enable the repository and install Ansible Engine
```
# subscription-manager repos --enable ansible-2.9-for-rhel-8-x86_64-rpms
# yum install ansible ansible-test ansible-doc
```
3. Create and `activate` a venv.
```
$ python3 -m venv .tox-venv
$ . .tox-venv/bin/activate
(.tox-venv) $ 
```
4. Pip install tox. Note this is installing into the `active` venv.
```
(.tox-venv) $ pip install tox
```
5. Bootstrap the tox environment.
```
$ git clone https://github.com/lshake/bootstrap_ansible
```
8. Build the Tox testing venvs
```
$ git clone https://github.com/lshake/bootstrap_ansible
$ ./bootstrap_ansible/build.sh
```


10. Export a portable venv



99. Exit the venv.
$ deactivate


### RHEL7 
1. Create a clean rhel7 ec2 instance.
2. Install a (non-platform) Python 3 and pre-requisite dev tools, Python 3 devel & virtualenv, Git and GCC.
3. Register the machine with Red Hat Subscription Manager
7. Enable the repository and install Ansible Engine
5. Create a venv.
6. Pip install tox.
7. Bootstrap the tox environment.
99. Ansible all the things.

## Detailed-level Steps

# yum -y install python-virtualenv git gcc
$ python -m virtualenv .tox-venv
$ . .tox-venv/bin/activate
(.tox-venv) $ pip install tox
$ git cl




1. Create a clean rhel8 ec2 instance.
2. Install a (non-platform) Python 3 and pre-requisite dev tools, Python 3 devel & virtualenv, Git and GCC.
```
# yum -y install python3 python3-virtualenv python3-devel git gcc
```
6. Register the machine with Red Hat Subscription Manager and enable the Ansible Engine repository.
```
# subscription-manager register
Registering to: subscription.rhsm.redhat.com:443/subscription
Username: <username>
Password: <password>
# subscription-manager list --available --matches 'Red Hat Ansible Engine' --matches 'Employee SKU'
# subscription-manager attach --pool=<pool-id>
```
7. Enable the repository and install Ansible Engine
```
# subscription-manager repos --enable ansible-2.9-for-rhel-8-x86_64-rpms
# yum install ansible ansible-test ansible-doc
```
3. Create and `activate` a venv.
```
$ python3 -m venv .tox-venv
$ . .tox-venv/bin/activate
(.tox-venv) $ 
```
4. Pip install tox. Note this is installing into the `active` venv.
```
(.tox-venv) $ pip install tox
```
5. Bootstrap the tox environment.
```
$ git clone https://github.com/lshake/bootstrap_ansible
```
8. Build the Tox testing venvs
```
```

9. Create the Python venv(s)

10. Export a portable venv



99. Exit the venv.
$ deactivate


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
