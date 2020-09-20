Testing on EL8 illustrates that there's no easily accesible boto2. Boto2 is required for the AWS modules. So we need to resort to pip. :(

The following steps illustrate one method to get Boto2 and its dependencies prepped in a relatively clean, isolated fashion.

The initial plan was to try and use the EPEL python3-tox and keep things packaged as much as possible. The EPEL python3-tox appears to be quite old and doesn't have --devenv to create portable venvs. As an alternative, we can create a manual venv, pip-install tox into this, then bootstrap the rest of the venvs.

1. Create a clean rhel8 ec2 instance.
2. Install a (non-platform) Python 3 and pre-requisite dev tools, Python 3 devel & virtualenv, Git and GCC.
3. Create a venv.
4. Pip install tox.
5. Bootstrap the tox environment.
6. Register the machine with Red Hat Subscription Manager
7. Install Ansible Engine

  


1. Create a clean rhel8 ec2 instance.
2. Install a (non-platform) Python 3 and pre-requisite dev tools, Python 3 devel & virtualenv, Git and GCC.

`# yum -y install python3 python3-virtualenv python3-devel git gcc`

3. Create and activate a venv.

`$ python3 -m venv .tox-venv`
`$ . .tox-venv/bin/activate`
`(.tox-venv) $ `

4. Pip install tox.

`(.tox-venv) $ pip install tox`

5. Bootstrap the tox environment.

`$ git clone https://github.com/lshake/bootstrap_ansible`

6. Register the machine with Red Hat Subscription Manager and enable the Ansible Engine repository.

```# subscription-manager register
Registering to: subscription.rhsm.redhat.com:443/subscription
Username: <username>
Password: <password>
# subscription-manager list --available --matches 'Red Hat Ansible Engine' --matches 'Employee SKU'
# subscription-manager attach --pool=<pool-id>```

7. Install Ansible Engine

`# subscription-manager repos --enable ansible-2.9-for-rhel-8-x86_64-rpms`


<< DOSTUFF >>

$ deactivate



TODO:

- Assess venv + tox vs pipx
- Lee's OOTB envs include:

  py27-ansible27
  py27-ansible28
  py27-ansible29
  py3-ansible27
  py3-ansible28
  py3-ansible29

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


== Old Steps ==

# 1. Install/enable EPEL (https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm)
# 2. Install python3-tox (https://dl.fedoraproject.org/pub/epel/8/Everything/x86_64/Packages/p/python3-tox-3.4.0-1.el8.noarch.rpm or later)
# 3. Install Git
# 4. Clone Lee's Ansible bootstrap (https://github.com/lshake/bootstrap_ansible)
# 5. Set up a Python virtual environment (venv)


1. Install/enable EPEL
`# yum -y install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm`

2. Install python3-tox
`#  yum -y install python3-tox`

3. Install Git
`# yum -y install git`

4. Clone Lee's Ansible bootstrap
`$ git clone https://github.com/lshake/bootstrap_ansible

