# Ansible Refactoring & Static Assignments (Imports and Roles)

In this project, we will continue working with ansible-config-mgt repository and make some improvements of our code. Now we need to refactor our Ansible code, create assignments, and learn how to use the imports functionality. Imports allow to effectively re-use previously created playbooks in a new playbook - it allows us to organize our tasks and reuse them when needed.


## Step 1 - Jenkins job enhancement

Before we begin, let us make some changes to our Jenkins job - now every new change in the codes creates a separate directory which is not very convenient when we want to run some commands from one place. Besides, it consumes space on Jenkins serves with each subsequent change. Let us enhance it by introducing a new Jenkins project/job - we will require `Copy Artifact` plugin.

1. Go to your `Jenkins-Ansible` server and create a new directory called `ansible-config-artifact` - we will store there all artifacts after each build.

<img width="800" alt="ssh" src="https://github.com/user-attachments/assets/42a2e581-d682-478c-aa6a-fa05bdf68f91" />

```
sudo mkdir /home/ubuntu/ansible-config-artifact
```
<img width="800" alt="mkdir" src="https://github.com/user-attachments/assets/fba212df-205a-49a8-983b-105d7f333036" />

2. Change permissions to this directory, so Jenkins could save files there

```
sudo chmod -R 0777 /home/ubuntu/ansible-config-artifact
```
<img width="800" alt="chmod" src="https://github.com/user-attachments/assets/308c4e71-4a3c-4834-8025-f3020bccee98" />

3. Go to Jenkins web console -> Manage Jenkins -> Manage Plugins -> on Available tab search for `Copy Artifact` and install this plugin without restarting Jenkins

<img width="800" alt="copy artifact" src="https://github.com/user-attachments/assets/c88e0458-5cc1-4b20-a8a1-9014c915f744" />

<img width="800" alt="artifact" src="https://github.com/user-attachments/assets/9f57db51-19a4-4f1c-853a-eb7ccbd41893" />

4. Create a new Freestyle project and name it `save_artifacts`.

<img width="800" alt="save artifacts" src="https://github.com/user-attachments/assets/ccd57194-c40b-4bf7-9c55-dca46f17c2ae" />

5. This project will be triggered by completion of your existing ansible project. Configure it accordingly

<img width="800" alt="triggers" src="https://github.com/user-attachments/assets/09d61bea-a06e-4ca3-8359-5bd6d8a8b939" />

__Note:__ You can configure number of builds to keep in order to save space on the server, for example, you might want to keep only last 2 or 5 build results. You can also make this change to your ansible job.

6. The main idea of `save_artifacts` project is to save artifacts into `/home/ubuntu/ansible-config-artifact` directory. To achieve this, create a `Build` step and choose `Copy artifacts from other project`, specify `ansible` as a source project and `/home/ubuntu/ansible-config-artifact` as a target directory.

<img width="800" alt="build step" src="https://github.com/user-attachments/assets/9bf9a092-8e78-4144-b3fb-4015cfde7fbc" />

7. Test your set up by making some change in README.MD file inside your `ansible-config-mgt` repository (right inside main branch).

<img width="800" alt="first view" src="https://github.com/user-attachments/assets/f57c22b9-91c3-44c3-9df5-fa70ac28834e" />

Remove the line - Testing

<img width="800" alt="second view" src="https://github.com/user-attachments/assets/ddf70b2e-504a-442c-8425-4d2c9b999f56" />

If both Jenkins jobs have completed one after another - you shall see your files inside `/home/ubuntu/ansible-config-artifact` directory and it will be updated with every commit to your master branch.
Now your Jenkins pipeline is more neat and clean.

<img width="800" alt="build error" src="https://github.com/user-attachments/assets/adc9081f-6d4d-45a4-9093-92da9f784eb6" />

The error above is from the jenkins console output.
This is because jenkins does not have the privillege to write to the `ansible-config-artifact` directory despite setting permission `chmod -R 777 ansible-config-artifact`
This was resolved by adding jenkins user to ubuntu group (has `rwx` permission)

```

sudo chown -R jenkins:jenkins /home/ubuntu/ansible-config-artifact

ls -ld /home/ubuntu/ansible-config-artifact

ls -ld /home /home/ubuntu

sudo chmod 755 /home/ubuntu

sudo -u jenkins ls -la /home/ubuntu/ansible-config-artifact
```
<img width="800" alt="permission" src="https://github.com/user-attachments/assets/f61fdba7-2111-4f48-b6b7-b5895f2638e2" />

Now test the setup again. observe that the build was successful this time

<img width="800" alt="success" src="https://github.com/user-attachments/assets/bd0412d7-3219-402c-a9dd-7167dbc3ae2c" />

## Step 2 - Refactor Ansible code by importing other playbooks into `site.yml`

Before starting to refactor the codes, ensure that you have pulled down the latest code from master (main) branch, and create a new branch, name it refactor.

<img width="800" alt="origin main" src="https://github.com/user-attachments/assets/4ac6a6c6-6487-4dab-9b41-dd85d823c6c1" />

`DevOps` philosophy implies constant iterative improvement for better efficiency - refactoring is one of the techniques that can be used, but you always have an answer to question "why?". Why do we need to change something if it works well?

In previous project, you wrote all tasks in a single playbook common.yml, now it is pretty simple set of instructions for only 2 types of `OS`, but imagine you have many more tasks and you need to apply this playbook to other servers with different requirements.
In this case, you will have to read through the whole playbook to check if all tasks written there are applicable and is there anything that you need to add for certain `server/OS` families. Very fast it will become a tedious exercise and your playbook will become messy with many commented parts. Your DevOps colleagues will not appreciate such organization of your codes and it will be difficult for them to use your playbook.

Let see code re-use in action by importing other playbooks.

1. Within `playbooks` folder, create a new file and name it `site.yml` - This file will now be considered as an entry point into the entire infrastructure configuration. Other playbooks will be included here as a reference. In other words, `site.yml` will become a parent to all other playbooks that will be developed. Including common.yml that you created previously.

2. Create a new folder in root of the repository and name it `static-assignments`. The __static-assignments__ folder is where all other children playbooks will be stored. This is merely for easy organization of your work. It is not an Ansible specific concept, therefore you can choose how you want to organize your work. You will see why the folder name has a prefix of __static__ very soon. For now, just follow along.

3. Move `common.yml` file into the newly created `static-assignments` folder.

<img width="800" alt="touch static" src="https://github.com/user-attachments/assets/3f2814ee-2bf1-4ec4-a264-d73c322c0e00" />

4. Inside `site.yml` file, import `common.yml` playbook.

```
- hosts: all
- import_playbook: ../static-assignments/common.yml
```
<img width="800" alt="in siteyml" src="https://github.com/user-attachments/assets/95dea2b3-eff1-415f-995f-0237ff4cb8b2" />

The code above uses built in `import_playbook` Ansible module.

Your folder structure should look like this;

```
├── static-assignments
│   └── common.yml
├── inventory
    └── dev
    └── stage
    └── uat
    └── prod
└── playbooks
    └── site.yml

```
<img width="800" alt="look" src="https://github.com/user-attachments/assets/6c164321-1d47-48d0-9c2c-e0359b6352e5" />

__5. Run `ansible-playbook` command against the `dev` environment__

Since you need to apply some tasks to your `dev` servers and `wireshark` is already installed - you can go ahead and create another playbook under `static-assignments` and name it `common-del.yml`.

```
touch static-assignments/common-del.yml
```
<img width="800" alt="newlook" src="https://github.com/user-attachments/assets/1e2b6b8b-c6d6-4a3d-9c38-6b64d92ff16b" />

In this playbook, configure deletion of `wireshark` utility.

```
- name: update web, nfs and db servers
  hosts: webservers, nfs, db
  remote_user: ec2-user
  become: yes
  become_user: root
  tasks:
  - name: delete wireshark
    yum:
      name: wireshark
      state: removed

- name: update LB server
  hosts: lb
  remote_user: ubuntu
  become: yes
  become_user: root
  tasks:
  - name: delete wireshark
    apt:
      name: wireshark-qt
      state: absent
      autoremove: yes
      purge: yes
      autoclean: yes
```
<img width="800" alt="commoldelyml" src="https://github.com/user-attachments/assets/38ec9606-d2e8-48a9-8c92-ce5f91a7e7cb" />

__Update `site.yml` with `- import_playbook: ../static-assignments/common-del.yml` instead of `common.yml`__

```
- hosts: all
- import_playbook: ../static-assignments/common-del.yml
```
<img width="800" alt="site update" src="https://github.com/user-attachments/assets/208ea592-8908-45ff-a6d4-48892dbe5efa" />

__Run it against dev servers__

```
cd /home/ubuntu/ansible-config-mgt/

ansible-playbook -i inventory/dev.yml playbooks/site.yml
```

<img width="800" alt="task1" src="https://github.com/user-attachments/assets/4b471f19-20c1-4351-aa07-1ea3a04b6f07" />

<img width="800" alt="task2" src="https://github.com/user-attachments/assets/6c2b215d-d01d-4493-b036-4864d28edbea" />

__Ensure that wireshark is deleted on all the servers__

__Run__ `wireshark --version` or `ansible all -i inventory/dev.yml -m shell -a "rpm -qa | grep wireshark || dpkg -l | grep wireshark || echo 'wireshark not installed'"` to check

<img width="800" alt="wireshark not installed" src="https://github.com/user-attachments/assets/6d14d7e5-4ec9-4ecc-b902-b0ee6e8cc383" />

Now you have learned how to use import_playbooks module and you have a ready solution to install/delete packages on multiple servers with just one command.


## Step 3 - Configure UAT Webservers with a role `Webserver`

We have our nice and clean dev environment, so let us put it aside and configure 2 new Web Servers as uat. We could write tasks to configure Web Servers in the same playbook, but it would be too messy, instead, we will use a dedicated role to make our configuration reusable.

1. Launch 2 fresh EC2 instances using RHEL 9 image, we will use them as our uat servers, so give them names accordingly - `Web1-UAT` and `Web2-UAT`.

<img width="800" alt="running" src="https://github.com/user-attachments/assets/ca0446b7-6af7-448c-985e-9d8c05209cdd" />

2. To create a role, you must create a directory called `roles/`, relative to the playbook file or in `/etc/ansible/` directory.

There are two ways how you can create this folder structure:

Use an Ansible utility called ansible-galaxy inside `ansible-config-mgt/roles` directory (you need to create `roles` directory upfront)

```
mkdir roles
cd roles
ansible-galaxy init webserver
```

__Note__: You can choose either way, but since you store all your codes in GitHub, it is recommended to create folders and files there rather than locally on `Jenkins-Ansible` server.

The entire folder structure should look like below, but if you create it manually - you can skip creating `tests`, `files`, and `vars` or remove them if you used `ansible-galaxy`

```
└── webserver
    ├── README.md
    ├── defaults
    │   └── main.yml
    ├── files
    ├── handlers
    │   └── main.yml
    ├── meta
    │   └── main.yml
    ├── tasks
    │   └── main.yml
    ├── templates
    ├── tests
    │   ├── inventory
    │   └── test.yml
    └── vars
        └── main.yml
```

<img width="800"  alt="galaxy" src="https://github.com/user-attachments/assets/6b960e36-faf8-48d2-9d13-5dd64220d32d" />

After removing unnecessary directories and files, the roles structure should look like this

```
└── webserver
    ├── README.md
    ├── defaults
    │   └── main.yml
    ├── handlers
    │   └── main.yml
    ├── meta
    │   └── main.yml
    ├── tasks
    │   └── main.yml
    └── templates
```
<img width="800" alt="webserver remove" src="https://github.com/user-attachments/assets/7d30fba7-5eff-41b3-b381-867175581ce7" />

3. Update your inventory `ansible-config-mgt/inventory/uat.yml` file with IP addresses of your 2 `UAT Web servers`

__NOTE:__ Ensure you are using ssh-agent to ssh into the `Jenkins-Ansible` instance

```
[uat-webservers]
<Web1-UAT-Server-Private-IP-Address> ansible_ssh_user='ec2-user'
<Web2-UAT-Server-Private-IP-Address> ansible_ssh_user='ec2-user'
```
<img width="865" height="380" alt="uat" src="https://github.com/user-attachments/assets/c480ed54-c583-494b-85fe-bfe3a3f40fab" />

To learn how to setup SSH agent and connect VS Code to your Jenkins-Ansible instance, please see this video:

- For Windows users - [ssh-agent on windows](https://www.youtube.com/watch?v=TYyTXxVWOYA)
- For Linux users - [ssh-agent on linux](https://www.youtube.com/watch?v=EoLrCX1VVog)


4. In /etc/ansible/ansible.cfg file uncomment roles_path string and provide a full path to your roles directory roles_path = /home/ubuntu/ansible-config-mgt/roles, so Ansible could know where to find configured roles.

<img width="800" alt="ansible" src="https://github.com/user-attachments/assets/fc3c7058-b81d-4b43-a5d0-6fb72db86c0f" />

5. It is time to start adding some logic to the webserver role. Go into `tasks` directory, and within the `main.yml` file, start writing configuration tasks to do the following:

- Install and configure Apache (httpd service)
- Clone Tooling website from GitHub https://github.com/<your-name>/tooling.git.
- Ensure the tooling website code is deployed to /var/www/html on each of 2 UAT Web servers.
- Make sure httpd service is started


Your main.yml consist of following tasks:
```
# tasks file for webserver

- name: install apache
  become: true
  ansible.builtin.yum:
    name: "httpd"
    state: present

- name: install git
  become: true
  ansible.builtin.yum:
    name: "git"
    state: present

- name: clone a repo
  become: true
  ansible.builtin.git:
    repo: https://github.com/<your-name>/tooling.git
    dest: /var/www/html
    force: yes

- name: copy html content to one level up
  become: true
  command: cp -r /var/www/html/html/ /var/www/

- name: Start service httpd, if not started
  become: true
  ansible.builtin.service:
    name: httpd
    state: started

- name: recursively remove /var/www/html/html/ directory
  become: true
  ansible.builtin.file:
    path: /var/www/html/html
    state: absent
```
<img width="800" alt="main" src="https://github.com/user-attachments/assets/df8aa370-e65e-4b67-8c04-a6c26bc6ac88" />

## Step 4 - Reference `Webserver` role

Within the static-assignments folder, create a new assignment for uat-webservers `uat-webservers.yml`. This is where you will reference the role.

```
- hosts: uat-webservers
  roles:
     - webserver
```
<img width="800" alt="uat" src="https://github.com/user-attachments/assets/ff43d673-a1d6-40a0-8acf-77d976dcb7db" />

Remember that the entry point to our ansible configuration is the site.yml file. Therefore, you need to refer your `uat-webservers.yml` role inside `site.yml`.

So, we should have this in `site.yml`

```
- hosts: all
- import_playbook: ../static-assignments/common.yml

- hosts: uat-webservers
- import_playbook: ../static-assignments/uat-webservers.yml
```

<img width="800" alt="new site" src="https://github.com/user-attachments/assets/332d9ddc-0289-4cf6-8b5a-b9d1c4031161" />

## Step 5 - Commit & Test

Commit your changes, create a Pull Request and `main` them to master branch, make sure webhook triggered two consequent Jenkins jobs, they ran successfully and copied all the files to your `Jenkins-Ansible` server into `/home/ubuntu/ansible-config-artifact/` directory.

<img width="800" alt="commit" src="https://github.com/user-attachments/assets/0763e17b-08a9-43bc-9087-d6b721fd1189" />

<img width="800" alt="merged" src="https://github.com/user-attachments/assets/89258cfb-2f24-479b-9ace-77622bd1b475" />

<img width="800" alt="jenkins changes" src="https://github.com/user-attachments/assets/dd3aad96-bf4a-4f86-8ec7-06c226939b0d" />

Now run the playbook against your uat inventory and see what happens:

__NOTE:__ Before running your playbook, ensure you have tunneled into your Jenkins-Ansible server via ssh-agent For windows users, see this [video](https://www.youtube.com/watch?v=TYyTXxVWOYA) For [Linux] users, see this [video](https://www.youtube.com/watch?v=EoLrCX1VVog)

```
cd /home/ubuntu/ansible-config-artifact

ansible-playbook -i /inventory/uat.yml playbooks/site.yaml
```
<img width="800" alt="play 1" src="https://github.com/user-attachments/assets/db932949-7e9a-4157-9fa5-02c802dbbdc4" />

<img width="800" alt="play 2" src="https://github.com/user-attachments/assets/1abf52b3-7784-4c4f-bd03-693d8dce8dec" />

You should be able to see both of your UAT Web servers configured and you can try to reach them from your browser:

```
http://<Web1-UAT-Server-Public-IP-or-Public-DNS-Name>/index.php

or

http://<Web1-UAT-Server-Public-IP-or-Public-DNS-Name>/index.php
```

__Access Web1-UAT__

<img width="800" alt="web 1" src="https://github.com/user-attachments/assets/caa43574-42de-402a-939c-fb0a5004909e" />

__Access Web2-UAT__

<img width="800" alt="web 2" src="https://github.com/user-attachments/assets/138b11e2-5d4e-4ed0-87c9-6152c330a833" />

__Our Ansible architecture now looks like this:__

<img width="800" alt="image-53" src="https://github.com/user-attachments/assets/caf72b04-7a2a-4779-b3c3-67017411852c" />
