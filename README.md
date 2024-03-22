-   [What is CI/CD?](#what-is-cicd)
    -   [Continuous Integration (CI)](#continuous-integration-ci)
    -   [Continuous Delivery (CD)](#continuous-delivery-cd)
-   [Why use CI/CD?](#why-use-cicd)
-   [When is CI/CD useful?](#when-is-cicd-useful)
-   [Where is CI/CD used?](#where-is-cicd-used)
-   [How does CI/CD work?](#how-does-cicd-work)
-   [Integrating GitHub Webhooks to a Jenkins Pipeline](#integrating-github-webhooks-to-a-jenkins-pipeline)
    -   [Prerequisites](#prerequisites)
    -   [Create a Jenkins Job](#create-a-jenkins-job)
    -   [Add a GitHub Webhook](#add-a-github-webhook)
-   [Merging with Jenkins](#merging-with-jenkins)
    -   [Create a new branch](#create-a-new-branch)
    -   [Update existing Jenkins job](#update-existing-jenkins-job)
    -   [Create a new Jenkins job for merging](#create-a-new-jenkins-job-for-merging)
    -   [Test the setup](#test-the-setup)
-   [Deploying an app on AWS with Jenkins](#deploying-an-app-on-aws-with-jenkins)
    -   [Create an EC2 instance](#create-an-ec2-instance)
    -   [Create a new Jenkins job](#create-a-new-jenkins-job)
-   [Integrating CD into the rest of the pipeline](#integrating-cd-into-the-rest-of-the-pipeline)
    -   [Test the setup](#test-the-setup-1)
-   [Implementing Automated Deployment with Jenkins](#implementing-automated-deployment-with-jenkins)
    -   [Create a new Jenkins job](#create-a-new-jenkins-job-1)
    -   [Test the setup](#test-the-setup-2)

## What is CI/CD?

CI/CD, or Continuous Integration/Continuous Delivery, is a practice in DevOps that aims to improve software development and deployment processes, mainly by making them much faster.

![](images/image-2.png)

### Continuous Integration (CI)

-   Developers frequently merge their code changes into a central repository.
-   Each merge triggers an automated build and testing process.
-   Provides immediate feedback to the team.
-   Can identify bugs early and save time through automation.

![](images/image.png)

### Continuous Delivery (CD)

-   Getting the updated app to users quickly and smoothly.
-   The app is always ready to be sent to users, but someone has to decide when to do it.
-   The app is sent to users automatically whenever it passes all the tests.

![](images/image-1.png)

## Why use CI/CD?

-   To speed up the release process.
-   To catch and fix errors quickly.
-   To improve the quality of software.
-   To reduce manual work in testing and deploying software.

## When is CI/CD useful?

-   When you have a team of developers working on the same project.
-   When you want to release updates to your app quickly and smoothly.
-   When you want to make sure your app is always working correctly and passing tests.
-   When you want to reduce the time and effort spent on testing and deploying software.

## Where is CI/CD used?

-   In software development teams that want to improve their processes.
-   In companies that want to release updates to their apps quickly and automatically.

## How does CI/CD work?

Using an example of a simple CI/CD pipeline with Jenkins, GitHub, and AWS:

-   Developers write code on their computers and send it to GitHub, which stores the code online.
-   When new code is sent to GitHub, Jenkins automatically takes the code and runs tests to make sure it works.
-   If the tests pass, Jenkins can then send the updated code to AWS, which makes it available to users online.
-   If the tests fail, Jenkins lets the developers know so they can fix the problems.
-   The app is constantly being improved, tested, and made available to users, all with minimal manual work. It helps teams work together better and keeps the quality of the app high.

![](images/image-7.png)

## Integrating GitHub Webhooks to a Jenkins Pipeline

### Prerequisites

-   [x] Jenkins installed and available on the internet.
-   [x] Jenkins GitHub plugin installed.
-   [x] GitHub Repository

### Create a Jenkins Job

-   Click 'New Item' on the Jenkins dashboard.
-   Enter a name for the job and select 'Freestyle project'.
-   Provide the GitHub project URL in the 'General' section.

![](images/image-6.png)

-   In the 'Source Code Management' section, select 'Git' and provide the SSH repository URL.
-   Add the private SSH key to the credentials part.
-   Specify the branch to use.
-   In the 'Build Triggers' section, select 'GitHub hook trigger for GITScm polling'.

![](images/image-3.png)

### Add a GitHub Webhook

-   Go to the GitHub repository settings.
-   Click on 'Webhooks' and then 'Add webhook'.
-   Enter the Jenkins URL with '/github-webhook/' at the end, e.g. 'http://jenkins-url/github-webhook/'.
-   Select 'application/json' as the content type.
-   Make sure the webhook is active.
-   Click 'Add webhook'.

![](images/image-4.png)

Now, whenever a change is pushed to the GitHub repository, Jenkins will automatically trigger the job.

You should see a green checkmark next to the webhook URL in the GitHub settings if it is working correctly.

![](images/image-5.png)

## Merging with Jenkins

### Create a new branch

```bash
git checkout -b dev
```

### Update existing Jenkins job

-   In the 'Source Code Management' section, change the branch to 'dev'.
-   In the post-build actions, add a trigger to build another project and put the name of the job that will merge the code, e.g. merge-job.

### Create a new Jenkins job for merging

-   Click 'New Item' on the Jenkins dashboard.
-   Enter the name of the job you put in the post-build actions of the previous job and select 'Freestyle project'.
-   In the 'Source Code Management' section, select 'Git' and provide the SSH repository URL.
-   Add the private SSH key to the credentials part.
-   Specify the dev branch.
-   In post-build actions, add a 'Git Publisher' and select 'Merge before build' and 'Push only if build succeeds'.
-   Specify the branch to merge into, e.g. 'main' and enter 'origin' in the 'Target remote name' field.
-   Save the job.

### Test the setup

Now, whenever a change is pushed to the dev branch, the first Jenkins job will be triggered, and if it succeeds, it should trigger the second job to merge the code into the main branch.

## Deploying an app on AWS with Jenkins

### Create an EC2 instance

Launch an EC2 instance and configure it ensuring SSH access is allowed as we will be SSHing into the instance from Jenkins.

### Create a new Jenkins job

-   Click 'New Item' on the Jenkins dashboard.
-   Enter a name for the job and select 'Freestyle project'.
-   In the 'Source Code Management' section, select 'Git' and provide the SSH repository URL.
-   Add the private SSH key to the credentials part.
-   Specify the branch to use.
-   In the 'Build Environment' section, select 'SSH Agent' and choose the credentials with the private key that is needed to SSH into the EC2 instance.
-   In the 'Build' section, add an 'Execute shell' build step.
-   Enter the commands to SSH into the EC2 instance.
    -   Use the `-o "StrictHostKeyChecking=no"` option to avoid the prompt for adding the host to known hosts.
    -   Update the instance with the latest packages and install Nginx.
    -   Add a reverse proxy to the Nginx configuration to forward traffic to the app running on port 3000.
    -   Restart and enable Nginx.
    -   Install software needed to run the app.
    -   CD into the app folder and install the dependencies.

```bash
rsync -avz -e "ssh -o StrictHostKeyChecking=no" app ubuntu@<ec2-public-ip>:/home/ubuntu/
rsync -avz -e "ssh -o StrictHostKeyChecking=no" environment ubuntu@<ec2-public-ip>:/home/ubuntu/

ssh -o "StrictHostKeyChecking=no" ubuntu@<ec2-public-ip> <<EOF
    sudo apt-get update -y
    sudo apt-get upgrade -y

    sudo apt-get install nginx -y

    sudo systemctl restart nginx
    sudo systemctl enable nginx

    cd environment/app
    chmod +x provision.sh
    ./provision.sh

    cd ~/app
    npm install
EOF
```

## Integrating CD into the rest of the pipeline

Following on from the previous section, we can trigger the deployment job after the merge job is successful.

This will enable us to automatically deploy the app to AWS whenever we make a change to the code and push it to the dev branch.

-   In the post-build actions of the merge job, add a trigger to build another project and put the name of the job that will deploy the app, e.g. cd-job.

### Test the setup

-   Make a change to the code and push it to the dev branch.
-   Wait for the Jenkins merge job to run and merge the code into the main branch.
-   Wait for the Jenkins deploy job to run and deploy the app to AWS.
-   Go to the EC2 instance public IP address in a browser and check if the app is running with the latest changes.

## Implementing Automated Deployment with Jenkins

Once the three jobs from the previous sections are set up, we can automate the entire process by chaining them together and finishing it off with a final job that will start the app on our EC2 instance.

This will enable us to simply push code changes to the dev branch and have Jenkins take care of the rest, from merging the code to deploying it to AWS and starting the app, so we can see our changes live without any manual work.

### Create a new Jenkins job

-   Click 'New Item' on the Jenkins dashboard.
-   Enter a name for the job and select 'Freestyle project'.
-   In the 'Build Environment' section, select 'SSH Agent' and choose the credentials with the private key that is needed to SSH into the EC2 instance.
-   In the 'Build' section, add an 'Execute shell' build step.
-   Enter the commands to SSH into the EC2 instance and start the app.

```bash
ssh -o "StrictHostKeyChecking=no" ubuntu@54.154.166.106 <<EOF
	# kill process listening on port 3000
	kill -9 $(lsof -i:3000 -t) 2> /dev/null
    cd app
    npm install
    nohup node app.js > /dev/null 2>81 &
EOF
```

### Test the setup

-   Make a change to the code and push it to the dev branch.
-   Wait for the four Jenkins jobs to run and then go to the EC2 instance public IP address in a browser and check if the app is running with the latest changes.
