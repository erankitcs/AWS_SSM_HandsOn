### Installing SSM Agent on On-prem VM

sudo yum -y install https://s3.amazonaws.com/ec2-downloads-windows/SSMAgent/latest/linux_amd64/amazon-ssm-agent.rpm

### Create SSM Hybrid Connection from Main Node(Any Aws Build type server)

aws ssm create-activation --default-instance-name MyOnPremInstance --iam-role SSMServiceRoleForActivation --registration-limit 1

- You will get activation Id from above command.Copy It.

{
    "ActivationCode": "XXXXX",
    "ActivationId": "XXXXXX"
}

### Register On-Premises VM with SSM and Restart SSM Agent

On the CLI of the on-premises VM, register with SSM (using the ActivationCode and ActivationId from the previous step/task):

1. `sudo amazon-ssm-agent -register -code "<ActivationCode>" -id "<ActivationId>" -region us-east-1`

2. `sudo restart amazon-ssm-agent`

### Find the On-Premises VM's Instance ID and Log in to the Instance

From SSM Main Node run below command
- Find the on-premises registered VM's instance ID using the SSM API:
`aws ssm describe-instance-information`
In the output, you should only see one instance with the name MyOnPremInstance. Copy its InstanceId for the next command.
Example:
{
    "InstanceInformationList": [
        {
            "IsLatestVersion": true,
            "IamRole": "SSMServiceRoleForActivation",
            "ComputerName": "ip-172-16-1-169.ec2.internal",
            "PingStatus": "Online",
            "Name": "MyOnPremInstance",
            "InstanceId": "mi-0a5c308a1190af895",
            "IPAddress": "172.16.1.169",
            "ResourceType": "ManagedInstance",
            "ActivationId": "a0275820-e8a5-4b50-9ccb-5a37db570313",
            "AgentVersion": "3.0.431.0",
            "PlatformVersion": "2017.09",
            "RegistrationDate": 1607769708.301,
            "PlatformName": "Amazon Linux AMI",
            "PlatformType": "Linux",
            "LastPingDateTime": 1607769736.183
        }
    ]
}

- Log in to the shell of the on-premises managed SSM instance using the Session Manager session API:
`aws ssm start-session --target <ON_PREMISES_INSTANCE_ID>`

### Connect to an SSM Managed Instance via the SSM Console

1. Log in to the AWS Management Console with the credentials provided and check if you have ec2 instance with SSM Agent installed.
2. Create an IAM Role for EC2 Instance with SSM Policy
- Navigate to IAM.
- Go into Roles.
- Create a role and choose the AWS service EC2.
- Head to Permissions, type "SSM" in the policy search bar, and select the policy `AmazonEC2RoleforSSM`.
- Click Next, leave the tags as their default, and give your IAM role a name (e.g., "MyGUISSMRoleForEC2").
- In the EC2 console, select the Ec2 instance, go into its instance settings, and select Attach/Replace IAM Role.
- In the dropdown, select the MyGUISSMRoleForEC2 you just created and click Apply.
- Reboot the EC2 instance.

### Start Session Manager Session with Newly Created Instance

1. Navigate to the Systems Manager console, and select Session Manager in the left-hand menu.
2. Click Start session. You might already see the on-premises instance we set up earlier in the lab. Wait a couple minutes for your newly configured EC2 instance to show up.
3. Once it does, select it, and click Start session. It should log you in to a browser-based shell session with your instance.


### Few Notes

- Enabling the advanced-instances tier
Advanced instances also enable you to connect to your hybrid machines by using AWS Systems Manager Session Manager. Session Manager provides interactive shell access to your instances.

- Install the Session Manager plugin for the AWS CLI
If you want to use the AWS Command Line Interface (AWS CLI) to start and end sessions that connect you to your managed instances, you must first install the Session Manager plugin on your local machine.