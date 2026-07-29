# ANSIBLE CONFIGURATION MANAGEMENT (AUTOMATE PROJECT 7 TO 10)

In Projects 7 to 10 we perform a lot of manual operations to set up virtual servers, install and configure required software and deploy our web application.

This Project will make us appreciate `DevOps tools` even more by making most of the routine tasks automated with [Ansible Configuration Management](https://www.redhat.com/en/topics/automation/what-is-configuration-management#:~:text=Configuration%20management%20is%20a%20process,in%20a%20desired%2C%20consistent%20state.&text=Managing%20IT%20system%20configurations%20involves,building%20and%20maintaining%20those%20systems.), at the same time we will become confident with writing code using declarative languages such as YAML.

## Ansible Client as a Jump Server (Bastion Host)

A `Jump Server` (sometimes also referred as `Bastion Host`) is an intermediary server through which access to internal network can be provided. If you think about the current architecture you are working on, ideally, the webservers would be inside a secured network which cannot be reached directly from the Internet. That means, even DevOps engineers cannot SSH into the Web servers directly and can only access it through a Jump Server - it provides better security and reduces attack surface.

## Task

- Install and configure Ansible client to act as a Jump Server/Bastion Host
- Create a simple Ansible playbook to automate servers configuration

On the diagram below the Virtual Private Network (VPC) is divided into two subnets - Public subnet has public IP addresses and Private subnet is only reachable by private IP addresses.

<img width="800" alt="git image" src="https://github.com/user-attachments/assets/f9dee40b-cb8c-4e16-a224-ee159cca0f66" />

## Step 1 - Install and Configure ANSIBLE ON EC2 Instance

### 1. Update `Name` tag on your Jenkins EC2 Instance to `Jenkins-Ansible`. We will use this server to run playbooks.

<img width="800" alt="ansible" src="https://github.com/user-attachments/assets/05756787-2164-4dcb-a5f8-01a51b940399" />

### 2. In your GitHub account create a new repository and name it ansible-config-mgt

<img width="800" alt="git ansible" src="https://github.com/user-attachments/assets/e5ed66e6-5e90-4414-9f8b-fab5d0fa5d23" />

### 3. Instal Ansible ([See: install ansible with pip](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html#installing-ansible-with-pip))

```
sudo apt update
```
<img width="800" alt="update" src="https://github.com/user-attachments/assets/12e695a0-188b-4f15-99c0-932019d83f76" />

```
sudo apt install ansible
```
<img width="800" alt="install ansible" src="https://github.com/user-attachments/assets/c0aa71f3-3d76-42f5-aba3-33211982ef8d" />

__Check your ansible version__

```
ansible --version
```
<img width="800" alt="ansible version" src="https://github.com/user-attachments/assets/bdd4a9bc-ea78-44ca-849f-709fc1095d77" />

### 4. Configure Jenkins build job to save your repository content every time you change it – this will solidify your Jenkins configuration skills acquired in Project 9

- Configure a Webhook in GitHub and set the webhook to trigger `ansible` build.
On `ansible-config-mgt` repository, select Settings > Webhooks > Add webhook

<img width="800" alt="webhook" src="https://github.com/user-attachments/assets/4ae9aa5f-c0cb-4ad0-bc16-47144e62c96c" />

- Create a new Freestyle project `ansible` in Jenkins

<img width="800" alt="jenkins ansible" src="https://github.com/user-attachments/assets/d181d7a3-e017-45e7-81b0-0bd9bb62409f" />

- Point it to the `ansible-config-mgt` repository
Copy the repository URL

<img width="800" alt="ansible https" src="https://github.com/user-attachments/assets/8e9a069e-a6c2-4f4b-ac7f-d75084a50fe3" />

In configuration of our `ansible` freestyle project choose `Git`, provide there the link to our `ansible-config-mgt` GitHub repository and credentials (user/password) so Jenkins could access files in the repository.

<img width="800" alt="ansible config" src="https://github.com/user-attachments/assets/5716e41f-1156-478c-b19f-ef7fc526765b" />

<img width="800" alt="ansible configure" src="https://github.com/user-attachments/assets/0689988e-b6f6-4f31-a741-374aa2c84614" />

- Configure a Post-build job to save all (**) files, like you did it in [Project 9](https://github.com/Oluwafunmisi/StegHub_DevOps-Cloud_Engineering/blob/main/tooling_website_deployment_automation_with_CI/tooling_website_deployment_automation_with_CI.md).

<img width="800" alt="post build" src="https://github.com/user-attachments/assets/b5d074d9-7cea-40e9-b4da-470bb31f202e" />

### 5. Test your setup by making some change in README.MD file in `master` branch and make sure that builds starts automatically and Jenkins saves the files (build artifacts) in following folder

<img width="800" alt="test" src="https://github.com/user-attachments/assets/2ad098f8-dd13-4ece-a344-dbfe444a04e9" />

Check `ansible` project on jenkins for the build

<img width="800" alt="test update" src="https://github.com/user-attachments/assets/19f71b5f-f90e-47e3-8939-468f4b839dc3" />

Console output

<img width="800" alt="console output" src="https://github.com/user-attachments/assets/f40925e0-3f44-4436-b64a-7d8c5c7ca762" />

```
ls /var/lib/jenkins/jobs/ansible/builds/<build_number>/archive/
```
<img width="800" alt="lib" src="https://github.com/user-attachments/assets/5d764f01-744d-4ca4-8691-e8d1c5d2a583" />

__Note:__ Trigger Jenkins project execution only for /main (master) branch.

Now your setup will look like this:

<img width="800" alt="image-46-1536x818" src="https://github.com/user-attachments/assets/1b984a73-6dc1-4d22-b89b-1834bdc99ae1" />

__Tip__: Allocate an Elastic IP to your Jenkins-Ansible server to avoid reconfigure of GitHub webhook to a new IP address anytime you stop/start your Jenkins-Ansible server.

Allocate elastic IP

<img width="800" alt="allocate" src="https://github.com/user-attachments/assets/7a8508c5-0b82-4f9c-b696-98ebf5b7026c" />

Associate the elastic IP

<img width="800" alt="associate" src="https://github.com/user-attachments/assets/5b6be8cd-4c89-4e81-a2a8-ee2ed8047fc4" />

<img width="800" alt="elastic ip" src="https://github.com/user-attachments/assets/7d3ea46a-4d9a-45cc-8800-7326cdae894b" />

Update the webhook

<img width="800" alt="webhook update" src="https://github.com/user-attachments/assets/3a703190-a601-4715-b0d1-310633b43804" />

__Note:__ Elastic IP is free only when it is being allocated to an EC2 Instance, so do not forget to release Elastic IP once you terminate your EC2 Instance.


## Step 2 – Prepare your development environment using Visual Studio Code

### 1. First part of `DevOps` is `Dev`, which means you will require to write some codes and you shall have proper tools that will make your coding and debugging comfortable – you need an Integrated development environment (IDE) or Source-code Editor.
There is a plethora of different IDEs and Source-code Editors for different languages with their own advantages and drawbacks, you can choose whichever you are comfortable with, but we recommend one free and universal editor that will fully satisfy your needs – [Visual Studio Code (VSC)](https://code.visualstudio.com/download).

### 2. After you have successfully installed `VSC`, configure it to connect to your newly created GitHub repository.

__Make sure _Git is downloaded on your device_ > _Select clone github repository_ and _clone repository___

<img width="800" alt="ansible clone" src="https://github.com/user-attachments/assets/e3a2c655-e944-4ed8-81cc-17f8b5ec3f73" />





