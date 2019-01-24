# ansible-aws-demo
A demo project utilizing ansible and aws to create infrastructure and deploy a web app

The ansible controller used is Ubuntu 16 or 18 ( I experimented with both), python installed, pip installed, ansible installed via pip, boto and boto3 installed via pip.
The target machines are created with an out of the box AWS Ubuntu 16 AMI. Because the AWS image doesn't have python installed by default, the playbook will ssh in to perform a standard python install first, before performing any other normal ansible actions, which will require python be installed. A customized AMI could be used with python pre-installed to avoid this. However, I wanted to demonstrate ansible automation from an out of the box image, a blank slate so to speak.

I'm also using ec2.py as my dynamic inventory. Setup according to instructions here: https://aws.amazon.com/blogs/apn/getting-started-with-ansible-and-dynamic-amazon-ec2-inventory-management/
Alternatively, aws credentials can be stored in a .boto file.

To run this playbook, you should update the group_vars and host_vars accordingly to reflect your values for keypair and ansible_ssh_private_key_file, while the aws specific vars can be left as is, or changed if desired.

There are two methods for setting the db_password var. Ansible Vault is used in this example to demonstrate how a sensitive password might be encrypted. The encrpyted password shown would need to be replaced with a new encrpyted password. Alternatively, the plain text password that is commented out can be used. A concise guide for setting encrpyted variables can be found here: https://serversforhackers.com/c/how-ansible-vault-works

Pre_tasks have been used in some cases where tasks need to be run before roles

The outline of the project is as follows:
- Security Groups are created in AWS
- A base DB EC2 instance is created
- A simple user prompt is used to designate the number of web instances to be used and then those base EC2 instances are created
- Required Python packages are installed via ssh which are required for Ansible to run.
- The DB server is provisioned and populated with sample data
- The Web servers are provisioned and connected to the DB server
- An AWS target group is created for an Application Load Balancer
- An AWS Application Load Balancer is created.

Once everything is complete and the Load Balancer is fully provisioned, you should be able to access the app in your browser by going to the ALB's DNS name as indicated in the AWS console. Aside from the Welcome! page there is also "/how are you" and "/read from database", which will read from the database to very connectivity to the database.

I've left some debug tasks for demonstration purposes, for the sake of having a peek into what is going on.

*Important: The security group configuration will only allow ssh access (required for ansible) from the AWS SG named "ssh-only" so the Ansible Controller must have a security group attached named "ssh-only".
