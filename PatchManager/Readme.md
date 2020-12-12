## Tracking Node Inventory and OS Patching Using SSM Patch Manager

### Apply tags to SSM managed instance for using with Patch Manager

Head over to the EC2 service console on the AWS GUI and look for Running Instnaces. <br/> AmazonLinux-Instance & Windows-Instance.<br /> Repeat the following steps to apply Patch Group tags to both these EC2 instances.

Select either of the two instances and click on the Tags tab in the details which show up at the bottom.
Click on the Add/Edit Tags to add a new tag to the instance.
In the new pop-up window which opens up, click on Create Tag which should add a new row. Enter Patch Group in the Key value and Linux Prod or Windows Prod in the Value field depending on the system that you are tagging.
Repeat these steps for the other instance.

Head over to the Patch Manager page on Systems Manager console. <br /> Click on View predefined patch baselines. <br /> 
1. Click on the search bar which should give you a drop down. Select Operating System and choose Amazon Linux 2 or Windows depending on which EC2 instance you're creating the baseline for. If the search result offers a list of options for patch baselines, select the one which is the Default baseline patch baseline. There should be a green tick under the Default baseline column. 
2. Create two patch baselines: one for the Amazon Linux 2 EC2 instance and the other for the Windows EC2 instance. 
3. After creating the Patch baseline, head back to the Patch Manager page, click on the search bar, click on Name prefix, and type in the name you gave to the Patch baseline. Alternatively, you can filter by Owner -> Self. 
1. Select your patch baseline and click on the Action drop-down button. Then click Modify Patch Groups. On the new page, in the Patch Group input area, enter either Linux Prod or Windows Prod depending on the instance. Repeat this step for both Patch Baselines that you have created. 
2. Head back to either one of your created patch baselines, select it and click Configure Patching. Select the radio button for Select a patch group and select the Patch group for this baseline from the drop-down menu. Be sure to select the appropriate patch group here as selecting Linux Patch group for a Windows Patch baselines will simply skip the patching. 
3. For Windows, select the option to Scan for required updates so it only reports back on required patching. 
4. For Linux, select Scan and Install. 
5. After configuring patching, SSM will run one-time Run Commands to carry out actions. 
6. Head over to the Run Command page to track patching run commands.

### Set Up Inventory

1. On the Inventory page of the SSM console, click Set up inventory for both managed instances. Give it about 10 minutes (it takes about this much time on average).
2. Back on the Inventory page, scroll to the bottom of the page, and select the managed instance you want to have a look at to see its inventory.