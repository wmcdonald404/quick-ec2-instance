Testing on EL8 illustrates that there's no easily accesible boto2. Boto2 is required for the AWS modules. So we need to resort to pip. :(

In order to try and keep things as nice and clean and packagey as possible:

1. Install/enable EPEL (https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm)
2. Install python3-tox (https://dl.fedoraproject.org/pub/epel/8/Everything/x86_64/Packages/p/python3-tox-3.4.0-1.el8.noarch.rpm or later)
3. Clone Lee's Ansible bootstrap (https://github.com/lshake/bootstrap_ansible)


1. Install/enable EPEL
`# yum -y install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm`

2. Install python3-tox
`#  yum -y install python3-tox`

3. Clone Lee's Ansible bootstrap

`$ git clone https://github.com/lshake/bootstrap_ansible

