### Creating an IAM Role and Configuring an EC2 Instance for AWS Systems Manager

1. Get the ARN of SSM Policy for IAM Role `AmazonEC2RoleforSSM`
    aws iam list-policies --scope AWS --query "Policies[?PolicyName == 'AmazonEC2RoleforSSM']"
2. Create the role and attach the trust policy JSON file
    aws iam create-role --role-name MyEC2SSMRole --assume-role-policy-document file://EC2Trust.json
3. Assign policy from previous step to the newly created role
    aws iam attach-role-policy --role-name MyEC2SSMRole --policy-arn <SSM-POLICY-ARN>
4. Create instance profile for EC2 instance
    aws iam create-instance-profile --instance-profile-name MyEC2InstanceProfile
5. Add the role created in second task to the instance profile created above:
    aws iam add-role-to-instance-profile --instance-profile-name MyEC2InstanceProfile --role-name MyEC2SSMRole
6. Get subnet ID
    aws ec2 describe-subnets --query "Subnets[?Tags[?Value == 'SubnetA'] ].SubnetId | [0]"
7. Get security group ID
    aws ec2 describe-security-groups --filters Name=group-name,Values=SG --query "SecurityGroups[?GroupName == 'SG'].GroupId | [0]"
8. Get AMI ID (with SSM installed)
    aws ec2 describe-images --filters "Name=architecture,Values=x86_64" "Name=description,Values=*Amazon Linux 2 AMI 2.0.2019*gp2" "Name=owner-id,Values=137112412989" "Name=image-type,Values=machine" --query "sort_by(Images, &CreationDate)[::-1].ImageId | [0]"
       
9. Create an EC2 Instance Using Subnet, Security Group, and AMI ID
    aws ec2 run-instances --associate-public-ip-address --security-group-ids <SECURITY-GROUP-ID> --iam-instance-profile Arn=<INSTANCE-PROFILE-ARN> --instance-type t2.micro --image-id <AMI-ID> --subnet-id <SUBNET-ID> --tag-specifications "ResourceType=instance,Tags=[{Key=Name,Value=MyEC2}]"
10. Verify instance
    aws ec2 describe-instances --filters Name=tag:Name,Values=MyEC2 --query "Reservations[].Instances[].InstanceId"
11. Verify from SSM
    aws ssm describe-instance-information --query "InstanceInformationList[?InstanceId == '<INSTANCE-ID>']"