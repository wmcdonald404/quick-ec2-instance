Testing on EL8 illustrates that there's no easily accesible boto2. Boto2 is required for the AWS modules. So we need to resort to pip. :(

Initial plan was to try and use the EPEL python3-tox but this appears to be quite old and doesn't have --devenv to create portable venvs. As an alternative, we might need to create a manual venv, pip-install tox into this, then bootstrap the rest of the venvs.

1. Create a clean rhel8 ec2 instance
2. Install a (non-platform) Python3 and virtualenv
3. Create the venv
4. Pip install tox


# yum -y install python3 python3-virtualenv

$ python3 -m venv .tox-venv
$ . .tox-venv/bin/activate


TODO:

- Assess venv + tox vs pipx




1. Install/enable EPEL (https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm)
2. Install python3-tox (https://dl.fedoraproject.org/pub/epel/8/Everything/x86_64/Packages/p/python3-tox-3.4.0-1.el8.noarch.rpm or later)
3. Install Git
4. Clone Lee's Ansible bootstrap (https://github.com/lshake/bootstrap_ansible)
5. Set up a Python virtual environment (venv)


1. Install/enable EPEL
`# yum -y install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm`

2. Install python3-tox
`#  yum -y install python3-tox`

3. Install Git
`# yum -y install git`

4. Clone Lee's Ansible bootstrap
`$ git clone https://github.com/lshake/bootstrap_ansible

