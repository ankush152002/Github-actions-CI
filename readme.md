Arcitech DevOps Internship Assignment 


This document provides an overview of the setup and deployment process, CI/CD pipeline configuration, IAM roles and policies configuration, cron job setup, and any issues faced and resolved during the completion of the DevOps internship assignment.

Setup and Deployment Process

1] Set Up an EC2 Instance:

- Launched a t2.micro EC2 instance using the Ubuntu AMI.
- SSHed into the instance and installed necessary software: Python, pip, Git, Nginx

2] Deploy a Simple Web Application:

- Created a Python Flask application that returns "Hello, World!".
- Configured the Flask application to run with Gunicorn as the WSGI server.
- Configured Nginx as a reverse proxy to forward requests to the Gunicorn server.

Major commands Used 

gunicorn --bind 0.0.0.0:5000 wsgi:app
sudo ln -s /etc/nginx/sites-available/app /etc/nginx/sites-enabled
sudo ufw allow 'Nginx Full'

3] Configure S3 for Static File Hosting:


- Created an S3 bucket named devops-intern-assignment-ankush.
- Uploaded a text file with a brief introduction about myself to the S3 bucket.
- Set appropriate permissions to ensure the file is publicly accessible.

  
4] CI/CD Pipeline Configuration:

- Created a Git repository on GitHub .
- Added the web application code to the repository by cloning , adding and pushing to the main repository.
- Configured a CI/CD pipeline using GitHub Actions Workflow in the ci-cd -pipeline.yaml
- The pipeline clones the repository, installs dependencies, runs tests (if any), deploys the updated application to the EC2 instance, and updates the S3 bucket 
  with any static files from the application.


5] Manage Access and Permissions:

- Set up IAM roles and policies to ensure the EC2 instance can interact with the S3 bucket.
- Configured proper security groups for the EC2 instance to allow web traffic on port 80.


6]Cron Job Setup
Wrote a cron job script on the EC2 instance to check the application health by making an HTTP request to the web app and logging the status every 5 minutes.


Wrote a lambda fucntion to start and stop instance using boto3 module and triggered it by cloudwatch


7]Issues Faced and Resolved

Permission Denied Error When SSHing into EC2 Instance:
-Resolved by ensuring correct IAM roles and policies were assigned to the EC2 instance for SSH access.


Failed to Start Gunicorn Service:
-Resolved by installing Gunicorn and starting the service using systemctl.


AWS Credentials Issue in CI/CD Pipeline:
-Resolved by securely storing AWS credentials in GitHub Secrets and accessing them in the pipeline.

