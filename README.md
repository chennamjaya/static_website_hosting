# static_website_hosting
I am creating a basic static website hosting using Amazon S3 and Route 53 services

Step1:
Create an index.html file with a basic website code in the vs code and push it to the git.

Step2:
Setting Up S3 Bucket for Hosting
1. **Log in to the AWS Management Console**: Go to the AWS Management Console and sign in to your AWS account.
2. **Navigate to the S3 Service**: In the AWS Management Console, navigate to the S3 service.
3. **Create a New Bucket**:
   - Click on the "Create bucket" button.
   - Enter a unique name for your bucket.
   - Choose a region for your bucket. Choosing a region closest to your target audience is recommended for better performance.
   - Click on "Create bucket" to create your S3 bucket.
4. **Configure Bucket Settings**:
   - Once the bucket is created, click on its name to open its settings.
   - Configure the bucket for static website hosting:
     - Go to the "Properties" tab of your bucket.
     - Scroll down to "Static website hosting" and click on "Edit".
     - Choose "Use this bucket to host a website" and enter the index document (e.g., index.html).
     - Click "Save changes" to save the configuration.
5. **Set Bucket Policy for Public Access**:
   - In the bucket settings, go to the "Permissions" tab.
   - Scroll down to "Bucket policy" and click on "Edit".
   - Add a bucket policy for public read access. Below is an example bucket policy:
     ```json
     {
       "Version": "2012-10-17",
       "Statement": [
         {
           "Sid": "PublicReadGetObject",
           "Effect": "Allow",
           "Principal": "*",
           "Action": "s3:GetObject",
           "Resource": "arn:aws:s3:::your-bucket-name/*"
         }
       ]
     }
     ```
     Replace "your-bucket-name" with the name of your bucket.
   - Click "Save changes" to apply the bucket policy.

Step3: Configuring Route 53 for DNS

1. **Create a Hosted Zone in Route 53**:
   - Navigate to Route 53 in the AWS Management Console.
   - Click on "Create hosted zone".
   - Enter your domain name (e.g., example.com).
   - Choose "Public Hosted Zone" and click "Create hosted zone".

2. **Update DNS Records in Route 53**:
   - Once the hosted zone is created, Route 53 will provide you with a set of name servers.
   - Go to your domain registrar (e.g., Hostinger) and update the name servers with the ones provided by Route 53.
   - This step delegates DNS resolution for your domain to Route 53.

3. **Add an Alias Record for Your S3 Bucket**:
   - In the Route 53 dashboard, click on your hosted zone.
   - Click on "Create record".
   - Choose "Simple routing" and then "Define simple record".
   - Set "Record Name” to your domain name (e.g., example.com).
Choose "Alias to S3 website endpoint" and select your S3 bucket from the dropdown.
Click "Create records" to save the changes.

Step4: 
Automating Deployment with GitHub Workflows

Create a Workflow File:

Inside your GitHub repository, create a new directory named .github/workflows.
Create a YAML file inside this directory, for example, deploy.yml.
Configure the Workflow:

Define the name of the workflow and specify when it should run. For example:
yaml
Copy code
name: Deploy to S3
on:
  push:
    branches:
      - main
Set Up Jobs:

Define the jobs to be executed as part of the workflow. For example:
yaml
Copy code
jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout code
      uses: actions/checkout@v2

    - name: Configure AWS credentials
      uses: aws-actions/configure-aws-credentials@v1
      with:
        aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
        aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
        aws-region: your-region

    - name: Sync S3 bucket
      run: aws s3 sync . s3://your-bucket-name --delete
Set Up Secrets:

Go to your GitHub repository settings.
Navigate to "Secrets" or "Settings" > "Secrets" > "Add a new secret".
Add the AWS access key ID and secret access key as secrets. These secrets will be used in the workflow for authentication.
Commit and Push Changes:

Commit the workflow file (deploy.yml) to your repository.
Push the changes to GitHub.
Verify Deployment:

GitHub Actions will automatically trigger the workflow on every push to the main branch.
Monitor the workflow execution in the "Actions" tab of your repository.
Once the workflow completes successfully, verify that your website is deployed to the S3 bucket.


