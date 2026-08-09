# EXPERIENCE CONTINUOUS INTEGRATION WITH JENKINS | ANSIBLE | ARTIFACTORY | SONARQUBE | PHP

In this project, you will understand and get hands on experience around the entire concept around CI/CD from applications perspective. To fully gain real expertise around this idea, it is best to see it in action across different programming languages and from the platform perspective too. From the application perspective, we will be focusing on PHP here; there are more projects ahead that are based on Java, Node.js, .Net and Python. By the time you start working on Terraform, Docker and Kubernetes projects, you will get to see the platform perspective of CI/CD in action.

## What is Continuous Integration?

In software engineering, Continuous Integration (CI) is a practice of merging all developers’ working copies to a shared mainline (e.g., Git Repository or some other version control system) several times per day. Frequent merges reduce chances of any conflicts in code and allow to run tests more often to avoid massive rework if something goes wrong. This principle can be formulated as _Commit early, push often_.

The general idea behind multiple commits is to avoid what is generally considered as Merge Hell or Integration hell. When a new developer joins a new project, he or she must create a copy of the main codebase by starting a new feature branch from the mainline to develop his own features (in some organization or team, this could be called a __develop__, __main__ or __master__ branch). If there are tens of developers working on the same project, they will all have their own branches created from _mainline_ at different points in time. Once they make a copy of the repository it starts drifting away from the __mainline__ with every new merge of other developers’ codes. If this lingers on for a very long time without reconciling the code, then this will cause a lot of code conflict or Merge Hell, as rightly said. Imagine such a hell from tens of developers or worse, hundreds. So, the best thing to do, is to continuously commit & push your code to the mainline. As many times as tens times per day. With this practice, you can avoid Merge Hell or Integration hell.

<img width="900" alt="image-57" src="https://github.com/user-attachments/assets/1bf424f2-9b6a-48bd-8226-4236e391eb12" />

Before we move on to observability metrics - let us list down the principles that define a reliable and robust CI/CD pipeline:

- Maintain a code repository
- Automate build process
- Make builds self-tested
- Everyone commits to the baseline every day
- Every commit to baseline should be built
- Every bug-fix commit should come with a test case
- Keep the build fast
- Test in a clone of production environment
- Make it easy to get the latest deliverables
- Everyone can see the results of the latest build
- Automate deployment (if you are confident enough in your CI/CD pipeline and willing to go for a fully automated Continuous Deployment)

## 13 DevOps Success Metrics

1. __Deployment frequency:__ Tracking how often you do deployments is a good DevOps metric. Ultimately, the goal is to do more smaller deployments as often as possible. Reducing the size of deployments makes it easier to test and release. I would suggest counting both production and non-production deployments separately. How often you deploy to QA or pre-production environments is also important. You need to deploy early and often in QA to ensure enough time for testing.

2. __Lead time:__ If the goal is to ship code quickly, this is a key DevOps metric. I would define lead time as the amount of time that occurs between starting on a work item until it is deployed. This helps you know that if you started on a new work item today, how long would it take on average until it gets to production.

3. __Customer tickets:__ The best and worst indicator of application problems is customer support tickets and feedback. The last thing you want is your users reporting bugs or having problems with your software. Because of this, customer tickets also serve as a good indicator of application quality and performance problems.

4. __Percentage of passed automated tests__: To increase velocity, it is highly recommended that the development team makes extensive usage of unit and functional testing. Since DevOps relies heavily on automation, tracking how well automated tests work is a good DevOps metrics. It is good to know how often code changes break tests.

5. __Defect escape rate:__ Do you know how many software defects are being found in production versus QA? If you want to ship code fast, you need to have confidence that you can find software defects before they get to production. Defect escape rate is a great DevOps metric to track how often those defects make it to production.

6. __Availability:__ The last thing we ever want is for our application to be down. Depending on the type of application and how we deploy it, we may have a little downtime as part of scheduled maintenance. It is highly recommended to track this metric and all unplanned outages. Most software companies build status pages to track this. Such as this Google Products Status Page

7. __Service level agreements:__ Most companies have some service level agreement (SLA) that they promise to the customers. It is also important to track compliance with SLAs. Even if there are no formally stated SLAs, there probably are application non-functional requirements or expectations to be met.

8. __Failed deployments:__ We all hope this never happens, but how often do our deployments cause an outage or major issues for the users? Reversing a failed deployment is something we never want to do, but it is something you should always plan for. If you have issues with failed deployments, be sure to track this metric over time. This could also be seen as tracking *Mean Time To Failure (MTTF).

9. __Error rates:__ Tracking error rates within the application is super important. Not only they serve as an indicator of quality problems, but also ongoing performance and uptime related issues. In software development, errors are also known as exceptions, and proper exception handling is critical. If they are not handled nicely, we can figure it out while monitoring the rate of errors.

- __Bugs__ – Identify new exceptions being thrown in the code after a deployment
- __Production issues__ – Capture issues with database connections, query timeouts, and other related issuesPresenting error rate metrics like this simply gives greater insights into where to focus attention.

10. __Application usage & traffic:__ After a deployment, we want to see if the number of transactions or users accessing our system looks normal. If we suddenly have no traffic or a giant spike in traffic, something could be wrong. An attacker may be routing traffic elsewhere, or initiating a DDOS attack

11. __Application performance:__ Before we even perform a deployment, we should configure monitoring tools like Retrace, DataDog, New Relic, or AppDynamics to look for performance problems, hidden errors, and other issues. During and after the deployment, we should also look for any changes in overall application performance and establish some benchmarks to know when things deviate from the norm.

It might be common after a deployment to see major changes in the usage of specific SQL queries, web service or HTTP calls, and other application dependencies. These monitoring tools can provide valuable visualizations like this one below that helps make it easy to spot problems.

12. __Mean time to detection (MTTD):__ When problems happen, it is important that we identify them quickly. The last thing we want is to have a major partial or complete system outage and not know about it. Having robust application monitoring and good observability tools in place will help us detect issues quickly. Once they are detected, we also must fix them quickly!

13. __Mean time to recovery (MTTR):__ This metric helps us track how long it takes to recover from failures. A key metric for the business is keeping failures to a minimum and being able to recover from them quickly. It is typically measured in hours and may refer to business hours, not calendar hours.

These are the major metrics that any DevOps team should track and monitor to understand how well CI/CD process is established and how it helps to deliver quality application to the users.

## Simulating a typical CI/CD Pipeline for a PHP Based application

As part of the ongoing infrastructure development with Ansible started from Project 11, you will be tasked to create a pipeline that simulates continuous integration and delivery. Target end to end CI/CD pipeline is represented by the diagram below. It is important to know that both `Tooling and TODO` Web Applications are based on an interpreted (scripting) language (PHP). It means, it can be deployed directly onto a server and will work without compiling the code to a machine language.

The problem with that approach is, it would be difficult to package and version the software for different releases. And so, in this project, we will be using a different approach for releases, rather than downloading directly from git, we will be using Ansible `uri module`.

<img width="900" alt="image-58" src="https://github.com/user-attachments/assets/8f7a5938-0ccf-4989-95b1-c7af67e4a266" />

## Set Up

To get started, we will focus on these environments initially.

- Ci
- Dev
- Pentest

What we want to achieve, is having Nginx to serve as a reverse proxy for our sites and tools. Each environment setup is represented in the below table and diagrams.

<img width="900" alt="image-60" src="https://github.com/user-attachments/assets/2e6de0db-a732-4dc9-aae3-e81ec31043b9" />

CI Envirnoment

<img width="900" alt="image-59" src="https://github.com/user-attachments/assets/30de1dcd-c239-4541-aec0-dcbe1ea64a10" />

Other Environments From Lower To Higher

<img width="900" alt="image-61" src="https://github.com/user-attachments/assets/8e2d01ee-4b20-4768-8a2a-4b19eaf2298c" />

## Project Description:

In this project, we will be setting up a CI/CD Pipeline for a PHP based application. The overall CI/CD process looks like the architecture above.

This project is architected in two major repositories with each repository containing its own CI/CD pipeline written in a Jenkinsfile

- __ansible-config-mgt REPO__: This repository contains JenkinsFile which is responsible for setting up and configuring infrastructure required to carry out processes required for our application to run. It does this through the use of ansible roles. This repo is infrastructure specific

- __PHP-todo REPO__: This repository contains jenkinsfile which is focused on processes which are application build specific such as building, linting, static code analysis, push to artifact repository etc.

## Pre-requisites

Will be making use of AWS virtual machines for this and will require 6 servers for the project which includes:

__Nginx Server:__ This would act as the reverse proxy server to our site and tool.

__Jenkins server:__ To be used to implement your CI/CD workflows or pipelines. Select a t3.medium at least, Ubuntu 24.04 and Security group should be open to port 8080

__SonarQube server:__ To be used for Code quality analysis. Select a t3.medium at least, Ubuntu 24.04 and Security group should be open to port 9000

__Artifactory server:__ To be used as the binary repository where the outcome of your build process is stored. Select a t3.medium at least and Security group should be open to port 8081

__Database server:__ To server as the databse server for the Todo application

__Todo webserver:__ To host the Todo web application.


## Ansible Inventory should look like this

```
├── ci
├── dev
├── pentest
├── pre-prod
├── prod
├── sit
└── uat
```

ci inventory file

```
[jenkins]
<Jenkins-Private-IP-Address>

[nginx]
<Nginx-Private-IP-Address>

[sonarqube]
<SonarQube-Private-IP-Address>

[artifact_repository]
<Artifact_repository-Private-IP-Address>
```

dev Inventory file

```
[tooling]
<Tooling-Web-Server-Private-IP-Address>

[todo]
<Todo-Web-Server-Private-IP-Address>

[nginx]
<Nginx-Private-IP-Address>

[db:vars]
ansible_user=ec2-user
ansible_python_interpreter=/usr/bin/python

[db]
<DB-Server-Private-IP-Address>
```

pentest inventory file

```
[pentest:children]
pentest-todo
pentest-tooling

[pentest-todo]
<Pentest-for-Todo-Private-IP-Address>

[pentest-tooling]
<Pentest-for-Tooling-Private-IP-Address>
```

Observations:

1. You will notice that in the pentest inventory file, we have introduced a new concept `pentest:children` This is because, we want to have a group called pentest which covers Ansible execution against both `pentest-todo` and `pentest-tooling` simultaneously. But at the same time, we want the flexibility to run specific Ansible tasks against an individual group.
   
2. The `db` group has a slightly different configuration. It uses a RedHat/Centos Linux distro. Others are based on Ubuntu (in this case user is `ubuntu`). Therefore, the user required for connectivity and path to python interpreter are different. If all your environment is based on Ubuntu, you may not need this kind of set up. Totally up to you how you want to do this. Whatever works for you is absolutely fine in this scenario.

This makes us to introduce another Ansible concept called `group_vars`. With group vars, we can declare and set variables for each group of servers created in the inventory file.

For example, If there are variables we need to be common between both `pentest-todo` and `pentest-tooling`, rather than setting these variables in many places, we can simply use the `group_vars` for pentest. Since in the inventory file it has been created as `pentest:children` Ansible recognizes this and simply applies that variable to both children.

# 1. Install Jenkins

Let's lunch a AWS ec2 with an Ubuntu OS instance and configure the jenkins server on it.

<img width="900" alt="jenkins instance" src="https://github.com/user-attachments/assets/1cdf6345-b27b-41b6-b5c9-47a53d8b3933" />

#### Install jenkins and it's dependencies using the terminal.

```
sudo apt-get update  # Update the instance
sudo apt upgrade -y

# Jenkins requires Java. For current Jenkins releases, install Java 17:
sudo apt update
sudo apt install fontconfig openjdk-21-jre -y

#Add the Jenkins repository key
sudo wget -O /etc/apt/keyrings/jenkins-keyring.asc https://pkg.jenkins.io/debian-stable/jenkins.io-2026.key

# Add the Jenkins repository
echo "deb [signed-by=/etc/apt/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian-stable binary/" | \
sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null

# Install Jenkins
sudo apt update
sudo apt install jenkins -y

# Start Jenkins
sudo systemctl enable jenkins
sudo systemctl start jenkins

# Check Jenkins status
sudo systemctl status jenkins

# Get the initial Jenkins password
sudo cat /var/lib/jenkins/secrets/initialAdminPassword

# Copy the password.

```
<img width="900" alt="jenkins install" src="https://github.com/user-attachments/assets/82de7c66-e0bb-445d-8546-de7f4f15e846" />

#### Open TCP port 8080

<img width="900" alt="8080" src="https://github.com/user-attachments/assets/d6ef75d7-ebf5-4e09-b3ee-c28a6f758592" />

## 2. Installing Blue-Ocean Plugin

Install Blue Ocean plugin a Sophisticated visualizations of CD pipelines for fast and intuitive comprehension of software pipeline status.

Follow the navigation below :

- Go to manage jenkins > manage plugins > available
- Search for BLUE OCEAN PLUGIN and install

<img width="900" alt="blue ocean" src="https://github.com/user-attachments/assets/727f79a2-8d76-4973-8218-aabf105026f9" />

## Configure blue ocean pipeline with git repo

Follow the steps below:

- Click "Open blue oceans" plugin and create a new pipeline

<img width="900" alt="jenkins pipeline" src="https://github.com/user-attachments/assets/f0e86150-23d9-4883-ba0e-4994f4c7a931" />

- Select github
- Connect github with jenkins using your github personal access token

<img width="900" alt="connect git" src="https://github.com/user-attachments/assets/e3d920b2-d115-4331-bd9d-cf2703bdf0bf" />

- Select the repository
- Create the pipeline
Here is our newly created pipeline. It takes the name of your GitHub repository.

<img width="900" alt="pipeline created" src="https://github.com/user-attachments/assets/178ac70e-c312-466f-a21a-cfafa82ddb41" />

At this point you may not have a Jenkinsfile in the Ansible repository, so Blue Ocean will attempt to give you some guidance to create one. But we do not need that. We will rather create one ourselves. So, click on Administration to exit the Blue Ocean console.

## Let us create our `Jenkinsfile`

Inside the Ansible project, create a new directory `deploy` and start a new file `Jenkinsfile` inside the directory.

<img width="900" alt="deploy mkdir" src="https://github.com/user-attachments/assets/302e081d-2a49-4b17-9547-0d704872ccb0" />

<img width="900" height="378" alt="view" src="https://github.com/user-attachments/assets/5b108202-c4a0-4efe-86e8-6bb2a76405b2" />

Add the code snippet below to start building the `Jenkinsfile` gradually. This pipeline currently has just one stage called Build and the only thing we are doing is using the `shell script` module to echo `Building Stage`

```
pipeline {
    agent any


  stages {
    stage('Build') {
      steps {
        script {
          sh 'echo "Building Stage"'
        }
      }
    }
    }
}
```
Now go back into the Ansible pipeline in Jenkins, and select configure,
Scroll down to Build Configuration section and specify the location of the Jenkinsfile at `deploy/Jenkinsfile`

<img width="900" alt="deploy jenksin" src="https://github.com/user-attachments/assets/9f3956b8-e348-4954-851e-9110aa2a2e99" />

Back to the pipeline again, this time click "Build now"

This will trigger a build and you will be able to see the effect of our basic Jenkinsfile configuration by going through the console output of the build.

To really appreciate and feel the difference of Cloud Blue UI, it is recommended to try triggering the build again from Blue Ocean interface.

1. Click on Open Blue Ocean

2. Select your project

3. Click on the play button against the branch

<img width="900" alt="first build" src="https://github.com/user-attachments/assets/e41cf47d-0389-4485-a560-71755dfdd9fe" />

<img width="900" alt="build 1" src="https://github.com/user-attachments/assets/c46bc642-2a24-4c20-8bfe-b85f06306e9c" />

### Let us see this in action.

1. Create a new git branch and name it `feature/jenkinspipeline-stages`

<img width="900" alt="pipeline stages" src="https://github.com/user-attachments/assets/c5c584db-3d1b-4db8-9074-3461e22f73a0" />

2. Currently we only have the `Build` stage. Let us add another stage called `Test`. Paste the code snippet below and push the new changes to GitHub.

```groovy
   pipeline {
    agent any

  stages {
    stage('Build') {
      steps {
        script {
          sh 'echo "Building Stage"'
        }
      }
    }

    stage('Test') {
      steps {
        script {
          sh 'echo "Testing Stage"'
        }
      }
    }
    }
}
```
Push the new changes to GitHub

<img width="900" alt="commit stages" src="https://github.com/user-attachments/assets/7675c4a4-1e47-4a49-95c1-b1571454a32b" />

3. To make your new branch show up in Jenkins, we need to tell Jenkins to scan the repository
i. Click on the "Administration" button
ii. Navigate to the Ansible project and click on "Scan repository now"
iii. Refresh the page and both branches will start building automatically. You can go into Blue Ocean and see both branches there too.

<img width="900" alt="main jenkins pipeline" src="https://github.com/user-attachments/assets/2fbc77d3-8676-4ce1-8a85-42f4deca1b25" />

iv. In Blue Ocean, you can now see how the `Jenkinsfile` has caused a new step in the pipeline launch build for the new branch.

<img width="900" alt="pipeline blue" src="https://github.com/user-attachments/assets/d701d51f-186c-4195-a538-74e296a9ab89" />

## A QUICK TASK

1. Create a pull request to merge the latest code into the `main branch`

Merge the PR

<img width="900" alt="merged" src="https://github.com/user-attachments/assets/7f23a7aa-3d50-44f2-b595-97ea56f0134b" />

2. After merging the `PR`, go back into your terminal and switch into the `main` branch.
3. Pull the latest change.

<img width="900" alt="gp main" src="https://github.com/user-attachments/assets/3cd7dfb5-4e96-4133-a833-06e7ccecb551" />
