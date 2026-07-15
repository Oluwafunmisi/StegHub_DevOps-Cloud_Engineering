# DevOps Tooling Website Solution

## Introduction

__This project involves implementation of a solution that consists of the following components:__

- Infrastructure: AWS
- Webserver Linux: Red Hat Enterprise Linux 8
- Database Server: Ubuntu 24.04 + MySQL
- Storage Server: Red Hat Enterprise Linux 8 + NFS Server
- Programming Language: PHP
- Code Repository: GitHub

The diagram below shows the architecture of the solution.
  
  <img width="800" alt="image-17" src="https://github.com/user-attachments/assets/f265ebeb-9c75-46a4-b2f2-18d535b7d491" />

## Step 1 - Prepare NFS Server

__1.__ __Spin up an EC2 instance with RHEL Operating System__

<img width="800" alt="launch instance" src="https://github.com/user-attachments/assets/5882e131-878b-45e3-b8b8-4436a77ecf2f" />

__2.__ __Configure Logical volume management on the server__

- Format the lvm as xfs
- Create 3 Logical volumes: lv-opt, lv-apps, lv-logs.
- Create mount points on /mnt directory for the logical volumes as follows:
  - Mount lv-apps on /mnt/apps - To be used by web servers
  - Mount lv-logs on /mnt/logs - To be used by web serveer logs
  - Mount lv-opt on /mnt/opt - To be used by Jenkins server in next project.

#### Create 3 volumes in the same AZ as the NFS Server ec2 each of 10GB and attach all 3 volumes one by one to the NFS Server.

<img width="800" alt="volume" src="https://github.com/user-attachments/assets/884f0a39-4d7c-427d-a91d-31c4162c431f" />

#### Open up the Linux terminal to begin configuration.

```
ssh -i test.pem ec2-user@13.63.129.42
```
<img width="800" alt="ssh" src="https://github.com/user-attachments/assets/ec6734d4-de79-4d86-9742-473e4d96eb34" />

#### Use ```lsblk``` to inspect what block devices are attached to the server. All devices in Linux reside in /dev/ directory. Inspect with ```ls /dev/``` and ensure all 3 newly created devices are there. Their name will likely be ```nvme1n1```, ```nvme2n1``` and ```nvme3n1```

```
lsblk
```
<img width="800" alt="nvme" src="https://github.com/user-attachments/assets/46eaa1e2-7bee-4a7d-ac5c-7745af517769" />

#### Use ```fdisk``` utility to create a single partition on each of the 3 disks

```
sudo fdisk /dev/nvme1n1
```
<img width="800" alt="nvme1" src="https://github.com/user-attachments/assets/f3107c4d-2b6b-40ee-a2dd-94614bdd98f5" />

```
sudo fdisk /dev/nvme2n1
```
<img width="800" alt="nvme2" src="https://github.com/user-attachments/assets/337b2f22-6f79-4f3f-840b-6a834a76dc85" />

```
sudo fdisk /dev/nvme3n1
```
<img width="800" alt="nvme3" src="https://github.com/user-attachments/assets/611a8a5a-e5a1-4110-89a7-1a2ea51800ce" />

#### Use ```lsblk``` utility to view the newly configured partitions on each of the 3 disks

```
lsblk
```
<img width="800" alt="confirm" src="https://github.com/user-attachments/assets/0bb2f12a-0dad-4f8a-9eac-7abf147558d7" />

#### Install ```lvm``` package

```
sudo yum install lvm2 -y

sudo dnf install lvm2

```
<img width="800" alt="yum" src="https://github.com/user-attachments/assets/8ba184f1-1b0c-4f49-b1af-b9d31c380d49" />

#### Use ```pvcreate``` utility to mark each of the 3 dicks as physical volumes (PVs) to be used by LVM. Verify that each of the volumes have been created successfully

```
sudo pvcreate /dev/nvme1n1p1 /dev/nvme2n1p1 /dev/nvme3n1p1

sudo pvs
```
<img width="800" alt="pvcreate" src="https://github.com/user-attachments/assets/9a0d5e0d-d66c-44b1-9326-c983df0d7f16" />

#### Use ```vgcreate``` utility to add all 3 PVs to a volume group (VG). Name the VG ```webdata-vg```. Verify that the VG has been created successfully

```
sudo vgcreate webdata-vg /dev/nvme1n1p1 /dev/nvme2n1p1 /dev/nvme3n1p1

sudo vgs
```
<img width="800" alt="webdata" src="https://github.com/user-attachments/assets/47209f8e-503c-4564-98da-354086881708" />

#### Use ```lvcreate``` utility to create 3 logical volume, ```lv-apps```, ```lv-logs``` and ```lv-opt```. Verify that the logical volumes have been created successfully

```
sudo lvcreate -n lv-apps -L 9G webdata-vg

sudo lvcreate -n lv-logs -L 9G webdata-vg

sudo lvcreate -l 100%FREE -n lv-opt webdata-vg

sudo lvs
```
<img width="800" alt="lvcreate" src="https://github.com/user-attachments/assets/bced7093-4047-4f7b-b499-fbf5cf0a4e6d" />

#### Verify the entire setup

```
sudo vgdisplay -v   #view complete setup, VG, PV and LV
```
<img width="800" alt="verify" src="https://github.com/user-attachments/assets/19a92064-7a9b-4d73-91db-d3d93e452038" />

```
lsblk
```
<img width="800" alt="ls" src="https://github.com/user-attachments/assets/b569b618-9662-4cc0-a522-13b81a70a095" />

#### Use ```mkfs -t xfs``` to format the logical volumes instead of ext4 filesystem

```
sudo mkfs -t xfs /dev/webdata-vg/lv-apps

sudo mkfs -t xfs /dev/webdata-vg/lv-logs

sudo mkfs -t xfs /dev/webdata-vg/lv-opt
```
<img width="800" alt="alo" src="https://github.com/user-attachments/assets/3c3eb2c4-8e37-403e-ab3b-bfe1b922923d" />

Verify setup

```
sudo blkid
```
<img width="800" alt="xfs" src="https://github.com/user-attachments/assets/fc99ceb5-6cfa-4821-bb13-00e2f84005ef" />

#### Create mount point on ```/mnt``` directory

```
sudo mkdir /mnt/apps

sudo mkdir /mnt/logs

sudo mkdir /mnt/opt
```

Verify mount

```
ls /mnt
```
<img width="900" alt="mount" src="https://github.com/user-attachments/assets/8b4a34bd-472f-4804-8aea-c9a4137d6e17" />

Mount the logical volumes

```
sudo mount /dev/webdata-vg/lv-apps /mnt/apps

sudo mount /dev/webdata-vg/lv-logs /mnt/logs

sudo mount /dev/webdata-vg/lv-opt /mnt/opt
```
Verify 

```
df -h
```
<img width="800" alt="dfh" src="https://github.com/user-attachments/assets/a5677578-523d-4a14-b80e-7981232f562e" />

__3.__ __Install NFS Server, configure it to start on reboot and ensure it is up and running__.

```
sudo yum update -y

sudo yum install nfs-utils -y
```
<img width="800" alt="ut" src="https://github.com/user-attachments/assets/5b80c003-d767-4fc6-ae7e-eb2e459b9bcf" />

```
sudo systemctl start nfs-server.service

sudo systemctl enable nfs-server.service

sudo systemctl status nfs-server.service
```
<img width="800" alt="ctl" src="https://github.com/user-attachments/assets/fa722300-f9c9-4922-9144-c368ad6652e3" />

__4.__ __Export the mounts for Webservers' ```subnet cidr```(IPv4 cidr) to connect as clients. For simplicity, all 3 Web Servers are installed in the same subnet but in production set up, each tier should be separated inside its own subnet or higher level of security__

#### Set up permission that will allow the Web Servers to read, write and execute files on NFS.

```
sudo chown -R nobody: /mnt/apps

sudo chown -R nobody: /mnt/logs

sudo chown -R nobody: /mnt/opt

sudo chmod -R 777 /mnt/apps

sudo chmod -R 777 /mnt/logs

sudo chmod -R 777 /mnt/opt

sudo systemctl restart nfs-server.service
```
<img width="800" alt="chown" src="https://github.com/user-attachments/assets/5f36dbbb-a8b9-4810-8afc-f44661a6ec74" />

#### Configure access to NFS for clients within the same subnet (example Subnet Cidr - 172.31.0.0/20)

<img width="800" alt="subnetip" src="https://github.com/user-attachments/assets/0f337433-bffb-41b4-aef8-4056c2a5e54b" />

```
sudo vi /etc/exports

/mnt/apps 172.31.0.0/20(rw,sync,no_all_squash,no_root_squash)
/mnt/logs 172.31.0.0/20(rw,sync,no_all_squash,no_root_squash)
/mnt/opt 172.31.0.0/20(rw,sync,no_all_squash,no_root_squash)

sudo exportfs -arv
```
<img width="800" alt="exports" src="https://github.com/user-attachments/assets/df53ea10-c661-4916-8914-0869e847e457" />

<img width="800" alt="exported" src="https://github.com/user-attachments/assets/ed643442-c6de-4e6f-8da5-24dcd7e46fd4" />

__5.__ __Check which port is used by NFS and open it using the security group (add new inbound rule)__

```
rpcinfo -p | grep nfs
```
<img width="800" alt="port" src="https://github.com/user-attachments/assets/922c069b-7886-48f8-99cd-3fe8c8a4da42" />

__Note__: For NFS Server to be accessible from the client, the following ports must be opened: TCP 111, UDP 111, UDP 2049, NFS 2049.
Set the Web Server subnet cidr as the source

<img width="800" alt="inbound" src="https://github.com/user-attachments/assets/6cf65de5-ee15-4314-8d72-5b3ed7854691" />

## Step 2 - Configure the Database Server

#### Launch an Ubuntu EC2 instance that will have a role - DB Server

<img width="800" alt="database" src="https://github.com/user-attachments/assets/d495ddcc-d786-47d1-9d7f-d5d155e57422" />

#### Access the instance to begin configuration.

```
ssh -i test.pem ubuntu@13.60.52.126
```
<img width="800" alt="ssh" src="https://github.com/user-attachments/assets/64117c48-7210-47d1-9cf8-dc53a9158e69" />

#### Update and upgrade Ubuntu

```
sudo apt update && sudo apt upgrade -y
```
<img width="800" alt="uu" src="https://github.com/user-attachments/assets/4d13a87e-d702-442a-a6cc-b86dbf1128c8" />

__1.__ __Install MySQL Server__

#### Install mysql server

```
sudo apt install mysql-server
```
<img width="800" alt="sql" src="https://github.com/user-attachments/assets/1f76b978-f386-43d2-873c-eb0391050162" />

#### Run mysql secure script

```
sudo mysql_secure_installation
```
<img width="800"  alt="secure" src="https://github.com/user-attachments/assets/90e172d0-6e63-4618-ab0c-4be4940a1f30" />

__2.__ __Create a database and name it ```tooling```__

__3.__ __Create a database user and name it ```webaccess```__

__4.__ __Grant permission to ```webaccess``` user on ```tooling``` database to do anything only from the webservers ```subnet cidr```__

```
sudo mysql


CREATE DATABASE tooling;
CREATE USER 'webaccess'@'172.31.%' IDENTIFIED BY 'Pass123!';
GRANT ALL PRIVILEGES ON tooling.* TO 'webaccess'@'172.31.%';
FLUSH PRIVILEGES;

show databases;

use tooling;
select host, user from mysql.user;
exit
```
<img width="800" alt="webaccess" src="https://github.com/user-attachments/assets/42a44270-f12f-4d5b-953a-7f3d98d2669e" />

#### Set Bind Address and restart MySQL

```
sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf

sudo systemctl restart mysql
sudo systemctl status mysql
```
<img width="800" alt="bind" src="https://github.com/user-attachments/assets/ff5f62a3-66ef-40c7-a0bf-82cc1a49c643" />

<img width="800" alt="restart" src="https://github.com/user-attachments/assets/f27d16e0-9b12-4727-b1e6-43e1f2edf6f9" />

#### Open MySQL port 3306 on the DB Server EC2.

Access to the DB Server is allowed only from the ```Subnet Cidr``` configured as source.

<img width="800" alt="rules" src="https://github.com/user-attachments/assets/fabac190-ccb6-429c-a8c1-823d50a5c645" />

## Step 3 - Prepare the Web Servers

There is need to ensure that the Web Servers can serve the same content from a shared storage solution, in this case - NFS and MySQL database. One DB can be accessed for ```read``` and ```write``` by multiple clients.
For storing shared files that the Web Servers will use, NFS is utilized and previousely created Logical Volume ```lv-apps``` is mounted to the folder where Apache stores files to be served to the users (/var/www).

This approach makes the Web server ```stateless``` which means they can be replaced when needed and data (in the database and on NFS) integrtity is preserved

In further steps, the following was done:
- Configured NFS (This step was done on all 3 servers)
- Deployed a tooling application to the Web Servers into a shared NFS folder
- Configured the Web Server to work with a single MySQL database

#### Web Server 1

__1.__ __Launch a new EC2 instance with RHEL Operating System__

<img width="800" alt="launch" src="https://github.com/user-attachments/assets/91db64ef-668b-47d4-8617-056badfd0bee" />

__2.__ __Install NFS Client__

```
sudo yum install nfs-utils nfs4-acl-tools -y
```
<img width="800" alt="nfs" src="https://github.com/user-attachments/assets/e49d352c-ab76-4071-a223-b66f2d0bc178" />

__3.__ __Mount ```/var/www/``` and target the NFS server's export for ```apps```__.
NFS Server private IP address = 172.31.8.75

```
sudo mkdir /var/www
sudo mount -t nfs -o rw,nosuid 172.31.8.75:/mnt/apps /var/www
```
Security group was blocking nfs traffic so more inbound rules were added, export was changed from ```172.31.0.0/20``` to ```172.31.0.0/16`` because my IP address was not in range of activity and mount directly to port=20048 was added

<img width="800" alt="inbound" src="https://github.com/user-attachments/assets/cde79213-a26c-42bb-a843-991e174e0446" />

<img width="800" alt="exports" src="https://github.com/user-attachments/assets/95f55926-7514-4175-8494-ee090de9e115" />

<img width="800" alt="mounted" src="https://github.com/user-attachments/assets/f9aab078-c336-40a3-b241-c2f5efba2222" />

<img width="800" alt="showmount" src="https://github.com/user-attachments/assets/10f28e8f-209b-47e4-9be4-a17569933fcf" />

__4.__ __Verify that NFS was mounted successfully by running ```df -h```. Ensure that the changes will persist after reboot.__

<img width="800" alt="df" src="https://github.com/user-attachments/assets/37a97bab-3084-4659-9ffe-f1b39aab11c7" />

```
sudo vi /etc/fstab
```
Add the following line

```
172.31.8.75:/mnt/apps /var/www nfs defaults 0 0
```
<img width="800" alt="uuid" src="https://github.com/user-attachments/assets/28d6bc62-3cc8-4d69-a5ba-0fad5f763ef2" />

__5.__ __Install Remi's repoeitory, Apache and PHP__

```
sudo yum install httpd -y
```
<img width="800" alt="Install" src="https://github.com/user-attachments/assets/438d7285-38ff-4181-8d12-521546f964d3" />

```
sudo dnf install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
```
<img width="800"  alt="epel" src="https://github.com/user-attachments/assets/9038d075-4d86-44b5-b7b9-aef9ce14b24a" />

```
sudo dnf install dnf-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm
```
<img width="800" alt="remi" src="https://github.com/user-attachments/assets/4d4d0123-4058-4641-a817-cae4650bd288" />

```
sudo dnf module reset php
```
<img width="800" alt="resetphp" src="https://github.com/user-attachments/assets/f78ac91b-ac4b-4db7-a339-7628e14d0c37" />

```
sudo dnf module enable php:remi-7.4
```
<img width="800" alt="phpremi" src="https://github.com/user-attachments/assets/13f36ec0-eda4-44a4-a064-118d884c5519" />

```
sudo dnf install php php-opcache php-gd php-curl php-mysqlnd
```
<img width="800" alt="in" src="https://github.com/user-attachments/assets/0c8d4395-e367-4fc6-8b68-5aa36a05db5a" />

```
sudo systemctl start php-fpm
sudo systemctl enable php-fpm

sudo setsebool -P httpd_execmem 1
sudo setsebool -P httpd_can_network_connect=1
sudo setsebool -P httpd_can_network_connect_db=1
```
<img width="800" alt="restart" src="https://github.com/user-attachments/assets/aef47b57-4e00-46fa-aaec-da37f7a9848c" />

### Web Server 2

__1.__ __Launch a new EC2 instance with RHEL Operating System__

<img width="800" alt="instance" src="https://github.com/user-attachments/assets/9dd56baa-d951-4175-bba7-48221c3152db" />

__2.__ __Install NFS Client__

```
sudo yum install nfs-utils nfs4-acl-tools -y
```
<img width="800" alt="yum" src="https://github.com/user-attachments/assets/3a09dc0e-7cd9-49d1-8b0b-b7a04fd3b5fb" />


__3.__ __Mount ```/var/www/``` and target the NFS server's export for ```apps```__.
NFS Server private IP address = 172.31.8.75

```
sudo dnf install -y nfs-utils
sudo mkdir /var/www
sudo mount -t nfs -o rw,nosuid 172.31.8.75:/mnt/apps /var/www
```
<img width="800" alt="dnf mount" src="https://github.com/user-attachments/assets/988cd079-5bb3-49e1-8744-ab1b56fb01c1" />

__4.__ __Verify that NFS was mounted successfully by running ```df -h```. Ensure that the changes will persist after reboot.__

<img width="800" alt="verified" src="https://github.com/user-attachments/assets/ad49782c-3532-4172-875c-341866f8cc71" />


```
sudo vi /etc/fstab
```
Add the following line

```
172.31.8.75:/mnt/apps /var/www nfs defaults 0 0
```
<img width="800" alt="fstab" src="https://github.com/user-attachments/assets/00099016-36f0-4f85-9556-24a64198cd58" />

__5.__ __Install Remi's repoeitory, Apache and PHP__

```
sudo yum install httpd -y
```
<img width="800" alt="httpd" src="https://github.com/user-attachments/assets/d9359afc-1f1f-4ad3-8449-2c656fad972c" />

```
sudo dnf install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
```
<img width="800" alt="epel" src="https://github.com/user-attachments/assets/289028c3-4b4a-4ae0-9746-14341ab2f157" />

```
sudo dnf install dnf-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm
```
<img width="800" alt="remi" src="https://github.com/user-attachments/assets/78bb99c8-08e3-436b-af6c-5cd1571a1e8e" />

```
sudo dnf module reset php
```
<img width="800" alt="reset" src="https://github.com/user-attachments/assets/006bb6d6-6c57-4691-b18e-c385cf37bb07" />

```
sudo dnf module enable php:remi-7.4
```
<img width="800" alt="php remi" src="https://github.com/user-attachments/assets/c0b2bf9d-bcc3-4eec-b61b-9043cf243480" />


```
swapon --show
sudo fallocate -l 2G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile

sudo dnf install php php-opcache php-gd php-curl php-mysqlnd
```
<img width="800" alt="php sql install" src="https://github.com/user-attachments/assets/52c8908f-55bf-4695-80b4-c39e02248f7a" />

```
sudo vi /etc/php-fpm.d/www.conf #remove ngix in listen.acls_user

sudo systemctl start php-fpm
sudo systemctl enable php-fpm

sudo setsebool -P httpd_execmem 1
sudo setsebool -P httpd_can_network_connect=1
sudo setsebool -P httpd_can_network_connect_db=1
```
<img width="800" height="100" alt="ctl" src="https://github.com/user-attachments/assets/faa7e335-5679-4efe-bbbb-821c5feeff0d" />


### Web Server 3

__1.__ __Launch a new EC2 instance with RHEL Operating System__

<img width="800" alt="instance" src="https://github.com/user-attachments/assets/cf678b6b-3fe3-4bd6-947d-ef85728d4828" />


__2.__ __Install NFS Client__

```
sudo yum install nfs-utils nfs4-acl-tools -y
```
<img width="800" alt="yum" src="https://github.com/user-attachments/assets/eca05213-819e-487c-adf6-935dfc5aaf21" />

__3.__ __Mount ```/var/www/``` and target the NFS server's export for ```apps```__.
NFS Server private IP address = 172.31.8.75

```
sudo dnf install -y nfs-utils

sudo mkdir /var/www
sudo mount -t nfs -o rw,nosuid 172.31.8.75:/mnt/apps /var/www
```

__4.__ __Verify that NFS was mounted successfully by running ```df -h```. Ensure that the changes will persist after reboot.__

<img width="800" alt="mount" src="https://github.com/user-attachments/assets/617d463f-371c-4411-98e7-e8077fdcb25e" />

```
sudo vi /etc/fstab
```
Add the following line

```
172.31.8.75:/mnt/apps /var/www nfs defaults 0 0
```
<img width="800" alt="uu" src="https://github.com/user-attachments/assets/8e180a82-7b4a-4885-938c-0136558dc18f" />


__5.__ __Install Remi's repoeitory, Apache and PHP__

```
sudo yum install httpd -y
```
<img width="800" alt="install" src="https://github.com/user-attachments/assets/1bf24521-54f4-4d6c-92a2-65161ce5284d" />

```
sudo dnf install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
```
<img width="800" alt="epel" src="https://github.com/user-attachments/assets/cb6c7054-3dc4-48f5-9e13-58ee11de30f9" />


```
sudo dnf install dnf-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm
```
<img width="800" alt="remi" src="https://github.com/user-attachments/assets/c25f075d-786c-42c7-8e9c-495f2026aa16" />

```
sudo dnf module reset php
```
<img width="800" alt="reset" src="https://github.com/user-attachments/assets/989894dd-7ef7-47b6-b720-93c66e2d7a1d" />

```
sudo dnf module enable php:remi-7.4
```
<img width="800" alt="image" src="https://github.com/user-attachments/assets/c80a3cff-1808-43b8-9315-57bd9813f444" />

```
swapon --show
sudo fallocate -l 2G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile

sudo dnf install php php-opcache php-gd php-curl php-mysqlnd
```
<img width="800"  alt="json" src="https://github.com/user-attachments/assets/cb370e1b-243e-4e4f-bb9e-44357a9ebaa8" />

```
sudo vi /etc/php-fpm.d/www.conf #remove ngix in listen.acls_user

sudo systemctl start php-fpm
sudo systemctl enable php-fpm

sudo setsebool -P httpd_execmem 1
sudo setsebool -P httpd_can_network_connect=1
sudo setsebool -P httpd_can_network_connect_db=1
```
<img width="800" alt="restart" src="https://github.com/user-attachments/assets/c6fdd4be-edd5-4864-8da1-1ce3ed5bee7f" />

__6.__ __Verify that Apache files and directories are availabel on the Web Servers in ```/var/www``` and also on the NFS Server in ```/mnt/apps```. If the same files are present in both, it means NFS was mounted correctly.__
test.txt file was created from Web Server 1, and it was accessible from Web Server 2.

```
cd /var/www
sudo touch placeholder.txt
ls -l
```
<img width="800" alt="placeholder" src="https://github.com/user-attachments/assets/326417c3-8b9e-42a5-ad81-131894b6d4d7" />

```
ls -l /mnt/apps
```
<img width="800" alt="found" src="https://github.com/user-attachments/assets/cd7e5953-d074-40e9-aca2-51ad65da39d9" />

__7.__ __Locate the log folder for Apache on the Web Server and mount it to NFS server's export for logs. Repeat ```step 4``` to ensure the mount point persists after reboot__.

```
sudo vi /etc/fstab
```

Add the following line

```
172-31-8-75 :/mnt/logs /var/log/httpd nfs defaults 0 0
```
<img width="800" alt="added" src="https://github.com/user-attachments/assets/70bf2f12-4e5e-4ce3-abae-2d11eb7604cf" />

__8.__ __Fork the tooling source code from ```StegHub GitHub Account```__

<img width="800" alt="tooling" src="https://github.com/user-attachments/assets/8a392518-c888-46ab-b493-fd925edf6f4f" />

__9.__ __Deploy the tooling Website's code to the Web Server. Ensure that the ```html``` folder from the repository is deplyed to ```/var/www/html```__

#### Install Git
```
sudo yum install git
```
<img width="800" alt="git" src="https://github.com/user-attachments/assets/63edad31-2e02-4837-8a65-3a56e06bce22" />

#### Initialize the directory and clone the tooling repository

Ensure to clone the forked repository

<img width="800"  alt="clone" src="https://github.com/user-attachments/assets/db6b9bf8-6aa3-4fa9-883d-8ffe081d194c" />

__Note__:
Acces the website on a browser

- Ensure TCP port 80 is open on the Web Server.
- If ```403 Error``` occur, check permissions to the ```/var/www/html``` folder and also disable ```SELinux```
```
sudo setenforce 0
```
To make the change permanent, open selinux file and set selinux to disable.

```
sudo vi /etc/sysconfig/selinux

SELINUX=disabled

sudo systemctl restart httpd
```
<img width="800" alt="selinux" src="https://github.com/user-attachments/assets/40790a57-a614-4e43-b915-797fd4b96dbd" />

<img width="800" alt="tooling cd" src="https://github.com/user-attachments/assets/2f1d44c8-b117-40e0-8674-80cfcf56ec8b" />

__10.__ __Update the website's configuration to connect to the database (in ```/var/www/html/function.php``` file). Apply ```tooling-db.sql``` command__

```
sudo dnf install -y mysql
sudo mysql -h <db-private-IP> -u <db-username> -p <db-password < tooling-db.sql```

```
<img width="800" alt="tooling repo" src="https://github.com/user-attachments/assets/6ab3e6e4-492d-4102-9513-d623b279f0b6" />

```
sudo vi /var/www/html/functions.php
```
<img width="800" alt="db config" src="https://github.com/user-attachments/assets/ede34f5d-6996-4dca-8b62-d80978f945b6" />

```
sudo mysql -h 172.31.38.171 -u webaccess -p tooling 
```
<img width="800" alt="tables" src="https://github.com/user-attachments/assets/13ce7439-c9c5-4cf4-a65b-130464f94b66" />

__11.__ __Create in MyQSL a new admin user with username: ```myuser``` and password: ```password```__

```
INSERT INTO users(id, username, password, email, user_type, status) VALUES (2, 'myuser', '5f4dcc3b5aa765d61d8327deb882cf99', 'user@mail.com', 'admin', '1');
```
<img width="800" alt="insert" src="https://github.com/user-attachments/assets/92cf238e-5c90-4575-9843-4f5062298195" />

__12.__ __Open a browser and access the website using the Web Server public IP address ```http://<Web-Server-public-IP-address>/index.php```. Ensure login into the website with ```myuser``` user.__

#### From Web Server 1

<img width="800" alt="webserver1" src="https://github.com/user-attachments/assets/4d305f1c-a7ff-453d-875e-da5a6ca4b2b9" />

<img width="800" alt="webserver 1 login" src="https://github.com/user-attachments/assets/60a25fdd-334c-4b6c-9a86-f93d5788d980" />

### From Web Server 2

<img width="800" alt="webserver 2 login" src="https://github.com/user-attachments/assets/6a1df01a-c4ca-4911-97cd-0313d5adfab5" />

<img width="800" alt="interface" src="https://github.com/user-attachments/assets/7d7bdfba-7f66-46be-99ab-e9e6805f96e1" />
