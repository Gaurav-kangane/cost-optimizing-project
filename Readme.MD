In this Project we are going to exporting logs from EC2 to Cloudwatch then From Cloudwatch to S3 using lambda function.

High-level steps 
1.	Assign an IAM Role to the EC2 Instance
2.	Install the CloudWatch Agent on the EC2 Instance
3.	Configure the CloudWatch Agent
4.	Verify Logs in CloudWatch

1. Create IAM role for EC2 and attach to EC2 = CloudWatchAgentServerPolicy

2. Connect to the EC2 Instance and run the command below to install couldwatch Agent
   sudo yum install amazon-cloudwatch-agent -y

3. Create a Configuration File  using below command 
   sudo vi /opt/aws/amazon-cloudwatch-agent/bin/config.json

4. Add the following configuration to capture all logs

{
  "logs": {
    "logs_collected": {
      "files": {
        "collect_list": [
          {
            "file_path": "/var/log/*",
            "log_group_name": "ec2-all-logs",
            "log_stream_name": "{instance_id}-all-logs"
          }
        ]
      }
    }
  }
}

5. Start the agent using the configuration file:
   sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-ctl -a fetch-config -m ec2 -c file:/opt/aws/amazon-cloudwatch-agent/bin/config.json -s

6. Verify Logs in CloudWatch
   Open the CloudWatch Console in the AWS Management Console
   Navigate to Logs → Log Groups

7. Select a log group and view log streams for your EC2 instance

8. Install httpd and check the details in CloudWatch
   sudo yum install httpd -y
   Verify the httpd log in cloudwatch

9. Create Lambda function for exporting logs from cloudwatch to s3. Before this process add permission in s3 bucket

10. Open bucket and Go to the bucket's Permissions tab   -->   Go to Bucket Policy. Click Edit
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowCloudWatchLogs",
            "Effect": "Allow",
            "Principal": {
                "Service": "logs.amazonaws.com"
            },
            "Action": [
                "s3:PutObject",
                "s3:GetBucketAcl",
                "s3:GetObject"
            ],
            "Resource": [
                "arn:aws:s3:::your-bucket-name/*",
                "arn:aws:s3:::your-bucket-name"
            ]
        }
    ]
}

Click on Save Changes

11. create lambda function and change configuration to 10 min and run the lambda

12. Go to s3 and verify logs added or not.

13. We can also use Eventbridge sheduler for our project

14. Open Eventbridge --> create schedule --> give name --> select one time schedule --> choose date & time, hr, Take UTC timezone, window = 15 min click next

15. Choose Target API AWS Lambda --> selct your lambda function --> click next --> create schedule.





