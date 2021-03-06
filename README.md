aws-vpc-scenario2
=================

Ansible v2 role to create a multi-AZ public/private zone VPC with NAT gateways. Amazon Web Services describes this as a "Scenario 2" VPC.


More info: http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/VPC_Scenario2.html

This role provides:

- a VPC across two AZs
- a public and a private subnet in each AZ
- a RDS db subnet group using the private subnets
- NAT Gateways for allowing Internet access from private subnets
- relatively secure network ACLs for public and private subnets
- two relatively secure VPC security groups for public or private instances

Additionally, it provides
- a web server in each public subnet. IAM roles can be set by adding them to the ansible-playbook invocation
- a bastion server in just one public subnet
- an application load balancer connected to the two servers in the public subnets
- an Aurora database cluster

Requirements

- Ansible v2.7.x
- AWS account and credentials (OS-level environment variables were used for secrets, using AWS comand line, use aws configure)
- AWS CLI and Boto (Boto can be installed with pip, example in Linux: ```sudo pip install botocore && sudo pip install boto3```)

Role Variables
--------------

Adjust vars/main.yml to contain variable values that make sense for your deployment

Plays also require extra vars to be supplied on invocation via ansible-playbook:

- remote_cidr - network CIDR from which you will access the public subnets
- environment - used for resource tags and resource names
- iam_role - an IAM role (as ARN) to use for the public servers
- key_pair - the name of the keypair to use for the EC2 instances
- database_name - name of Aurora database that will be set up
- database_user - root user for the Aurora database
- database_password - root password for the database, must be minimum 8 characters


Example Playbook
----------------

Note: use the appropriate real-world value for your variables here.

Role play:
~~~~
  ---
  - name: Create a Scenario 2 VPC with public and private subnets in two AZs
    hosts: localhost
    connection: local
    gather_facts: yes

    roles:
    - { role: aws-vpc-scenario2, remote_cidr: 92.237.9.56/32, aws_env: dev01, iam_role: , key_pair: keypair_name, database_name: testdatabase, database_user: root_user, database_password: unsafe}
~~~~

Ansible Instructions
--------------------

Copy the above to a file, give it a name such as playbook.yml
Run it with ```ansible-playbook playbook.yml```
