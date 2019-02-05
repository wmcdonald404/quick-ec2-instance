# quick-satellite

This project is intended to act as a simple example of spinning up a Red Hat Satellite 6 instance on AWS.

This playbooks in this repository will:

- Configure some AWS credentials in the Ansible Vault
- Create a default VPC if one does not already exist (examples for specific VPC creation are included too)
- Create an appropriately sized instance (the file system layout is kept deliberately simple, for product deployments in anger refer to the documentation)
- Prepare an instance for a Satellite install

Once the VPC and instance are provisioned https://github.com/sean797/iac-satellite can be used to deploy the Satellite. The playbooks in that repository will:

- Subscribe the host appropriately
- Install Satellite
- Optionally: create some standard Satellite objects (products, content views, activation keys etc.)

## Contents

```
├── ansible.cfg
├── inventories
│   ├── aws
│   ├── ec2.ini
│   └── group_vars
│       └── all
│           ├── vars
│           │   └── all.yml
│           └── vault
│               └── all.yml
├── playbooks
│   ├── create-default-infrastructure.yml
│   ├── create-ec2-instances.yml
│   ├── create-specific-infrastructure.yml
│   ├── debug-infrastructure.yml
│   ├── destroy-ec2-instances.yml
│   └── templates
│       ├── config.j2
│       └── credentials.j2
└── README.md
```

- `inventories/aws`: AWS dynamic inventory script
- `inventories/group_vars/all/vars/all.yml`: inventory group vars for AWS and RHSM
- `inventories/group_vars/all/vault/all.yml`: sensitive inventory group vars for AWS and RHSM
- `playbooks/create-default-infrastructure.yml`: idempotently create a default AWS VPC 
- `playbooks/create-specific-infrastructure.yml`: idempotently create specific VPN with explicit subnets, security groups, internet gateways and routing tables
- `playbooks/create-ec2-instances.yml`: create the Satellite EC2 instance
- `playbooks/destroy-ec2-instances.yml`: destroy the Satellite EC2 instance
- `playbooks/debug-infrastructure.yml`: debug output from VPC facts

## Variables

- `ec2_image`: EC2 AMI image ID to provision (default ami-7c491f05, Red Hat Enterprise Linux (RHEL) 7.1 (HVM)_
- `ec2_region`: AWS region to create VPC/provision instances (default: eu-west-1)
- `vaulted_aws_access_key`: AWS Access key ID (https://docs.aws.amazon.com/general/latest/gr/aws-sec-cred-types.html#access-keys-and-secret-access-keys)
- `vaulted_aws_secret_key`: AWS Secret Access Key (as above)
- `vaulted_ec2_keypair`: AWS EC2 keypair (https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html)

## Steps
1. Clone the repository
```
$ git clone git@github.com:wmcdonald404/satellite-simple-install.git ~/quick-satellite/
```

2. Define your AWS access_key, secret_key and keypair variables in a vault file

Note: vault/all.yml will contain the vaulted/encrypted values.  vars/all.yml is effectively a layer of redirection so that there is a plain-text copy of the variable name to aid troubleshooting/tracing.

```
$ mkdir -p ~/quick-satellite/inventories/group_vars/all/{vars,vault}

$ ansible-vault create ~/quick-satellite/inventories/group_vars/all/vault/all.yml
$ ansible-vault edit ~/quick-satellite/inventories/group_vars/all/vault/all.yml

vaulted_aws_access_key: <access_key>
vaulted_aws_secret_key: <secret_key>
vaulted_ec2_key_pair: <key_pair>
```
3. Optionally, create a vault_pass file (nb: never check this into SCM)
```
vi ~/.vault_pass
```
4. Create AWS default VPC infrastructure
```
$ cd ~/quick-satellite
$ ansible-playbook ~/quick-satellite/playbooks/create-default-infrastructure.yml
```
5. Refresh the inventory cache (just in case this is an iteration run and a previous instance has been cached)
```
$ ~/quick-satellite/inventories/aws --refresh-cache
```
6. Create EC2 Satellite instance
```
$ cd ~/quick-satellite
$ ansible-playbook ~/quick-satellite/playbooks/create-ec2-instances.yml
```
7. Note the item.dns_name value

8. Progress to https://github.com/sean797/iac-satellite
