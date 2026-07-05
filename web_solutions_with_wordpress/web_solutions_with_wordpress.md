# Web Solution With WordPress

## Step 1 - Prepare a Web Server

__1.__ __Launch a RedHat EC2 instance that serve as ```Web Server```. Create 3 volumes in the same AZ as the web server ec2 each of 10GB and attache all 3 volumes one by one to the web server__.

<img width="900" alt="create instance" src="https://github.com/user-attachments/assets/ab5c7047-2239-4acc-925d-f09c67735e51" />

<img width="900" alt="instance created" src="https://github.com/user-attachments/assets/27ab0643-b927-45b3-a29b-705b46bf5b86" />

<img width="900" alt="rules" src="https://github.com/user-attachments/assets/fcc441db-1161-43d2-a3d1-92f36535c483" />

<img width="900" alt="volume" src="https://github.com/user-attachments/assets/65a01496-c976-4848-a391-5d93e93dad1b" />

__2.__ __Open up the Linux terminal to begin configuration__.

```
ssh -i "test.pem" ec2-user@51.20.253.207
```
<img width="900" alt="connect instance" src="https://github.com/user-attachments/assets/d495c98e-8306-44c0-a738-9e51947a808c" />

__3.__ __Use ```lsblk``` to inspect what block devices are attached to the server. All devices in Linux reside in /dev/ directory. Inspect with ```ls /dev/``` and ensure all 3 newly created devices are there. Their name will likely be ```nvme1n1```, ```nvme2n1``` and ```nvme3n1```__.

```
lsblk
```
<img width="900" alt="block" src="https://github.com/user-attachments/assets/984f2c9d-7522-4cf2-971e-802b0646fa85" />

__4.__ __Use ```df -h``` to see all mounts and free space on the server__.

```
df -h
```
<img width="900" alt="df" src="https://github.com/user-attachments/assets/36ef8bc7-915b-490e-9273-4e8f3cb30833" />

__5a.__ __Use ```fdisk``` utility to create a single partition on each of the 3 disks__.

```
sudo fdisk /dev/nvme1n1
```
<img width="900" alt="fdisk" src="https://github.com/user-attachments/assets/e8444042-cc67-428e-b90e-b76bfa2249c0" />

```
sudo fdisk /dev/nvme2n1
```
<img width="900" alt="disk2" src="https://github.com/user-attachments/assets/01135054-965b-4e9a-a748-9fd9ce1c0138" />

```
sudo fdisk /dev/nvme3n1
```
<img width="900" alt="disk3" src="https://github.com/user-attachments/assets/79ca229d-486b-4ce9-96c2-9b67f2ba9bae" />

__5b.__ __Use ```lsblk``` utility to view the newly configured partitions on each of the 3 disks__

```
lsblk
```
<img width="900" alt="tables" src="https://github.com/user-attachments/assets/b272a58a-d2f1-4813-bf71-88eafef4f8ef" />

__6.__ __Install ```lvm``` package__

```
sudo yum install lvm2 -y
```
lvm is alreadly installed

<img width="900" alt="lvm installed" src="https://github.com/user-attachments/assets/e1ede566-b8a7-4b73-9796-0261f38cc889" />

__7.__ __Use ```pvcreate``` utility to mark each disks as physical volumes (PVs) to be used by LVM. Verify that each of the volumes have been created successfully__.

```
sudo pvcreate /dev/nvme1n1p1 /dev/nvme2n1p1 /dev/nvme3n1p1

sudo pvs
```
<img width="900" alt="pv" src="https://github.com/user-attachments/assets/d78f7b41-6e2d-422d-a4e1-bf1e129fd07b" />

__8.__ __Use ```vgcreate``` utility to add all 3 PVs to a volume group (VG). Name the VG ```vg_data```. Verify that the VG has been created successfully__

```
sudo vgcreate vg_data /dev/nvme1n1p1 /dev/nvme2n1p1 /dev/nvme3n1p1

sudo vgs
```
<img width="900" alt="vg" src="https://github.com/user-attachments/assets/8d7ef6e1-9aa1-4276-8b93-288d569bb1b7" />

__9.__ __Use ```lvcreate``` utility to create 2 logical volume, ```apps-lv``` (__Use half of the PV size__), and ```logs-lv``` (__Use the remaining space of the PV size__). Verify that the logical volumes have been created successfully__.

__Note__: apps-lv is used to store data for the Website while logs-lv is used to store data for logs.

```
sudo lvcreate -n apps-lv -L 14G vg_data

sudo lvcreate -n logs-lv -L 14G vg_data

sudo lvs
```

<img width="900" alt="vg data" src="https://github.com/user-attachments/assets/5453cb88-21f4-4d0f-a213-06c729fb71a6" />

__10a.__ __Verify the entire setup__

```
sudo vgdisplay -v   #view complete setup, VG, PV and LV
```
<img width="900"  alt="display" src="https://github.com/user-attachments/assets/e34df547-5b0e-4ef5-84db-7e0076b15d8b" />

```
sudo lsblk
```
<img width="900" alt="ls" src="https://github.com/user-attachments/assets/f999dd30-0660-4f42-bc79-648ce3d515f4" />

__10b.__ __Use ```mkfs.ext4``` to format the logical volumes with ext4 filesystem__

```
sudo mkfs.ext4 /dev/vg_data/apps-lv

sudo mkfs.ext4 /dev/vg_data/logs-lv
```
<img width="900" alt="mk" src="https://github.com/user-attachments/assets/5731b49d-6a4e-44f2-b09d-7a7a0e2f348c" />

__11.__ __Create ```/var/www/html``` directory to store website files and ```/home/recovery/logs``` to store backup of log data__

```
sudo mkdir -p /var/www/html

sudo mkdir -p /home/recovery/logs
```
#### Mount /var/www/html on apps-lv logical volume

```
sudo mount /dev/vg_data/apps-lv /var/www/html
```
<img width="900" alt="mount" src="https://github.com/user-attachments/assets/23d0be7a-8582-4f0d-9100-068e9dec0114" />

__12.__ __Use ```rsync``` utility to backup all the files in the log directory ```/var/log``` into ```/home/recovery/logs``` (This is required before mounting the file system)__

```
sudo rsync -av /var/log /home/recovery/logs
```
<img width="900" alt="log" src="https://github.com/user-attachments/assets/c7eb0c15-a3a1-4073-8618-cbdbff7d1d02" />

__13.__ __Mount ```/var/log``` on ```logs-lv``` logical volume (All existing data on /var/log is deleted with this mount process which was why the data was backed up)__

```
sudo mount /dev/vg_data/logs-lv /var/log
sudo ls -l /var/log
```
<img width="900" alt="mount dev" src="https://github.com/user-attachments/assets/8d70c119-1ffc-4d12-9e88-cadd3a376f4b" />

__14.__ __Restore log file back into ```/var/log``` directory__

```
sudo rsync -av /home/recovery/logs/log/ /var/log
```
<img width="900" alt="restore" src="https://github.com/user-attachments/assets/54d19277-4e2b-4ae3-8f8e-29ba49de413c" />

__15.__ __Update ```/etc/fstab``` file so that the mount configuration will persist after restart of the server__

#### Get the ```UUID``` of the device and Update the ```/etc/fstab``` file with the format shown inside the file using the ```UUID```. Remember to remove the leading and ending quotes.

```
sudo blkid   # To fetch the UUID

sudo vi /etc/fstab
```
<img width="900" alt="uuid" src="https://github.com/user-attachments/assets/0b8f4b11-a102-4e3b-bc39-2ea3de0d35ae" />

<img width="900" alt="final" src="https://github.com/user-attachments/assets/10b37042-e5c9-49bf-a4cc-edc53d7a2c0f" />

__16.__ __Test the configuration and reload daemon. Verify the setup__

```
sudo mount -a   # Test the configuration

sudo systemctl daemon-reload

df -h   # Verifies the setup
```
<img width="900" alt="daemon" src="https://github.com/user-attachments/assets/5d316ba9-8a77-4728-bf61-7db470e2a2e4" />

## Step 2 - Prepare the Database Server

### Launch a second RedHat EC2 instance that will have a role - ```DB Server```. Repeat the same steps as for the Web Server, but instead of ```apps-lv```, create ```dv-lv``` and mount it to ```/db``` directory.

__1.__ __Create 3 volumes in the same AZ as the ```DB Server``` ec2 each of 10GB and attache all 3 volumes one by one to the DB Server__.

<img width="900" alt="create instance" src="https://github.com/user-attachments/assets/c5f523ab-cda7-4872-9a5e-5a4096c4293d" />

<img width="900" alt="instance created" src="https://github.com/user-attachments/assets/a49e9741-43d3-4b5b-8106-535e127c004d" />

<img width="900" alt="rules" src="https://github.com/user-attachments/assets/1f2fad11-f9ab-4139-a55f-2dbde651f2bd" />

<img width="900" alt="volume" src="https://github.com/user-attachments/assets/23b04fab-e2f1-4321-be49-69cb0b28cafd" />

__2.__ __Open up the Linux terminal to begin configuration__.

```
ssh -i "test.pem" ec2-user@16.171.0.181
```
<img width="900" alt="instance connected" src="https://github.com/user-attachments/assets/31a3b134-05cc-4c0b-8a9e-bc24dc3d5150" />

__3.__ __Use ```lsblk``` to inspect what block devices are attached to the server. Their name will likely be ```nvme1n1```, ```nvme2n1``` and ```nvme3n1```__.

```
lsblk
```
<img width="900" alt="lbs" src="https://github.com/user-attachments/assets/c9cfabf9-4493-41a6-a4d8-ec471e0f5dc3" />

__4a.__ __Use ```fdisk``` utility to create a single partition on each of the 3 disks__.

```
sudo fdisk /dev/nvme1n1
```
<img width="900" alt="fdisk" src="https://github.com/user-attachments/assets/2e55ca82-e309-4a49-af1c-8e3122dcd58f" />

```
sudo fdisk /dev/nvme2n1
```
<img width="900" alt="disk 2" src="https://github.com/user-attachments/assets/ebc99abe-3666-46fb-a64c-0dd2523309da" />

```
sudo fdisk /dev/nvme3n1
```
<img width="900" alt="disk 3" src="https://github.com/user-attachments/assets/b01ebf02-a260-4dc2-bcb7-e68377d57dc1" />

__4b.__ __Use ```lsblk``` utility to view the newly configured partitions on each of the 3 disks__.

```
lsblk
```
<img width="900" alt="partition" src="https://github.com/user-attachments/assets/8f5f8a91-0361-4c8f-aac2-d5378f79dcfd" />

__5.__ __Install ```lvm``` package__

```
sudo yum install lvm2 -y
```
Already installed.

<img width="900" alt="downloaded" src="https://github.com/user-attachments/assets/64fe433f-3062-4a69-aad9-34c5f489889a" />

__6.__ __Use ```pvcreate``` utility to mark each disks as physical volumes (PVs) to be used by LVM. Verify that each of the volumes have been created successfully__.

```
sudo pvcreate /dev/nvme1n1p1 /dev/nvme2n1p1 /dev/nvme3n1p1

sudo pvs
```
<img width="900" alt="pv" src="https://github.com/user-attachments/assets/7b2f1a7e-0da1-47f5-81eb-cd4bed63da2a" />

__7.__ __Use ```vgcreate``` utility to add all 3 PVs to a volume group (VG). Name the VG ```vg_data```. Verify that the VG has been created successfully__

```
sudo vgcreate vg_data /dev/nvme1n1p1 /dev/nvme2n1p1 /dev/nvme3n1p1

sudo vgs
```
<img width="900" alt="vg" src="https://github.com/user-attachments/assets/f4712288-1e34-40c3-bbc0-55e705d2dbec" />

__8.__ __Use ```lvcreate``` utility to create a logical volume, ```db-lv``` (__Use 20G of the PV size since it is the only LV to be created__). Verify that the logical volumes have been created successfully__.

```
sudo lvcreate -n db-lv -L 20G vg_data

sudo lvs
```
<img width="900" alt="data" src="https://github.com/user-attachments/assets/a6c0a283-5d4a-41b9-9f7c-eeb51386e9d4" />

__9.__ __Use ```mkfs.ext4``` to format the logical volumes with ext4 filesystem and monut ```/db``` on ```db-lv```__

```
sudo mkfs.ext4 /dev/vg_data/db-lv
```
```
sudo mkdir -p /db

sudo mount /dev/vg_data/db-lv /db
```
<img width="900" alt="mke" src="https://github.com/user-attachments/assets/499f2542-b05d-4cd0-83a0-3f077b995321" />

__10.__ __Update ```/etc/fstab``` file so that the mount configuration will persist after restart of the server__

#### Get the ```UUID``` of the device

```
sudo blkid
```
<img width="900"  alt="uuid" src="https://github.com/user-attachments/assets/d8a9bfd6-1733-48c0-900a-1cb2cf0dcefb" />

#### Update the ```/etc/fstab``` file with the format shown inside the file using the ```UUID```. Remember to remove the leading and ending quotes.

```
sudo vi /etc/fstab
```
<img width="900" alt="uuid db" src="https://github.com/user-attachments/assets/77a15de8-a89c-4923-9437-9a45d1888521" />

__11.__ __Test the configuration and reload daemon. Verify the setup__

```
sudo mount -a   # Test the configuration

sudo systemctl daemon-reload

df -h   # Verifies the setup
```
<img width="900" alt="mount" src="https://github.com/user-attachments/assets/32a6dcb9-7083-4f60-b61d-4ad68a895bd3" />

## Step 3 - Install WordPress on the Web Server EC2

__1.__ __Update the repository__

```
sudo yum -y update
```

__2.__ __Install wget, Apache and it's dependencies__

```
sudo yum install -y wget httpd php-fpm php-json
sudo yum -y install wget httpd php php-mysqlnd php-fpm php-json
```
<img width="900" alt="php" src="https://github.com/user-attachments/assets/5f80af15-6d03-462d-a922-ec4f21e2f492" />

<img width="900"  alt="sql" src="https://github.com/user-attachments/assets/a24c8f99-1fe5-445a-a879-abc0a5b40874" />

__3.__ __Start Apache__

```
sudo dnf install -y httpd

sudo systemctl enable --now httpd
```
<img width="955" height="45" alt="enable httpd" src="https://github.com/user-attachments/assets/5c0a2fcc-73c6-4e29-8d13-c489e89b904e" />

__4.__ __Install PHP and its dependencies__

```
sudo yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm

sudo yum install yum-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm

sudo yum module list php

sudo yum module reset php

sudo yum module enable php:remi-7.4

sudo yum install php php-opcache php-gd php-curl php-mysqlnd

sudo systemctl start php-fpm

sudo systemctl enable php-fpm

sudo setsebool -P httpd_execmem 1
```
<img width="900" alt="image" src="https://github.com/user-attachments/assets/613f6116-9721-468c-a934-f005945191be" />

<img width="900" alt="active" src="https://github.com/user-attachments/assets/95c5f05b-a3f8-4f43-861d-1c62bf4a2eb3" />

__Confirm php works in browser__

```
sudo dnf install -y redhat-indexhtml
```
<img width="900" alt="install redhat" src="https://github.com/user-attachments/assets/4f9a1c88-b0a2-43d4-8470-5f4380fddab4" />

__Restart Apache__
```
sudo systemctl restart httpd
```
<img width="900" alt="redhat" src="https://github.com/user-attachments/assets/ce86978b-fa03-4805-a09e-44820a8af396" />

__5.__ __Download WordPress__

Download wordpress and copy wordpress content to /var/www/html

```
/var/www/html

sudo mkdir wordpress && cd wordpress

sudo wget http://wordpress.org/latest.tar.gz

sudo tar xzvf latest.tar.gz   # Extract wordpress
```
<img width="900" alt="wordpress" src="https://github.com/user-attachments/assets/ca4c9450-0e66-4af6-b114-ed947e6e25f2" />

#### After extraction, ```cd``` into the extracted ```wordpress``` and ```Copy``` the content of ```wp-config-sample.php``` to ```wp-config.php```.

This will copy and create the file wp-config.php

```
 cd wordpress/

sudo cp -R wp-config-sample.php wp-config.php

ls -l
```
<img width="900" alt="ls l" src="https://github.com/user-attachments/assets/6a172a81-8349-4db1-b419-155182f34f54" />

#### Exit from the extracted ```wordpress```. Copy the content of the extracted ```wordpress``` to ```/var/www/html```.

```
cd ..

sudo cp -R wordpress/. /var/www/html/
```
__4.__ __Configure SELinux Policies__

To instruct SELinux to allow Apache to execute the PHP code via PHP-FPM run.

```
sudo chown -R apache:apache /var/www/html

sudo chcon -t httpd_sys_rw_content_t /var/www/html -R

sudo setsebool -P httpd_execmem 1

sudo setsebool -P httpd_can_network_connect=1

sudo setsebool -P httpd_can_network_connect_db=1

```

__5.__ __Install MySQL on DB Server EC2__

#### Update the EC2

```
sudo yum update -y
```
<img width="900" alt="yum" src="https://github.com/user-attachments/assets/af6c1c7f-fcb4-4de1-9233-3919e3a14912" />

#### Install MySQL Server

```
sudo dnf install -y mariadb-server
```
<img width="900" alt="maria" src="https://github.com/user-attachments/assets/7a774580-8fd5-467b-ad0a-25f64108817b" />

#### Verify that the service is up and running. If it is not running, restart the service and enable it so it will be running even after reboot.

```
sudo systemctl enable --now mariadb

sudo systemctl status mariadb
```
<img width="900" alt="maria status" src="https://github.com/user-attachments/assets/7dd8745b-4bf7-4632-bed7-c8af46b8d4f0" />


__6.__ __Configure DB to work with WordPress__

#### Run mysql secure script

```
sudo mysql_secure_installation
```
<img width="900" alt="secure" src="https://github.com/user-attachments/assets/4cfb2428-87a9-44ef-b7dc-4c8592026aa0" />

#### Create database

The user "wordpress" will be connecting to the database using the Web Server __private IP address__

```
sudo mysql -u root -p

CREATE DATABASE wordpress;
CREATE USER 'wordpress'@'172.31.22.65' IDENTIFIED BY 'StrongPassword123';
GRANT ALL PRIVILEGES ON wordpress.* TO 'wordpress'@'172.31.22.65';
FLUSH PRIVILEGES;
SHOW DATABASES;
exit

```
<img width="900" alt="database" src="https://github.com/user-attachments/assets/790c28d1-1838-4de0-ada4-00581e105fe9" />

__7.__ __Configure WordPress to connect to remote database__

#### Open MySQL port 3306 on the DB Server EC2.
For extra security, access to the DB Server is allowed only from the Web Server IP address. In the inbound rule, /32 is configured as source.

<img width="900" alt="connected to web server" src="https://github.com/user-attachments/assets/25b0393e-2288-4da6-ba8b-ccb537e0c2f9" />

#### Install mysql server on the Web Server EC2.

WordPress has its own database, therefore it needs a database server to store it's information such as: Username, Email, Passwords, First name and Last name of the users on the wordpress website on a database.

```
sudo dnf install -y mariadb-server
```
<img width="900" alt="maria db" src="https://github.com/user-attachments/assets/be43f787-6b4b-4f7f-a014-c570b9256e87" />

```
sudo systemctl enable --now mariadb

sudo systemctl status mariadb
```
<img width="900" alt="maria enable" src="https://github.com/user-attachments/assets/280ec346-cfa3-4d73-a05a-b2f672bc1a27" />

#### Open ```wp-config.php``` file and edit the database information

```
cd /var/www/html

sudo vi wp-config.php

sudo systemctl restart httpd
```
<img width="900" alt="wpconfig" src="https://github.com/user-attachments/assets/c8bf9001-a98d-4a5b-a3be-ad05e1485ce7" />

#### Connect to the DB Server from the Web Server

```
mysql -h 172.31.42.131 -u wordpress -p

SHOW DATABASES;

exit;
```
<img width="900" alt="connected" src="https://github.com/user-attachments/assets/a1cc4cf1-f693-4f08-9ddd-ec5d207048eb" />

#### Access the web page again with the Web Server public IP address and install wordpress on the browser

<img width="900" alt="php web" src="https://github.com/user-attachments/assets/30f8915c-0ef4-4c44-ac3a-dbd261e39776" />

<img width="900" alt="login" src="https://github.com/user-attachments/assets/85d56640-b07d-4e6a-8150-e7ce481274e8" />

<img width="900" alt="site" src="https://github.com/user-attachments/assets/44b1fb48-08f7-4ba5-9d67-36a1b6e0b38e" />

## At this point, the implementation of this project is complete and WordPress is available to be used.





