# AWS Cloudformation

## About this
This is an AWS Cloud formation stack. It will create a cloud infrastructure of the following components:
* EC2 admin instance
* EC2 instances via auto scaling group
* Launch template for the EC2 instances
* RDS - MariaDB for the wordpress data to make it persistent
* Listener, Load balancer and target group - to allow the auto scaling group to get DNS and to scale when load requires it.
* Security groups with various security rules.

## Requirements
* AWS account
* AWS CLI
* A terminal for AWS CLI - I've used Git Bash.

## How to provision the stack
aws cloudformation create-stack \
    --stack-name EnterStackName \
    --template-body file://aws_wp_stack.yaml \
    --parameters \
        ParameterKey=KeyName,ParameterValue=EnterKeyName \
        ParameterKey=VpcId,ParameterValue=EnterVPC-123456767123 \
        ParameterKey=SubnetID1,ParameterValue=EnterSubnet1-111111111111 \
        ParameterKey=SubnetID2,ParameterValue=EnterSubnet2-222222222222 \
        ParameterKey=SubnetID3,ParameterValue=EnterSubnet3-333333333333 \
        ParameterKey=RDSMasterUsername,ParameterValue=EnterAdminUserName \
        ParameterKey=RDSMasterPassword,ParameterValue=EnterAdminPassword \
        ParameterKey=WPAdminUser,ParameterValue=EnterWPAdminUsername \
        ParameterKey=WPAdminPassword,ParameterValue=EnterWPAdminPassword \
        ParameterKey=WPAdminEmail,ParameterValue=EnterEmailOfChoice \
        ParameterKey=WPTitle,ParameterValue=EnterWPTitleName

## Replacing values:
stack-name: Free choice
template-body: Name of the .yaml
KeyName: Name of your SSH key
VpcId: The ID of your VPC 
Subnet1,2,3: ID of your given VPC subnets
RDSMasterUsername: Free choice
RDSMasterPassword: Free choice
WPAdminUser: Free choice
WPAdminPassword: Free choice
WPAdminEmail: Free choice
WPTitle: Free choice

Free choice as in you can decide freely within limitations.

# How to find out the values

## SSH KEY
If you dont have a key already, preferably in same folder, use the following command:
aws ec2 create-key-pair --key-name YourNewKeyPairName --query 'KeyMaterial' --output text > YourNewKeyPair.pem

## VPC ID
AWS will come with a default VPC. To find the value, use the following command:
aws ec2 describe-vpcs --query 'Vpcs[*].VpcId'

## Subnet 1, 2 and 3
Assuming you only have the default VPC, use the following command:
aws ec2 describe-subnets --query 'Subnets[*].SubnetId'

If you have numerous VPCs, use this one instead:
aws ec2 describe-subnets --filters "Name=vpc-id,Values=YourVpcId" --query 'Subnets[*].SubnetId'


# Troubleshooting - Test that it works accordingly
Go to the DNS of the EC2 admin instance, or the loadbalancers DNS:

URL
DNS - you should reach a page stating "IT WORKS!"
DNS/Wordpress and you should reach sample page.
DNS/Wordpress/wp-admin and you should reach the admin dashboard login page.

Make sure that you go to http:// and not https://

# Troubleshooting
If something doesnt work as planned, you can SSH into the EC2 admin instance and check the wp-config.php file using nano.
/var/www/html/wordpress/wp-config.php

Can also check the /etc/httpd/conf/httpd.conf file if it has allowed "override all" under <Directory "/var/www/html"> section.

## Further troubleshooting
See official AWS tutorials:

* Preparing LAMP
https://docs.aws.amazon.com/linux/al2023/ug/ec2-lamp-amazon-linux-2023.html

* Wordpress
https://docs.aws.amazon.com/linux/al2/ug/hosting-wordpress.html
