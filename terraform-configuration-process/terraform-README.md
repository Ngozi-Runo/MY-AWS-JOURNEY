#HOW TO CONFIGURE TERRAFORM FOR AWS USER
- Go to your asw management console 
- Click on ---account ID--- on the top right corner
- Click on ---security--- credentials 
- Scroll down and click on create access key
- Check the box and click on create access key
- Go back to your terminal (VS code or powershell) and run this command, and run this command: aws configure
- Go back to your management console and copy the access key you created and paste it on the terminal 
- Similarly, go and copy the secret access key and paste it on the terminal
- On the Default region name; write ***us-east-1***
On the Default region Output format; write ***Json***
- To verify your terraform, do this command ***aws s3 ls***