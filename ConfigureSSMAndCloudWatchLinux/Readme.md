## Installation of SSM Agent in Linux

### 1. Install the SSM Agent Using YUM
- Login to Linux box
- wget https://s3.amazonaws.com/ec2-downloads-windows/SSMAgent/latest/linux_amd64/amazon-ssm-agent.rpm
- sudo yum -y localinstall amazon-ssm-agent.rpm

### 2. Create a Role for EC2 instance using AWS Managed Policy

- Log in to the AWS console using the credentials provided on the lab page.
- Head to IAM > Roles.
- Click Create role.
- Select EC2 as the service that will use this role.
- Click Next: Permissions.
- In the search bar, type "AmazonEC2RoleforSSM", select this role, and click Next: Tags.
- Leave tags as default, and click Next: Review.
- Assign your role a name such as "MyEC2SSMRole".
- Click Create role.

### 3. Attach IAM Role to EC2 Instance

- Select this instance.
- Click the Actions dropdown.
- Scroll to Instance Settings.
- Click Attach/Replace IAM Role.
- From the dropdown, select your IAM role and then click Apply.

### 4. Log Back in to Command Line of EC2 Instance

We will enable and start the SSM Agent so it can survive reboots and communicate with Systems Manager.

- Enable SSM Agent:
`sudo systemctl enable amazon-ssm-agent`
- Start SSM Agent:
`sudo systemctl start amazon-ssm-agent`
- To confirm the SSM Agent has started and is running successfully, check its status:
`sudo systemctl status amazon-ssm-agent`
The output should show an active/running status.

### 5. Check Logs for SSM Agent and Enable Debug Logging for It

Look at the SSM Agent log file, which houses all communication logs and actions that SSM Agent is performing on the instance:
`/var/log/amazon/ssm/amazon-ssm-agent.log`

The verbosity of SSM Agent logging can be increased by copying over and modifying a configuration file (`/etc/amazon/ssm/seelog.xml.template`) that comes by default with the SSM Agent install. To do so, carry out the following steps:

- Copy the example template to its original file name so it can be detected by SSM Agent:

`sudo cp /etc/amazon/ssm/seelog.xml.template /etc/amazon/ssm/seelog.xml`

- Edit the file you just copied:
`sudo vim /etc/amazon/ssm/seelog.xml`

- Look for this line in the file:

`<seelog type="adaptive" mininterval="2000000" maxinterval="100000000" critmsgcount="500" minlevel="info">`
Change minlevel="info" to minlevel="debug". Save and quit the file.

- Restart SSM Agent:
`sudo systemctl restart amazon-ssm-agent`

- Tail the SSM Agent's log file to observe the newly enabled verbosity (debug):

`sudo tail -f /var/log/amazon/ssm/amazon-ssm-agent.log`

### Working with CloudWatch Agent

- Login into EC2 Instance 
`sudo yum install amazon-cloudwatch-agent`
or 
`https://s3.amazonaws.com/amazoncloudwatch-agent/amazon_linux/amd64/latest/amazon-cloudwatch-agent.rpm`

`sudo rpm -U ./amazon-cloudwatch-agent.rpm`

- Create AWS IAM Role with permission `CloudWatchAgentServerRole`

- Now, Create agent configuration file. You can use sample `/opt/aws/amazon-cloudwatch-agent/doc/amazon-cloudwatch-agent-schema.json` or create a new one using `cloudwatchconfigfile.json`

`sudo cp /opt/aws/amazon-cloudwatch-agent/doc/amazon-cloudwatch-agent-schema.json /opt/aws/amazon-cloudwatch-agent/etc/amazon-cloudwatch-agent.json`

or 

`sudo vi /opt/aws/amazon-cloudwatch-agent/etc/amazon-cloudwatch-agent.json` and copy content from `cloudwatchconfigfile.json`

sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl -a fetch-config -m ec2 -s -c file:/opt/aws/amazon-cloudwatch-agent/etc/amazon-cloudwatch-agent.json

- Create test.log file inside with some content

- Assign `logs:FilterLogEvents` permission to EC2 Role.

- CLI Test 
`aws --region us-east-1 logs filter-log-events --log-group-name test.log  --filter-pattern Ankit`
{
    "searchedLogStreams": [
        {
            "searchedCompletely": true,
            "logStreamName": "test.log"
        }
    ],
    "events": [
        {
            "ingestionTime": 1607754705618,
            "timestamp": 1607754690311,
            "message": "Ankit test log",
            "eventId": "35854127690368122223113264827615589656871657370579042304",
            "logStreamName": "test.log"
        }
    ]
}