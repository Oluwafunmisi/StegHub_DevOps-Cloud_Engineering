# Tooling Website deployment automation with Continuous Integration using Jenkins

In this project we are going to start automating part of our routine tasks with a free and open source automation server - Jenkins. It is one of the mostl popular CI/CD tools.

Acording to Circle CI, Continuous integration (CI) is a software development strategy that increases the speed of development while ensuring the quality of the code that teams deploy. Developers continually commit code in small increments which is then automatically built and tested before it is merged with the shared repository.

## Task

Enhance the architecture prepared in the previous project by adding a ```Jenkins``` server, configure a job to automatically deploy source codes changes from ```Git``` to ```NFS``` server.

Here is how the updated architecture looks.

<img width="800" alt="image-25-1536x905" src="https://github.com/user-attachments/assets/d1c77389-1833-4161-8932-497d708934ab" />

# Step 1 - Install Jenkins server

## 1. Create an aws EC2 instance based on Ubuntu Server 24.04 LTS and name it ```Jenkins```

<img width="800" alt="instance" src="https://github.com/user-attachments/assets/8f8e38cd-1dc5-43ab-a850-8ca74d3a184c" />

## 2. Install JDK since Jenkins is a Java-based application

__Access the instance__

```
ssh -i test.pem ubuntu@16.16.210.173
```
<img width="800" alt="ssh" src="https://github.com/user-attachments/assets/a88c4293-9677-49b3-9256-f764a20dd2eb" />

__Update the Instance__

```
sudo apt-get update
```
<img width="800" alt="update" src="https://github.com/user-attachments/assets/a93eb2ec-28e2-4de1-9ada-5d2c217e9bd5" />

__Install JDK(since jenkins is a java based application)__

```
sudo apt install default-jdk-headless
```
<img width="800" alt="jdk" src="https://github.com/user-attachments/assets/d4ce00ba-bd97-4c46-aed1-c195d9aa4a6d" />

__Download the Jenkins key__

```
sudo rm -f /etc/apt/keyrings/jenkins-keyring.asc
sudo wget -O /etc/apt/keyrings/jenkins-keyring.asc \
https://pkg.jenkins.io/debian-stable/jenkins.io-2026.key
```
<img width="800" alt="keyring" src="https://github.com/user-attachments/assets/9f28ee0a-b523-41b4-8617-dbaec8a02037" />

```
gpg --show-keys /etc/apt/keyrings/jenkins-keyring.asc
```
<img width="800" alt="verify" src="https://github.com/user-attachments/assets/5f2856b0-038f-49db-9ebb-0c47638217db" />

## 3. Install Jenkins

__Update ubuntu and install Jenkins__

```
sudo apt update
sudo apt install fontconfig openjdk-21-jre
sudo apt install jenkins
```
<img width="800" alt="uujenkins" src="https://github.com/user-attachments/assets/50ce1c6f-4086-4a26-b515-e116567a7a4e" />

__Confirm Jenkins installation__
```
sudo systemctl status jenkins
```
<img width="800"  alt="image" src="https://github.com/user-attachments/assets/ce341269-8296-43e6-aa57-47a495d84820" />

## 4. By default Jenkins server uses TCP port 8080 - open it by creating a new Inbound rule in the EC2 Security Group

<img width="800" alt="tcp" src="https://github.com/user-attachments/assets/6a92a6e7-0362-475a-81df-5e62f45148a4" />

## 5. Perform initial Jenkins setup

From a browser access ```http://<Jenkins-Server-Public-IP-Address>:8080```
You will be prompted to provide a default admin password.
Retrieve it from the server.

```
http://16.16.210.173:8080
```
<img width="800" alt="jenkins site" src="https://github.com/user-attachments/assets/2514cbb9-b48e-4dc3-b817-d2d5d2d3bd8b" />

```
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```
<img width="800" alt="jenkins login" src="https://github.com/user-attachments/assets/1034d27e-6027-4c35-b07b-68292cfde9d7" />

Then you will be asked which plugins to install - choose suggested plugins

<img width="800" alt="installing" src="https://github.com/user-attachments/assets/eee39aa2-b7de-41b9-aff6-3bb5b885dab2" />

Once plugins installation is done, create an admin user and you will get the jenkins server address.

<img width="800" alt="create admin" src="https://github.com/user-attachments/assets/e88c7521-8200-4a93-adec-0f53940f609c" />

<img width="800" alt="server" src="https://github.com/user-attachments/assets/5975af7b-aa5a-4ee1-be6b-f87d7090bbda" />

The installation is complete

<img width="800" alt="ready" src="https://github.com/user-attachments/assets/94a30058-36cf-4285-89ef-e5b168a3adc9" />

# Step 2 - Configure Jenkins to retrieve source codes from GitHub using Webhooks

In this part, we will learn how to configure a simple Jenkins job/project. This job will will be triggered by GitHub webhooks and will execute a build task to retrieve codes from GitHub and store it locally on Jenkins server.

## 1. Enable webhooks in your GitHub repository settings.

On your GitHub repository,

Select Settings > Webhooks > Add webhook

<img width="800" alt="webhooks" src="https://github.com/user-attachments/assets/b8b1c0be-ba8c-489a-848e-c79e5f6298a6" />

## 2. Go to Jenkins web console, click ``New Item`` and create a ``Freestyle project``

<img width="800"  alt="freestyle" src="https://github.com/user-attachments/assets/409b730f-cc7b-49eb-ac9a-c2ed49124f93" />

__To connect our GitHub repository, we will need to provide its URL, we can copy from the repository itself__

```
https://github.com/Oluwafunmisi/tooling.git
```
<img width="800" alt="tool" src="https://github.com/user-attachments/assets/5fc3ba25-389a-4021-adee-74ddcc4c468e" />

In configuration of our Jenkins freestyle project choose Git repository, provide there the link to our Tooling GitHub repository and credentials (user/password) so Jenkins could access files in the repository.

<img width="800" alt="none" src="https://github.com/user-attachments/assets/34d6c39e-ba10-4a0f-92e7-f8e544c65aff" />

Save the configuration and try to run the build. For now we can only do it manually. Click ``Build Now`` button. After all was configured correctly, the build was successfull and was seen under #3
You can open the build and check in Console Output if it has run successfully.

<img width="800" alt="build" src="https://github.com/user-attachments/assets/97901738-a3e2-4440-8ce9-e54b5e0b49e8" />

But this build does not produce anything and it runs only when we trigger it manually. Let us fix it.

## 3. Click Configure our job/project and add these two configurations

__Configure ``triggering the job from GitHub webhook`` and also Configure ``Post-build Actions`` to ``archive all the files`` - files resulted from a build are called artifacts:__

<img width="800" alt="triggers" src="https://github.com/user-attachments/assets/d215aade-cac1-4206-8331-18c189a6ea9c" />

<img width="800"  alt="post build" src="https://github.com/user-attachments/assets/16d4d695-794a-4cb8-b952-5aa3a7d34765" />

Now, go ahead and make some change in any file in our GitHub repository (e.g. README.MD file) and push the changes to the main branch.

<img width="800" alt="jenkins test" src="https://github.com/user-attachments/assets/15fc6f54-2dc4-4ec1-b029-5e97b46b04d7" />

we will see that a new build has been launched automatically by webhook and its results - artifacts, saved on Jenkins server.

<img width="800" alt="reflected" src="https://github.com/user-attachments/assets/ceccea79-5f44-4f00-ac18-8513a74322bd" />

<img width="800" alt="console" src="https://github.com/user-attachments/assets/6ee21dfc-c806-4424-8b25-6cbf5fa637f9" />

Now we configured an automated Jenkins job  that receives files from GitHub by webhook trigger this method is considered as push because the changes are being pushed and files transfer is initiated by GitHub. There are also other methods: ``trigger one job (downstreadm) from another (upstream)``, ``poll GitHub periodically`` and others.

By default, the artifacts are stored on Jenkins server locally

```
ls /var/lib/jenkins/jobs/tooling_github/builds/<build_number>/archive/
```
<img width="800" alt="ssh view" src="https://github.com/user-attachments/assets/3f286983-3cb5-41b0-a680-a8ba812c56d8" />

# Step 3 - Configure Jenkins to copy files to NFS server via SSH

Now we have our artifacts saved locally on Jenkins server, the next step is to copy them to our NFS server to /mnt/apps directory.

Jenkins is a highly extendable application and there are more than 1400 plugins available. now we will need a plugin that is called ``Publish Over SSH``

### 1. Install Publish Over SSH plugin.

On main dashboard, Select Manage Jenkins > Manage Plugins > Available > Search for Publish over SSH and Install without restart.

<img width="800" alt="possh" src="https://github.com/user-attachments/assets/c582691e-21cc-4347-a1f8-b572f6d315ab" />

<img width="800" alt="plugin" src="https://github.com/user-attachments/assets/c9be4ad8-c68f-4592-83dc-a6255ae0e286" />


### 2. Configure the job/project to copy artifacts over to NFS server

On main dashboard select ``Manage Jenkins > Configure System`` menu item.

Scroll down to Publish over SSH plugin configuration section and configure it to be able to connect to your NFS server:

- Provide a ``private key`` (content of .pem file that we use to connect to NFS server via SSH/Putty)

- Arbitrary name

- Hostname - can be ``private IP address`` of our ``NFS`` server

- Username - ``ec2-user`` (since NFS server is based on EC2 with RHEL 9)

- Remote directory - ``/mnt/apps`` since our Web Servers use it as a mointing point to retrieve files from the NFS server

<img width="800" alt="connection" src="https://github.com/user-attachments/assets/1c00a73f-ea37-45bf-aa84-de0d2f5cbec5" />

Test the configuration and make sure the connection returns Success. N.B that TCP port 22 on NFS server must be open to receive SSH connections

<img width="800" alt="config test" src="https://github.com/user-attachments/assets/95bc96ac-9f49-43bc-b9af-32c63724687d" />

Save the configuration, open your Jenkins job/project configuration page and add another one Post-build Action (``Send build artifact over ssh``).

Also, Configure it to send all files produced by the build into our previouslys define remote directory In our case we want to copy all files and directories, so we use ``**``  If you want to apply some particular pattern to define which files to send - [use this syntax](https://ant.apache.org/manual/dirtasks.html#patterns)

<img width="800" alt="post build actions" src="https://github.com/user-attachments/assets/5e5a1226-9afe-465b-868e-460a8e6e30b3" />

Save this configuration and go ahead, change something in README.MD file in our GitHub Tooling repository

<img width="800" alt="readme update" src="https://github.com/user-attachments/assets/3f13b8aa-c10c-4e18-9954-e362cfb88b05" />

The line created previously in the README.md file have been removed

Webhook will trigger a new job

<img width="800" alt="remove" src="https://github.com/user-attachments/assets/65fbe6d7-3e0c-406f-bfac-a8c74d146a8c" />

<img width="800" alt="console update" src="https://github.com/user-attachments/assets/9b6a3749-e9a7-4da3-95c9-b25c3fe6bce0" />

__To verify README.MD file__

```
cat /mnt/apps/README.md
```
<img width="800" alt="cat" src="https://github.com/user-attachments/assets/f79c2da7-efd0-48d1-af44-5f38ee81c4a0" />

If you see the changes you had previously made in your GitHub - the job works as expected.


