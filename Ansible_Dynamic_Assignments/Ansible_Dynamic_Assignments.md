# Ansible Dynamic Assignments (Include) and Community Roles

In this project we will introduce [dynamic assignments](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_reuse.html#includes-dynamic-re-use) by using `include module`.
We will continue configuring our `UAT servers`, learn and practice new Ansible concepts and modules.

### EC2 Instances for this project

Ansible server

<img width="800" alt="ansible" src="https://github.com/user-attachments/assets/17bc2709-246c-47b5-964e-8000d0ea20a3" />

UAT Web server 1

<img width="800" alt="web1" src="https://github.com/user-attachments/assets/4bf8ad7e-1851-4c88-b258-b305bbf6a128" />

UAT Web server 2

<img width="800" alt="web 2" src="https://github.com/user-attachments/assets/6f996368-fc33-4b0a-adce-35c3281dbfbf" />

Load balancer server

<img width="800" alt="lb" src="https://github.com/user-attachments/assets/4b26585a-c377-4327-96c1-69474ba955c6" />

Load balancer security group inbound rule

<img width="800" alt="load rules" src="https://github.com/user-attachments/assets/8d12e6e4-f476-4447-b268-b7cac07abf73" />

Database server

<img width="800" alt="database" src="https://github.com/user-attachments/assets/dd745ffb-11c6-4475-a073-eb459de74c02" />

From [previous project](https://steghub.com/lessons/ansible-refactoring-static-assignments-imports-and-roles-101/), we can already tell that static assignments use `import` Ansible module. The module that enables dynamic assignments is `include`.

Hence,
```
import = Static
include = Dynamic
```

When the `import` module is used, all statements are pre-processed at the time playbooks are parsed. Meaning, when you execute `site.yml` playbook, Ansible will process all the playbooks referenced during the time it is parsing the statements. This also means that, during actual execution, if any statement changes, such statements will not be considered. Hence, it is `static`.
On the other hand, when `include` module is used, all statements are processed only during execution of the playbook. Meaning, after the statements are parsed, any changes to the statements encountered during execution will be used.

Take note that in most cases it is recommended to use `static assignments` for playbooks, because it is more reliable. With `dynamic` ones, it is hard to debug playbook problems due to its dynamic nature. However, you can use dynamic assignments for environment specific variables as we will be introducing in this project.


## Introducing Dynamic Assignment Into Our structure

In your `https://github.com/<your-name>/ansible-config-mgt` GitHub repository start a new branch and call it `dynamic-assignments`.

```
git checkout -b dynamic-assignments
```

Create a new folder, name it `dynamic-assignments`.
Then inside this folder, create a new file and name it `env-vars.yml`. We will instruct `site.yml` to `include` this playbook later. For now, let us keep building up the structure.

```
mkdir dynamic-assignments
touch dynamic-assignments/env-vars.yml
```
<img width="800" alt="dynamic assignment" src="https://github.com/user-attachments/assets/b0e4817f-ed34-482b-ad68-a9e61bc4ce4b" />

Your GitHub shall have following structure by now.

__Note__: Depending on what method you used in the previous project you may have or not have `roles` folder in your GitHub repository - if you used `ansible-galaxy`, then `roles` directory was only created on your `Jenkins-Ansible` server locally. It is recommended to have all the codes managed and tracked in GitHub, so you might want to recreate this structure manually in this case - it is up to you.

```
├── dynamic-assignments
│   └── env-vars.yml
├── inventory
│   └── dev
    └── stage
    └── uat
    └── prod
└── playbooks
    └── site.yml
└── roles (optional folder)
    └──...(optional subfolders & files)
└── static-assignments
    └── common.yml
```

Since we will be using the same Ansible to configure multiple environments, and each of these environments will have certain unique attributes, such as `servername`, `ip-address` etc., we will need a way to set values to variables per specific environment.

For this reason, we will now create a folder to keep each environment's variables file. Therefore, create a new folder `env-vars`, then for each environment, create new `YAML` files which we will use to set variables.

```
mkdir env-vars

touch env-vars/dev.yml env-vars/stage.yml env-vars/uat.yml env-vars/prod.yml

git add .

git commit -m "Add dynamic assignments environment variables structure"

git push origin dynamic-assignments
```
<img width="800" alt="env commit" src="https://github.com/user-attachments/assets/18080f98-4b02-495f-a631-16bc982703e2" />

Your layout should now look like this.

```
├── dynamic-assignments
│   └── env-vars.yml
├── env-vars
    └── dev.yml
    └── stage.yml
    └── uat.yml
    └── prod.yml
├── inventory
    └── dev
    └── stage
    └── uat
    └── prod
├── playbooks
    └── site.yml
└── static-assignments
    └── common.yml
    └── webservers.yml
```

Now paste the instruction below into the `env-vars.yml` file.

```
---
- name: looping through list of available files
  include_vars: "{{ item }}"
  with_first_found:
    - files:
        - dev.yml
        - stage.yml
        - prod.yml
        - uat.yml
      paths:
        - "{{ playbook_dir }}/../env-vars"
  tags:
    - always
```
<img width="800" alt="env" src="https://github.com/user-attachments/assets/d1a41908-581b-4a3d-b75d-4f7c1725aa72" />

Notice 3 things to notice here:

1. We used `include_vars` syntax instead of `include`, this is because Ansible developers decided to separate different features of the module. From Ansible version 2.8, the `include` module is deprecated and variants of `include_*` must be used. These are:

- [include_role](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/include_role_module.html#include-role-module)
- [include_tasks](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/include_tasks_module.html#include-tasks-module)
- [include_vars](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/include_vars_module.html#include-vars-module)

In the same version, variants of `import` were also introduces, such as:

- [import_role](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/import_role_module.html#import-role-module)
- [import_tasks](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/import_tasks_module.html#import-tasks-module)

2. We made use of a [special variables](https://docs.ansible.com/ansible/latest/reference_appendices/special_variables.html) `{{ playbook_dir }}` and `{{ inventory_file }}`. `{{ playbook_dir }}` will help Ansible to determine the location of the running playbook, and from there navigate to other path on the filesystem. `{{ inventory_file }}` on the other hand will dynamically resolve to the name of the inventory file being used, then append `.yml` so that it picks up the required file within the `env-vars` folder.

3. We are including the variables using a loop. `with_first_found` implies that, looping through the list of files, the first one found is used. This is good so that we can always set default values in case an environment specific env file does not exist.


## Update `site.yml` with dynamic assignments

Update `site.yml` file to make use of the dynamic assignment. (At this point, we cannot test it yet. We are just setting the stage for what is yet to come. So hang on to your hats)

`site.yml` should now look like this.

```
---
- hosts: all
  name: Include dynamic variables
  become: yes
  tasks:
    - include_tasks: ../dynamic-assignments/env-vars.yml
      tags:
        - always

- import_playbook: ../static-assignments/common.yml

- import_playbook: ../static-assignments/uat-webservers.yml

- import_playbook: ../static-assignments/loadbalancers.yml
```
<img width="800" alt="site" src="https://github.com/user-attachments/assets/2d122020-baa1-447b-a18f-2c884bec09a9" />

## Community Roles

Now it is time to create a role for `MySQL` database - it should install the `MySQL` package, create a database and configure users. But why should we re-invent the wheel? There are tons of roles that have already been developed by other open source engineers out there. These roles are actually production ready, and dynamic to accomodate most of Linux flavours. With Ansible Galaxy again, we can simply download a ready to use ansible role, and keep going.

## Download Mysql Ansible Role

You can browse available community roles [here](https://galaxy.ansible.com/ui/)
We will be using a [MySQL role developed by geerlingguy](https://galaxy.ansible.com/ui/standalone/roles/geerlingguy/mysql/).

__Hint__: To preserve your your GitHub in actual state after you install a new role - make a commit and push to master your `ansible-config-mgt` directory. Of course you must have `git` installed and configured on `Jenkins-Ansible` server and, for more convenient work with codes, you can configure `Visual Studio Code to work with this directory`. In this case, you will no longer need webhook and Jenkins jobs to update your codes on `Jenkins-Ansible` server, so you can disable it - we will be using Jenkins later for a better purpose.

### Configure vscode to work with the directory (`ansible-config-mgt`)

__Configure SSH for vscode__

<img width="800" alt="connect host" src="https://github.com/user-attachments/assets/24830827-ed50-4aaf-a69d-66f3257346ec" />

HostName = Jenkins-Ansible Public IP Address

<img width="800"  alt="config" src="https://github.com/user-attachments/assets/d2c0eb92-de64-4cc1-a56c-5701b2309764" />

<img width="800" alt="jenkins ansible" src="https://github.com/user-attachments/assets/99510cfa-0707-4424-9633-91e50c8d98f8" />

Click on `Open Folder` and Select `ansible-config-mgt`

On `Jenkins-Ansible` server make sure that `git` is installed with `git --version`, then go to `ansible-config-mgt` directory and run

```
git init
git pull https://github.com/<your-name>/ansible-config-mgt.git
git remote add origin https://github.com/<your-name>/ansible-config-mgt.git
git branch roles-feature
git switch roles-feature
```
<img width="800" alt="branch role feature" src="https://github.com/user-attachments/assets/95abf6f3-526c-42a4-a0c0-c682f8add01d" />

### Inside `roles` directory create your new `MySQL role` with `ansible-galaxy` install `geerlingguy.mysql`

```
ansible-galaxy role install geerlingguy.mysql
```
<img width="800" alt="sql" src="https://github.com/user-attachments/assets/ee437360-17bd-4ff8-94a3-02b0ea247005" />

__Rename the folder to `mysql`__

```
mv geerlingguy.mysql/ mysql
```
<img width="800" alt="sql rename" src="https://github.com/user-attachments/assets/2d82bd24-ed8f-46c9-855b-03ad88075645" />

Read README.md file, and edit roles configuration to use correct credentials for MySQL required for the tooling website.

### Create Database and mysql user (`roles/mysql/vars/main.yml`)

Latest update does not contain main.yml so manually create

```
cd ~/ansible-config-mgt/roles/mysql
mkdir -p vars
nano vars/main.yml
```

```
mysql_root_password: ""
mysql_databases:
  - name: tooling
    encoding: utf8
    collation: utf8_general_ci
mysql_users:
  - name: webaccess
    host: "172.31.32.0/20" # Webserver subnet cidr
    password: PassWord123!
    priv: "tooling.*:ALL"
```
<img width="800" alt="main yml" src="https://github.com/user-attachments/assets/7543548c-5106-48b2-b7b9-8fb7a651fe98" />

### Create a new playbook inside `static-assignments` folder and name it `db-servers.yml` , update it with `mysql` roles.

```
- hosts: db_servers
  become: yes
  vars_files:
    - vars/main.yml
  roles:
    - { role: mysql }
```
<img width="800" alt="db server" src="https://github.com/user-attachments/assets/db1048be-e57a-4d44-8c9e-128d7b6cd154" />

### Now it is time to upload the changes into your GitHub:

```
git add .
git commit -m "Add database servers playbook with MySQL role"
git push origin dynamic-assignments
```

<img width="800" alt="commit" src="https://github.com/user-attachments/assets/80f976e5-d33e-4281-b6ff-9c6bd4da4671" />

<img width="800" alt="push" src="https://github.com/user-attachments/assets/55b771ca-0982-42d3-aa3b-78777a8a6864" />

### Now, if you are satisfied with your codes, you can create a Pull Request.

<img width="800"  alt="merge" src="https://github.com/user-attachments/assets/8d53ec4d-daf1-4401-9f7c-35b18c131191" />

### Merge it to `main` branch on GitHub

<img width="800" alt="merged" src="https://github.com/user-attachments/assets/14ca4cc4-a5cf-477e-88d4-fe6d12f01f25" />

# Load Balancer roles

We want to be able to choose which Load Balancer to use, `Nginx` or `Apache`, so we need to have two roles respectively:

1. Nginx
2. Apache

With your experience on Ansible so far you can:

- Decide if you want to develop your own roles, or find available ones from the community

### Using the Community

```
ansible-galaxy role install geerlingguy.nginx

ansible-galaxy role install geerlingguy.apache
```
<img width="800" alt="nginx apache" src="https://github.com/user-attachments/assets/d8aa7426-7d02-44c3-bbce-4758cbac42b2" />

### Rename the installed Nginx and Apache roles

```
mv geerlingguy.nginx nginx

mv geerlingguy.apache apache
```
<img width="800" alt="apache nginx" src="https://github.com/user-attachments/assets/2aede860-8a35-4e6e-94e3-c6235e0fb672" />

### The folder structure now looks like this

<img  alt="view" src="https://github.com/user-attachments/assets/e8ff6e05-5060-4351-8d58-5de92a4d059b" />

- ### Update both static-assignment and site.yml files to refer the roles

__Important Hints:__

- Since you cannot use both `Nginx` and `Apache` load balancer, you need to add a condition to enable either one - this is where you can make use of variables.
- Declare a variable in `defaults/main.yml` file inside the `Nginx` and `Apache` roles. Name each variables `enable_nginx_lb` and `enable_apache_lb` respectively.
- Set both values to `false` like this `enable_nginx_lb: false` and `enable_apache_lb: false`.
- Declare another variable in both roles `load_balancer_is_required` and set its value to `false` as well

### For nginx

```
# roles/nginx/defaults/main.yml
enable_nginx_lb: false
load_balancer_is_required: false
```
<img width="800"  alt="main nginx" src="https://github.com/user-attachments/assets/580c082f-9a50-40b1-a8d2-c41c50557676" />

### For apache

```
# roles/apache/defaults/main.yml
enable_apache_lb: false
load_balancer_is_required: false
```
<img width="800" alt="main apache" src="https://github.com/user-attachments/assets/a7cca919-b69a-426a-9e5b-8153cbc20a64" />

### Update assignment

`loadbalancers.yml` file

```
---
- hosts: lb
  become: yes
  roles:
    - role: nginx
      when: enable_nginx_lb | bool and load_balancer_is_required | bool
    - role: apache
      when: enable_apache_lb | bool and load_balancer_is_required | bool
```

<img width="800" alt="load balancer" src="https://github.com/user-attachments/assets/d3a8e2fe-fb80-4f9e-8418-4bb7608404a0" />

- ### Update `site.yml` files respectively

```
---
- hosts: all
  name: Include dynamic variables
  become: yes
  tasks:
    - include_tasks: ../dynamic-assignments/env-vars.yml
      tags:
        - always

- import_playbook: ../static-assignments/common.yml

- import_playbook: ../static-assignments/uat-webservers.yml

- import_playbook: ../static-assignments/loadbalancers.yml

- import_playbook: ../static-assignments/db-servers.yml
```

<img width="800" alt="new site" src="https://github.com/user-attachments/assets/ef803449-c46d-49cb-b26a-7a4e6bd67e82" />


Now you can make use of `env-vars\uat.yml` file to define which `loadbalancer` to use in UAT environment by setting respective environmental variable to `true`.

You will activate `load balancer`, and enable `nginx` by setting these in the respective environment's `env-vars` file.

### Enable Nginx

```
enable_nginx_lb: true
load_balancer_is_required: true
```
<img width="800" alt="enable uat" src="https://github.com/user-attachments/assets/3e667c5a-c3e8-45d7-a7f6-22bc1ed1bc49" />

# Your flow should be:
```
Jenkins-Ansible server
(Private IP)
        |
        |  ansible-playbook
        |
        +----------------+
        |                |
   Load Balancer     Web Servers     Database Server
   (nginx/apache)    (web role)      (mysql role)

### Run the playbook against the uat inventory
```
```
ansible-playbook -i inventory/uat.yml playbooks/site.yml --extra-vars "env=uat"
```

<img width="800" alt="task 1" src="https://github.com/user-attachments/assets/18d309e7-c538-4afb-95b2-23040441ac38" />
<img width="800" alt="task 2" src="https://github.com/user-attachments/assets/4ca5df58-3f9a-44e5-af8f-492e102ef1b8" />
<img width="800"  alt="task 3" src="https://github.com/user-attachments/assets/ad5da281-3600-4474-8467-573c50055229" />
<img width="800" alt="task 4" src="https://github.com/user-attachments/assets/f0b2429f-ed48-4960-8e05-09a44a188d28" />
<img width="800"  alt="task 5" src="https://github.com/user-attachments/assets/614155dc-ef91-4cc8-8a9b-b7f99341d15b" />
<img width="800" alt="task 6" src="https://github.com/user-attachments/assets/a6425dd5-5522-4ff5-a544-481725d20fef" />
<img width="800" alt="task 8" src="https://github.com/user-attachments/assets/8d112b34-80dd-46c9-a422-970ff86c8352" />
<img width="800" alt="task 9" src="https://github.com/user-attachments/assets/6f20e0f6-b3b3-4858-895f-d42e25fcefc6" />
<img width="800" alt="task 10" src="https://github.com/user-attachments/assets/857531d7-7f7e-49bd-bd1b-a6151de4ce6a" />
<img width="800" alt="task 11" src="https://github.com/user-attachments/assets/d45de68b-998d-4447-be8a-3e10560c9383" />
<img width="800" alt="task 12" src="https://github.com/user-attachments/assets/05b121be-a8fb-494c-89c4-f46daea760d9" />
<img width="800" alt="task 13" src="https://github.com/user-attachments/assets/9ceedec3-4614-489a-a0da-372c553df0b8" />

# Access the Load balancer

___Update load balancer to point to UAT web server private ip___

```
enable_nginx_lb: true
enable_apache_lb: false
load_balancer_is_required: true

nginx_vhosts:
  - listen: "80"
    server_name: "_"
    locations:
      - path: "/"
        proxy_pass: "http://myapp1"

nginx_upstreams:
  - name: myapp1
    servers:
      - "172.31.35.71"
      - "172.31.36.230"
```
<img width="800" alt="uat update" src="https://github.com/user-attachments/assets/185a30e9-898b-4588-933f-f5a92752889e" />

### Access the tooling website using the LB's Public IP address

<img width="800" alt="load site" src="https://github.com/user-attachments/assets/dc91345c-7c68-45c3-8407-5c6ddfb730a6" />

<img width="800" alt="login" src="https://github.com/user-attachments/assets/2cca8667-5949-41a3-93df-2873d770b12d" />

### Conclusion

We have learned and practiced how to use Ansible configuration management tool to prepare UAT environment for Tooling web solution.
