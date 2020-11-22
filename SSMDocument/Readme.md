### Creating and Using a Custom Document with Parameter Store Variables

1. Create SSM Parameter to Use in SSM Document - mysql-pass
2. Create SSM Command Document
   - In the left-hand menu, under Shared Resources, click Documents.
   - Click Create command or session.
   - Give your document a name.
        Leave the Target type dropdown field blank, which sets its value to /(meaning to target all applicable resources).
        You can also choose to select the particular resource you're going to run this document against. In our case, we'll run it against an EC2 instance; however, we'll leave the target at its default value.
   - Set the Document type to Command document.
3. Enter the Provided SSM Command Document Schema and create document.
4. Execute the SSM Document
    - Select the Owned by me tab, and click the document you created.
    - Click Run command to execute your document.
    - Leave Document version as Default.
    - For Targets, select Choose instances manually.
    - Select the listed AmazonLinux-Instance EC2 SSM managed instance.
    - Uncheck the Enable writing to an S3 bucket option.
    - Leave everything else as default, and click Run.
    - After clicking Run, you'll be taken to the Run Command page to track the progress/status of Run Command executing the document.

Final CLI command of run would be similar to below:
`aws ssm send-command --document-name "AnkitDoc" --document-version "1" --targets '[{"Key":"InstanceIds","Values":["i-06aa6899432f593f9"]}]' --parameters '{"Packages":["mariadb-server"],"Password":["{{ ssm:mysql-pass }}"]}' --timeout-seconds 600 --max-concurrency "50" --max-errors "0" --region us-east-1`
5. Use SSM Session to Connect to the Managed Instance and Verify
    - Navigate to the Session Manager page, click Start Session, select the AmazonLinux-Instance, and start a shell session with it to verify the document was successfully applied against the instance.