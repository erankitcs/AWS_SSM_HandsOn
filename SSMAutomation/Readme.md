## Executing Automation Workflow Using AWS SSM Automation

Systems Manager Automation enables you to run a sequence of workflows (e.g., AWS API calls, provisioning instances, creating buckets, and firing off Lambda functions) through the convenience of a single SSM Automation document.

The beauty of it, though, is you can use plugins within the Automation document to jump between different steps of the workflow depending on conditions you decide. Comparatively, AWS CloudFormation doesn't allow you to do this as readily, and there are some AWS resource actions you can't do within CloudFormation. With the aws:executeAwsApi plugin provided with Automation, you can virtually execute any API call via Automation and optionally also parse outputs.

### Create Automation Document

1. Click Execute automation.
2. Click Create Document, and choose the Editor tab.
3. Click Edit. If a warning pops up, it's safe to ignore, so click OK.
4. Copy the SSM Automation document JSON here.
5. Paste the contents of the JSON document into the form under Editor, and click Create automation. You'll then be taken back to the Documents SSM page.

### Provide Inputs and Execute Automation Document

1. On the upper part of the screen, click the Owned by me tab to list the Automation document we just created.
2. Click Execute automation. You'll be taken to the configuration and input page for the Automation document.
3. Leave EC2IamRole set to MyEC2SSMRole.
4. In the Application dropdown, select the application you want to deploy. (In the lab video, we deploy mariadb-server.)
5. Click Execute to run your automation.

### Track Automation Progress Status

Once you click Execute, you'll be taken to the main Automation page, where it will list all the steps in the automation, as well as actions and status against those steps.

Wait for the Automation status, which should be on top right side of the screen, to display Success.

Note: Automation can fail if any of the mandatory steps fail.

### Use Session Manager to Log in to SSM Automation's Spun-Up EC2 Instance

1. Navigate to Session Manager, click Start Session, and select the EC2 instance tagged SSM-Created-EC2.
2. Click Start session to start a shell session with the instance, and verify the Automation document successfully ran through and deployed as planned.
3. For mariadb-server input, use the following to verify:

`sudo systemctl status mariadb`
This should show an active/running status.

`mysql -uroot -pmysecureencryptedpassword`
This should log you in to the database shell prompt.

4. For httpd input, use the following to verify:

`sudo systemctl status httpd`
This should show an active/running status.

5. curl   `http://localhost`
This should return a blob of HTML output confirming the web server is working.