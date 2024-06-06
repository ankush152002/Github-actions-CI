Arcitech DevOps Internship Assignment 
----------------------------------------------------------------------------------------------------------------------------------------------

1] Set Up an EC2 Instance:
=

- Launched a t2.micro EC2 instance using the Ubuntu AMI.
- SSHed into the instance and installed necessary software: Python, pip, Git, Nginx

2] Deploy a Simple Web Application
=

- Created a Python Flask application app.py that returns "Hello World!"
- Created a Virtual Environment and Installed Dependencies:
- Created a file named wsgi.py:
- Created a systemd service file for Gunicorn (/etc/systemd/system/gunicorn.service):
- Created an Nginx configuration file (/etc/nginx/sites-available/myFlaskApp):
- Configured the Flask application to run with Gunicorn as the WSGI server.
- Configured Nginx as a reverse proxy to forward requests to the Gunicorn server.

      server {
        listen 80; server_name 17.25.35.128;

      location / {
        proxy_pass http://unix:/home/ubuntu/myFlaskApp/app.sock;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}


Major commands Used 

gunicorn --bind 0.0.0.0:5000 wsgi:app

sudo ln -s /etc/nginx/sites-available/app /etc/nginx/sites-enabled

sudo ufw allow 'Nginx Full'

3] Configure S3 for Static File Hosting:
=

- Created an S3 bucket named devops-intern-assignment-ankush.
- Uploaded a text file  named  intro.txt with a brief introduction about myself to the S3 bucket.
- Set appropriate permissions to ensure the file is publicly accessible.

  
4] CI/CD Pipeline Configuration:
=

- Created a Git repository on GitHub .
- Added the web application code to the repository by cloning , adding and pushing to the main repository.
- Using Git Bash Perfomed git commands
- Configured a CI/CD pipeline using GitHub Actions Workflow in the ci-cd -pipeline.yaml

   
   AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
        AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
        AWS_REGION: us-east-1
      run: |
        ssh -o StrictHostKeyChecking=no -i ~/.ssh/id_rsa ubuntu@ec2-18-234-196-207.compute-1.amazonaws.com << 'EOF'
          cd /home/ubuntu/myFlaskApp
          git pull origin main
          source env/bin/activate
          sudo systemctl restart gunicorn
        EOF


  
- The pipeline clones the repository, deploys the updated application to the EC2 instance, and updates the S3 bucket 
  with any static files from the application.


5] Manage Access and Permissions:
=

- Set up IAM roles and policies to ensure the EC2 instance can interact with the S3 bucket.

  {
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "s3:*",
      "Resource": "arn:aws:s3:::devops-intern-assignment-ankush/*"
    }
  ]
}


-  Attached the IAM role to the EC2 instance using the EC2 management console.


- Configured proper security groups for the EC2 instance to allow web traffic on port 80.


6] Cron Job Setup
-

- Wrote a cron job script on the EC2 instance to check the application health by making an HTTP request to the web app and logging the status every 5 minutes.


#!/bin/bash

RESPONSE=$(curl --write-out '%{http_code}' --silent --output /dev/null http://localhost)

if [ "$RESPONSE" -ne 200 ]; then
  echo "Application is down. HTTP Status: $RESPONSE"
else
  echo "Application is running. HTTP Status: $RESPONSE"
fi




- Wrote a lambda fucntion to start and stop instance using boto3 module and triggered it by cloudwatch

- Created IAM Role:
  Created an IAM role with the following policies:
  AWSLambdaBasicExecutionRole
  AmazonEC2FullAccess

-Start EC2 Instance Lambda Function:
 
    CloudWatch Events
    Start EC2 Instance Rule:

     Name: StartEC2InstanceRule
     Schedule Expression: cron(0 8 * * ? *) (Runs at 8:00 AM UTC every day)
     Target: StartEC2Instance Lambda function

 -Stop EC2 Instance Lambda Function:

   Stop EC2 Instance Rule:

      Name: StopEC2InstanceRule
      Schedule Expression: cron(0 20 * * ? *) (Runs at 8:00 PM UTC every day)
     Target: StopEC2Instance Lambda function


7]Issues Faced and Resolved
=

- Permission Denied Error When SSHing into EC2 Instance:
-Resolved by ensuring correct IAM roles and policies were assigned to the EC2 instance for SSH access.


- Failed to Start Gunicorn Service:
-Resolved by installing Gunicorn and starting the service using systemctl.


- AWS Credentials Issue in CI/CD Pipeline:
-Resolved by securely storing AWS credentials in GitHub Secrets and accessing them in the pipeline.–
